# Migrating from version 1 to version 3 for OTA applications<a name="porting-migration-ota"></a>

This guide will help you migrate your application from OTA library version 1 to version 3\. 

**Note**  
The OTA version 2 APIs are the same as OTA v3 APIs, so if your application is using version 2 of the APIs then changes are not required for API calls but we recommend that you integrate version 3 of the library\. 

Demos for OTA version 3 are available here:
+ [ ota\_demo\_core\_mqtt](https://github.com/aws/amazon-freertos/tree/main/demos/ota/ota_demo_core_mqtt)\.
+ [ ota\_demo\_core\_http](https://github.com/aws/amazon-freertos/tree/main/demos/ota/ota_demo_core_http)\.
+ [ ota\_ble](https://github.com/aws/amazon-freertos/tree/main/demos/ble/ota_ble)\.

## Summary of API changes<a name="porting-migration-ota-summary"></a>


**Summary of API changes between OTA Library version 1 and version 3**  

|  OTA version 1 API  |  OTA version 3 API  |  Description of changes  | 
| --- | --- | --- | 
|  OTA\_AgentInit  |  OTA\_Init  |  The input paramerts are changed as well as the value returned from the function due to changes in the implementation in OTA v3\. Please refer to the section for OTA\_Init below for details\.  | 
|  OTA\_AgentShutdown  |  OTA\_Shutdown  |  Change in the input parameters including an additional parameter for an optional unsubscribe from MQTT topics\. Please refer to the section for OTA\_Shutdown below for details\.  | 
|  OTA\_GetAgentState  |  OTA\_GetState  |  The API name is changed with no changes to the input parameter\. The return value is the same but the enum and members are renamed\. Please refer to the section for OTA\_GetState below for details\.   | 
|  n/a  |  OTA\_GetStatistics  |  New API added that replaces the APIs OTA\_GetPacketsReceived, OTA\_GetPacketsQueued, OTA\_GetPacketsProcessed, OTA\_GetPacketsDropped\. Please refer to the section for OTA\_GetStatistics below for details\.  | 
|  OTA\_GetPacketsReceived  |  n/a  |  This API is removed from version 3 and replaced by OTA\_GetStatistics\.  | 
|  OTA\_GetPacketsQueued  |  n/a  |  This API is removed from version 3 and replaced by OTA\_GetStatistics\.  | 
|  OTA\_GetPacketsProcessed  |  n/a  |  This API is removed from version 3 and replaced by OTA\_GetStatistics\.  | 
|  OTA\_GetPacketsDropped  |  n/a  |  This API is removed from version 3 and replaced by OTA\_GetStatistics\.  | 
|  OTA\_ActivateNewImage  |  OTA\_ActivateNewImage  |  The input parameters are the same but the return OTA error code is renamed and new error codes are added in version 3 of the OTA library\. Please see the section for OTA\_ActivateNewImage for details\.  | 
|  OTA\_SetImageState  |  OTA\_SetImageState  |  The input parameters are the same and renamed, the return OTA error code is renamed and new error codes are added in version 3 of the OTA library\. Please see the section for OTA\_SetImageState for details\.  | 
|  OTA\_GetImageState  |  OTA\_GetImageState  |  The input parameters are the same, the return enum is renamed in version 3 of the OTA library\. Please see the section for OTA\_GetImageState for details\.  | 
|  OTA\_Suspend  |  OTA\_Suspend  |  The input parameters are the same, the return OTA error code is renamed and new error codes are added in version 3 of the OTA library\. Please see the section for OTA\_Suspend for details\.  | 
|  OTA\_Resume  |  OTA\_Resume  |  The input parameter for connection is removed as the connection is handled in the OTA demo/application, the return OTA error code is renamed and new error codes are added in version 3 of the OTA library\. Please see the section for OTA\_Resume for details\.  | 
|  OTA\_CheckForUpdate  |  OTA\_CheckForUpdate  |  The input parameters are the same, the return OTA error code is renamed and new error codes are added in version 3 of the OTA library\. Please see the section for OTA\_CheckForUpdate for details\.  | 
|  n/a  |  OTA\_EventProcessingTask  |  New API added and it is the main event loop to handle events for OTA update and must be called by the application task\. Please see the section for OTA\_EventProcessingTask for details\.  | 
|  n/a  |  OTA\_SignalEvent  |  New API added and it adds the event to the back of OTA event queue and is used by internal OTA modules to signal the agent task\. Please see the section for OTA\_SignalEvent for details\.  | 
|  n/a  |  OTA\_Err\_strerror  |  New API for error code to string conversion for OTA errors\.  | 
|  n/a  |  OTA\_JobParse\_strerror  |  New API for error code to string conversion for Job Parsing errors\.  | 
|  n/a  |  OTA\_OsStatus\_strerror  |  New API for status code to string conversion for OTA OS port status\.  | 
|  n/a  |  OTA\_PalStatus\_strerror  |  New API for status code to string conversion for OTA PAL port status\.  | 

## Description of changes required<a name="porting-migration-ota-description"></a>

### OTA\_Init<a name="porting-migration-ota-init"></a>

When initializing the OTA Agent in v1 the `OTA_AgentInit` API is used which takes parameters for connection context, thing name, complete callback and timeout as input\.

```
OTA_State_t OTA_AgentInit( void * pvConnectionContext,
                           const uint8_t * pucThingName,
                           pxOTACompleteCallback_t xFunc,
                           TickType_t xTicksToWait );
```

This API is now changed to `OTA_Init` with parameters for the buffers required for ota, ota interfaces, thing name and application callback\. 

```
OtaErr_t OTA_Init( OtaAppBuffer_t * pOtaBuffer,
                   OtaInterfaces_t * pOtaInterfaces,
                   const uint8_t * pThingName,
                   OtaAppCallback OtaAppCallback );
```

**Removed input parameters \- **    
**pvConnectionContext \- **  
The connection context is removed because the OTA Library Version 3 does not require the connection context to be passed to it and the MQTT/HTTP operations are handled by their respective interfaces in the OTA demo/application\.   
**xTicksToWait \- **  
The ticks to wait parameter is also removed as the task is created in the OTA demo/application before calling OTA\_Init\. 

**Renamed input parameters \-**    
**xFunc \- **  
The parameter is renamed to OtaAppCallback and its type is changed to OtaAppCallback\_t\.

**New input parameters \- **    
**pOtaBuffer**  
The application must allocate the buffers and pass them to the OTA library using the OtaAppBuffer\_t structure during initialization\. The buffers required differ slightly depending on the protocol used for downloading the file\. For the MQTT protocol the buffers for stream name are required and for the HTTP protocol the buffers for pre\-signed url and authorization scheme are required\.  
Buffers required when using MQTT for file download \-  

```
static OtaAppBuffer_t otaBuffer =
{
    .pUpdateFilePath    = updateFilePath,
    .updateFilePathsize = otaexampleMAX_FILE_PATH_SIZE,
    .pCertFilePath      = certFilePath,
    .certFilePathSize   = otaexampleMAX_FILE_PATH_SIZE,
    .pStreamName        = streamName,
    .streamNameSize     = otaexampleMAX_STREAM_NAME_SIZE,
    .pDecodeMemory      = decodeMem,
    .decodeMemorySize   = ( 1U << otaconfigLOG2_FILE_BLOCK_SIZE ),
    .pFileBitmap        = bitmap,
    .fileBitmapSize     = OTA_MAX_BLOCK_BITMAP_SIZE
};
```
Buffers required when using HTTP for file download \-  

```
static OtaAppBuffer_t otaBuffer =
{
    .pUpdateFilePath    = updateFilePath,
    .updateFilePathsize = otaexampleMAX_FILE_PATH_SIZE,
    .pCertFilePath      = certFilePath,
    .certFilePathSize   = otaexampleMAX_FILE_PATH_SIZE,
    .pDecodeMemory      = decodeMem,
    .decodeMemorySize   = ( 1U << otaconfigLOG2_FILE_BLOCK_SIZE ),
    .pFileBitmap        = bitmap,
    .fileBitmapSize     = OTA_MAX_BLOCK_BITMAP_SIZE,
    .pUrl               = updateUrl,
    .urlSize            = OTA_MAX_URL_SIZE,
    .pAuthScheme        = authScheme,
    .authSchemeSize     = OTA_MAX_AUTH_SCHEME_SIZE
};
```
Where \-   

```
    pUpdateFilePath    Path to store the files. 
    updateFilePathsize Maximum size of the file path.
    pCertFilePath      Path to certificate file. 
    certFilePathSize   Maximum size of the certificate file path.
    pStreamName        Name of stream to download the files.
    streamNameSize     Maximum size of the stream name.
    pDecodeMemory      Place to store the decoded files.
    decodeMemorySize   Maximum size of the decoded files buffer.
    pFileBitmap        Bitmap of the parameters received.
    fileBitmapSize     Maximum size of the bitmap.
    pUrl               Presigned url to download files from S3.
    urlSize            Maximum size of the URL.
    pAuthScheme        Authentication scheme used to validate download.
    authSchemeSize     Maximum size of the auth scheme.
```  
**pOtaInterfaces **  
The second input parameter to OTA\_Init is a reference to the OTA interfaces for type OtaInterfaces\_t\. This set of interfaces must be passed to the OTA Library and includes in the operating system interface the MQTT interface, HTTP interface and platform abstraction layer interface\.     
**OTA OS Interface **  
The OTA OS Functional interface is a set of APIs that must be implemented for the device to use the OTA library\. The function implementations for this interface are provided to the OTA library in the user application\. The OTA library calls the function implementations to perform functionalities that are typically provided by an operating system\. This includes managing events, timers, and memory allocation\. The implementations for FreeRTOS and POSIX are provided with the OTA library\.  
Example for FreeRTOS using the provided FreeRTOS port \-  

```
    OtaInterfaces_t otaInterfaces;
    otaInterfaces.os.event.init   = OtaInitEvent_FreeRTOS;
    otaInterfaces.os.event.send   = OtaSendEvent_FreeRTOS;
    otaInterfaces.os.event.recv   = OtaReceiveEvent_FreeRTOS;
    otaInterfaces.os.event.deinit = OtaDeinitEvent_FreeRTOS;
    otaInterfaces.os.timer.start  = OtaStartTimer_FreeRTOS;
    otaInterfaces.os.timer.stop   = OtaStopTimer_FreeRTOS;
    otaInterfaces.os.timer.delete = OtaDeleteTimer_FreeRTOS;
    otaInterfaces.os.mem.malloc   = Malloc_FreeRTOS;
    otaInterfaces.os.mem.free     = Free_FreeRTOS;
```
Example for Linux using the provided POSIX port \-  

```
  OtaInterfaces_t otaInterfaces;
  otaInterfaces.os.event.init    = Posix_OtaInitEvent;
  otaInterfaces.os.event.send    = Posix_OtaSendEvent;
  otaInterfaces.os.event.recv    = Posix_OtaReceiveEvent;
  otaInterfaces.os.event.deinit  = Posix_OtaDeinitEvent;
  otaInterfaces.os.timer.start   = Posix_OtaStartTimer;
  otaInterfaces.os.timer.stop    = Posix_OtaStopTimer;
  otaInterfaces.os.timer.delete  = Posix_OtaDeleteTimer;
  otaInterfaces.os.mem.malloc    = STDC_Malloc;
  otaInterfaces.os.mem.free      = STDC_Free;
```  
**MQTT Interface **  
The OTA MQTT interface is a set of APIs that must be implemented in a library to enable the OTA library to download a file block from streaming service\.  
Example using the coreMQTT Agent APIs from the [ OTA over MQTT demo](https://github.com/aws/amazon-freertos/blob/main/demos/ota/ota_demo_core_mqtt/ota_demo_core_mqtt.c)\-   

```
  OtaInterfaces_t otaInterfaces;
  otaInterfaces.mqtt.subscribe = prvMqttSubscribe;
  otaInterfaces.mqtt.publish = prvMqttPublish;
  otaInterfaces.mqtt.unsubscribe = prvMqttUnSubscribe;
```  
**HTTP Interface**  
The OTA HTTP interface is a set of APIs that must be implemented in a library to enable the OTA library to download a file block by connecting to a pre\-signed url and fetching data blocks\. It is optional unless you configure the OTA library to download from a pre\-signed URL instead of a streaming service\.  
Example using the coreHTTP APIs from the [ OTA over HTTP demo](https://github.com/aws/amazon-freertos/blob/main/demos/ota/ota_demo_core_http/ota_demo_core_http.c)\-   

```
  OtaInterfaces_t otaInterfaces;
  otaInterfaces.http.init = httpInit;
  otaInterfaces.http.request = httpRequest;
  otaInterfaces.http.deinit = httpDeinit;
```  
**OTA PAL Interface**  
The OTA PAL interface is a set of APIs that must be implemented for the device to use the OTA library\. The device specific implementation for the OTA PAL is provided to the library in the user application\. These functions are used by the library to store, manage, and authenticate downloads\.  

```
    OtaInterfaces_t otaInterfaces;
    otaInterfaces.pal.getPlatformImageState = otaPal_GetPlatformImageState;
    otaInterfaces.pal.setPlatformImageState = otaPal_SetPlatformImageState;
    otaInterfaces.pal.writeBlock = otaPal_WriteBlock;
    otaInterfaces.pal.activate = otaPal_ActivateNewImage;
    otaInterfaces.pal.closeFile = otaPal_CloseFile;
    otaInterfaces.pal.reset = otaPal_ResetDevice;
    otaInterfaces.pal.abort = otaPal_Abort;
    otaInterfaces.pal.createFile = otaPal_CreateFileForRx;
```  
**Changes in return \-**  
The return is changed from OTA agent state to OTA error code\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0 : OtaErr\_t](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#ga7ab3c74dc057383c56c6cb9aa6bf0b2d)\.

### OTA\_Shutdown<a name="porting-migration-ota-shutdown"></a>

In the OTA Library version 1 the API used to shutdown the OTA Agent was OTA\_AgentShutdown which is now changed to OTA\_Shutdown along with changes in input parameters\.

**OTA Agent Shutdown \( version 1 \)**  

```
OTA_State_t OTA_AgentShutdown( TickType_t xTicksToWait );
```

**OTA Agent Shutdown \( version 3 \) **  

```
OtaState_t OTA_Shutdown( uint32_t ticksToWait,
                         uint8_t unsubscribeFlag );
```

**ticksToWait \- **  
The number of ticks to wait for the OTA Agent to complete the shutdown process\. If this is set to zero, the function will return immediately without waiting\. The actual state is returned to the caller\. The agent does not sleep for this while but used for busy looping\.

**New input parameter \- **  
unsubscribeFlag \-   
Flag to indicate if unsubscribe operations should be performed from the job topics when shutdown is called\. If the flag is 0 then unsubscribe operations are not called for job topics\. If the application must be unsubscribed from the job topics then this flag must be set to 1 when calling OTA\_Shutdown\. 

**Changes in return \-**  
OtaState\_t \-   
The enum for OTA Agent state and its members are renamed\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#ga1cb476a5e0ee81fa486f605e64419dcc)\.

### OTA\_GetState<a name="porting-migration-ota-get-state"></a>

The API name is changed from OTA\_AgentGetState to OTA\_GetState\.

**OTA Agent Shutdown \( version 1 \)**  

```
OTA_State_t OTA_GetAgentState( void );
```

**OTA Agent Shutdown \( version 3 \)**  

```
OtaState_t OTA_GetState( void );
```

**Changes in return \-**  
OtaState\_t \-   
The enum for OTA Agent state and its members are renamed\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#ga1cb476a5e0ee81fa486f605e64419dcc)\.

### OTA\_GetStatistics<a name="porting-migration-ota-get-stats"></a>

New single API added for statistics\. It replaces the APIs OTA\_GetPacketsReceived, OTA\_GetPacketsQueued, OTA\_GetPacketsProcessed, OTA\_GetPacketsDropped\. Also, in the OTA Library version 3, the statistics numbers are related to the current job only\.

**OTA Library version 1**  

```
uint32_t OTA_GetPacketsReceived( void );
uint32_t OTA_GetPacketsQueued( void );
uint32_t OTA_GetPacketsProcessed( void );
uint32_t OTA_GetPacketsDropped( void );
```

**OTA Library version 3**  

```
OtaErr_t OTA_GetStatistics( OtaAgentStatistics_t * pStatistics );
```

**pStatistics \- **  
Input/output parameter for statistics data like packets received, dropped, queued and processed for current job\.

**Output parameter \- **  
OTA error code\.

**Example usage \- **  

```
OtaAgentStatistics_t otaStatistics = { 0 };
OTA_GetStatistics( &otaStatistics );
LogInfo( ( " Received: %u   Queued: %u   Processed: %u   Dropped: %u",
                           otaStatistics.otaPacketsReceived,
                           otaStatistics.otaPacketsQueued,
                           otaStatistics.otaPacketsProcessed,
                           otaStatistics.otaPacketsDropped ) );
```

### OTA\_ActivateNewImage<a name="porting-migration-ota-activate"></a>

The input parameters are the same but the return OTA error code is renamed and new error codes are added in the version 3 of the OTA library\.

**OTA Library version 1**  

```
OTA_Err_t OTA_ActivateNewImage( void ); 
```

**OTA Library version 3**  

```
OtaErr_t OTA_ActivateNewImage( void );
```
The return OTA error code enum is changed and new error codes are added\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0 : OtaErr\_t](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#ga7ab3c74dc057383c56c6cb9aa6bf0b2d)\.

**Example usage \- **  

```
 OtaErr_t otaErr = OtaErrNone;
 otaErr = OTA_ActivateNewImage();
 /* Handle error */
```

### OTA\_SetImageState<a name="porting-migration-ota-set-image"></a>

The input parameters are the same and renamed, the return OTA error code is renamed and new error codes are added in the version 3 of the OTA library\.

**OTA Library version 1 **  

```
OTA_Err_t OTA_SetImageState( OTA_ImageState_t eState ); 
```

**OTA Library version 3**  

```
OtaErr_t OTA_SetImageState( OtaImageState_t state ); 
```
The input parameter is renamed to OtaImageState\_t\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#gad4909faa8b9c8672e7f7bef1bc6b5b84)\.  
The return OTA error code enum is changed and new error codes are added\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0 / OtaErr\_t](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#ga7ab3c74dc057383c56c6cb9aa6bf0b2d)\.

**Example usage \- **  

```
  OtaErr_t otaErr = OtaErrNone;
  otaErr = OTA_SetImageState( OtaImageStateAccepted );
  /* Handle error */
```

### OTA\_GetImageState<a name="porting-migration-ota-get-image"></a>

The input parameters are same, the return enum is renamed in the version 3 of the OTA library\. 

**OTA Library version 1 **  

```
OTA_ImageState_t OTA_GetImageState( void ); 
```

**OTA Library version 3**  

```
OtaImageState_t OTA_GetImageState( void ); 
```
The return enum is renamed to OtaImageState\_t\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0 : OtaImageState\_t ](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#gad4909faa8b9c8672e7f7bef1bc6b5b84)\.

**Example usage \- **  

```
 OtaImageState_t imageState;
 imageState = OTA_GetImageState();
```

### OTA\_Suspend<a name="porting-migration-ota-suspend"></a>

The input parameters are the same, the return OTA error code is renamed and new error codes are added in the version 3 of the OTA library\. 

**OTA Library version 1 **  

```
OTA_Err_t OTA_Suspend( void ); 
```

**OTA Library version 3**  

```
OtaErr_t OTA_Suspend( void ); 
```
The return OTA error code enum is changed and new error codes are added\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0 : OtaErr\_t](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#ga7ab3c74dc057383c56c6cb9aa6bf0b2d)\. 

**Example usage \- **  

```
OtaErr_t xOtaError = OtaErrUninitialized;
xOtaError = OTA_Suspend();
/* Handle error */
```

### OTA\_Resume<a name="porting-migration-ota-resume"></a>

The input parameter for connection is removed as the connection is handled in the OTA demo/application, the return OTA error code is renamed and new error codes are added in the version 3 of the OTA library\.

**OTA Library version 1 **  

```
OTA_Err_t OTA_Resume( void * pxConnection ); 
```

**OTA Library version 3**  

```
OtaErr_t OTA_Resume( void ); 
```
The return OTA error code enum is changed and new error codes are added\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0 : OtaErr\_t](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#ga7ab3c74dc057383c56c6cb9aa6bf0b2d)\.

**Example usage \- **  

```
OtaErr_t xOtaError = OtaErrUninitialized;
xOtaError = OTA_Resume();
/* Handle error */
```

### OTA\_CheckForUpdate<a name="porting-migration-ota-check"></a>

The input parameters are the same, the return OTA error code is renamed and new error codes are added in the version 3 of the OTA library\.

**OTA Library version 1 **  

```
OTA_Err_t OTA_CheckForUpdate( void ); 
```

**OTA Library version 3**  

```
OtaErr_t OTA_CheckForUpdate( void ) 
```
The return OTA error code enum is changed and new error codes are added\. Please refer to [AWS IoT Over\-the\-air Update v3\.0\.0 : OtaErr\_t](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/group__ota__enum__types.html#ga7ab3c74dc057383c56c6cb9aa6bf0b2d)\.

### OTA\_EventProcessingTask<a name="porting-migration-ota-event"></a>

This is a new API and is the main event loop to handle events for OTA updates\. It must be called by the application task\. This loop will continue to handle and execute events received for OTA Update until this task is terminated by the application\.

**OTA Library version 3 **  

```
void OTA_EventProcessingTask( void * pUnused ); 
```

**Example for FreeRTOS \-**  

```
/* Create FreeRTOS task*/
xTaskCreate( prvOTAAgentTask,
             "OTA Agent Task",
             otaexampleAGENT_TASK_STACK_SIZE,
             NULL,
             otaexampleAGENT_TASK_PRIORITY,
             NULL );
   
/* Call OTA_EventProcessingTask from the task */                        
static void prvOTAAgentTask( void * pParam )
{
    /* Calling OTA agent task. */
    OTA_EventProcessingTask( pParam );
    LogInfo( ( "OTA Agent stopped." ) );

    /* Delete the task as it is no longer required. */
    vTaskDelete( NULL );
}
```

**Example for POSIX \- **  

```
/* Create posix thread.*/
if( pthread_create( &threadHandle, NULL, otaThread, NULL ) != 0 )
{
    LogError( ( "Failed to create OTA thread: "
                ",errno=%s",
                strerror( errno ) ) );

   /* Handle error. */
}
   
/* Call OTA_EventProcessingTask from the thread.*/                        
static void * otaThread( void * pParam )
{
    /* Calling OTA agent task. */
    OTA_EventProcessingTask( pParam );
    LogInfo( ( "OTA Agent stopped." ) );
    
    return NULL;
}
```

### OTA\_SignalEvent<a name="porting-migration-ota-signal"></a>

This is a new API that adds the event to the back of the event queue and is also used by internal OTA modules to signal agent task\.

**OTA Library version 3**  

```
bool OTA_SignalEvent( const OtaEventMsg_t * const pEventMsg ); 
```

**Example usage \- **  

```
OtaEventMsg_t xEventMsg = { 0 };
xEventMsg.eventId = OtaAgentEventStart;
( void ) OTA_SignalEvent( &xEventMsg );
```

## Integrating the OTA Library as a submodule in your application<a name="porting-migration-ota-integrating"></a>

If you want to integrate the OTA library in your own application you can use the git submodule command\. Git submodules allow you to keep a Git repository as a subdirectory of another Git repository\. The OTA library version 3 is maintained in the [ ota\-for\-aws\-iot\-embedded\-sdk](https://github.com/aws/ota-for-aws-iot-embedded-sdk) repository\.

```
git submodule add https://github.com/aws/ota-for-aws-iot-embedded-sdk.git destination_folder
```

```
git commit -m "Added the OTA Library as submodule to the project."
```

```
git push
```

For more information, see [ Integrating the OTA Agent into your application](https://docs.aws.amazon.com/freertos/latest/userguide/integrate-ota-agent.html) in the *FreeRTOS User Guide*\.

## References<a name="porting-migration-ota-references"></a>
+ [ OTAv1](https://github.com/aws/amazon-freertos/tree/202012.00/libraries/freertos_plus/aws/ota)\. 
+ [OTAv3](https://github.com/aws/ota-for-aws-iot-embedded-sdk/tree/v3.0.0)\.