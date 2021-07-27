# Integrating the OTA Agent into your application<a name="integrate-ota-agent"></a>

The over\-the\-air \(OTA\) Agent is designed to simplify the amount of code you must write to add OTA update functionality to your product\. That integration burden consists primarily of initialization of the OTA Agent and creating a custom callback function for responding to the OTA Agent event messages\. During initialization OS, MQTT, HTTP \(if HTTP is used for file download\) and platform specific implementation \(PAL\) interfaces are passed to the OTA Agent\. Buffers can also be initialized and passed to the OTA Agent\.

**Note**  
Although the integration of the OTA update feature into your application is rather simple, the OTA update system requires an understanding of more than just device code integration\. To familiarize yourself with how to configure your AWS account with AWS IoT things, credentials, code\-signing certificates, provisioning devices, and OTA update jobs, see [FreeRTOS Prerequisites](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-prereqs.html)\. 

## Connection management<a name="ota-agent-http-mqtt"></a>

The OTA Agent uses the MQTT protocol for all control communication operations involving AWS IoT services, but it doesn't manage the MQTT connection\. To ensure that the OTA Agent doesn't interfere with the connection management policy of your application, the MQTT connection \(including disconnect and any reconnect functionality\) must be handled by the main user application\. The file can be downloaded over the MQTT or HTTP protocol\. You can choose which protocol when you create the OTA job\. If you choose MQTT, the OTA Agent uses the same connection for control operations and for downloading files\.

## Simple OTA demo<a name="simple-demo-agent"></a>

The following is an excerpt of a simple OTA demo that shows you how the Agent connects to the MQTT broker and initializes the OTA Agent\. In this example, we configure the demo to use the default OTA application callback and to return some statistics once per second\. For brevity, we leave out some details from this demo\.

The OTA demo also demonstrates MQTT connection management by monitoring the disconnect callback and reestablishing the connection\. When a disconnect happens, the demo first suspends the OTA Agent operations and then attempts to reestablish the MQTT connection\. The MQTT reconnection attempts are delayed by a time which is exponentially increased up to a maximum value and a jitter is also added\. If the connection is reestablished, the OTA Agent continues its operations\. 

For a working example that uses the AWS IoT MQTT broker, see the OTA demo code in the `demos/ota` directory\.

Because the OTA Agent is its own task, the intentional one\-second delay in this example affects this application only\. It has no impact on the performance of the Agent\.

```
static BaseType_t prvRunOTADemo( void )
{
    /* Status indicating a successful demo or not. */
    BaseType_t xStatus = pdFAIL;

    /* OTA library return status. */
    OtaErr_t xOtaError = OtaErrUninitialized;

    /* OTA event message used for sending event to OTA Agent.*/
    OtaEventMsg_t xEventMsg = { 0 };

    /* OTA interface context required for library interface functions.*/
    OtaInterfaces_t xOtaInterfaces;

    /* OTA library packet statistics per job.*/
    OtaAgentStatistics_t xOtaStatistics = { 0 };

    /* OTA Agent state returned from calling OTA_GetState.*/
    OtaState_t xOtaState = OtaAgentStateStopped;

    /* Set OTA Library interfaces.*/
    prvSetOtaInterfaces( &xOtaInterfaces );

    /*************************** Init OTA Library. ***************************/

    if( ( xOtaError = OTA_Init( &xOtaBuffer,
                                &xOtaInterfaces,
                                ( const uint8_t * ) ( democonfigCLIENT_IDENTIFIER ),
                                prvOtaAppCallback ) ) != OtaErrNone )
    {
        LogError( ( "Failed to initialize OTA Agent, exiting = %u.",
                    xOtaError ) );
    }
    else
    {
        xStatus = pdPASS;
    }

    /************************ Create OTA Agent Task. ************************/

    if( xStatus == pdPASS )
    {
        xStatus = xTaskCreate( prvOTAAgentTask,
                               "OTA Agent Task",
                               otaexampleAGENT_TASK_STACK_SIZE,
                               NULL,
                               otaexampleAGENT_TASK_PRIORITY,
                               NULL );

        if( xStatus != pdPASS )
        {
            LogError( ( "Failed to create OTA agent task:" ) );
        }
    }

    /****************************** Start OTA ******************************/

    if( xStatus == pdPASS )
    {
        /* Send start event to OTA Agent.*/
        xEventMsg.eventId = OtaAgentEventStart;
        OTA_SignalEvent( &xEventMsg );
    }

    /******************** Loop and display OTA statistics ********************/

    if( xStatus == pdPASS )
    {
        while( ( xOtaState = OTA_GetState() ) != OtaAgentStateStopped )
        {
            /* Get OTA statistics for currently executing job. */
            if( xOtaState != OtaAgentStateSuspended )
            {
                OTA_GetStatistics( &xOtaStatistics );

                LogInfo( ( " Received: %u   Queued: %u   Processed: %u   Dropped: %u",
                           xOtaStatistics.otaPacketsReceived,
                           xOtaStatistics.otaPacketsQueued,
                           xOtaStatistics.otaPacketsProcessed,
                           xOtaStatistics.otaPacketsDropped ) );
            }

            vTaskDelay( pdMS_TO_TICKS( otaexampleEXAMPLE_TASK_DELAY_MS ) );
        }
    }

    return xStatus;
}
```

Here is the high\-level flow of this demo application:
+ Create an MQTT Agent context\.
+ Connect to your AWS IoT endpoint\.
+ Initialize the OTA Agent\.
+ Loop that allows an OTA update job and outputs statistics once a second\.
+ If MQTT disconnects, suspend the OTA Agent operations\.
+ Try connecting again with exponential delay and jitter\.
+ If reconnected, resume OTA Agent operations\.
+ If the Agent stops, delay one second, and then try reconnecting\.

## Using application callback for OTA Agent events<a name="application-callback-ota"></a>

The previous example used `prvOtaAppCallback` as the callback handler for OTA Agent events\. \(See the fourth parameter to the `OTA_Init` API call\)\. If you want to implement custom handling of the completion events, you must change the default handling in the OTA demo/application\. During the OTA process, the OTA Agent can send one of the following event enums to the callback handler\. It is up to the application developer to decide how and when to handle these events\.

```
/**
 * @ingroup ota_enum_types
 * @brief OTA Job callback events.
 *
 * After an OTA update image is received and authenticated, the agent calls the user
 * callback (set with the @ref OTA_Init API) with the value OtaJobEventActivate to
 * signal that the device must be rebooted to activate the new image. When the device
 * boots, if the OTA job status is in self test mode, the agent calls the user callback
 * with the value OtaJobEventStartTest, signaling that any additional self tests
 * should be performed.
 *
 * If the OTA receive fails for any reason, the agent calls the user callback with
 * the value OtaJobEventFail instead to allow the user to log the failure and take
 * any action deemed appropriate by the user code.
 *
 * See the OtaImageState_t type for more information.
 */
typedef enum OtaJobEvent
{
    OtaJobEventActivate = 0,       /*!< @brief OTA receive is authenticated and ready to activate. */
    OtaJobEventFail = 1,           /*!< @brief OTA receive failed. Unable to use this update. */
    OtaJobEventStartTest = 2,      /*!< @brief OTA job is now in self test, perform user tests. */
    OtaJobEventProcessed = 3,      /*!< @brief OTA event queued by OTA_SignalEvent is processed. */
    OtaJobEventSelfTestFailed = 4, /*!< @brief OTA self-test failed for current job. */
    OtaJobEventParseCustomJob = 5, /*!< @brief OTA event for parsing custom job document. */
    OtaJobEventReceivedJob = 6,    /*!< @brief OTA event when a new valid AFT-OTA job is received. */
    OtaJobEventUpdateComplete = 7, /*!< @brief OTA event when the update is completed. */
    OtaLastJobEvent = OtaJobEventStartTest
} OtaJobEvent_t;
```

The OTA Agent can receive an update in the background during active processing of the main application\. The purpose of delivering these events is to allow the application to decide if action can be taken immediately or if it should be deferred until after completion of some other application\-specific processing\. This prevents an unanticipated interruption of your device during active processing \(for example, vacuuming\) that would be caused by a reset after a firmware update\. These are the job events received by the callback handler:

**`OtaJobEventActivate `**  
When the callback handler receives this event, you can either reset the device immediately or schedule a call to reset the device later\. This allows you to postpone the device reset and self\-test phase, if necessary\.

**`OtaJobEventFail`**  
When the callback handler receives this event, the update has failed\. You do not need to do anything in this case\. You might want to output a log message or do something application\-specific\.

**`OtaJobEventStartTest`**  
The self\-test phase is meant to allow newly updated firmware to execute and test itself before it determines whether it is functioning properly and commit itself to be the latest permanent application image\. When a new update is received and authenticated and the device has been reset, the OTA Agent sends the `OtaJobEventStartTest` event to the callback function when it is ready for testing\. The developer can add any required tests to determine if the device firmware is functioning properly after update\. When the device firmware is deemed reliable by the self tests, the code must commit the firmware as the new permanent image by calling the `OTA_SetImageState( OtaImageStateAccepted )` function\.

**`OtaJobEventProcessed`**  
The OTA event queued by `OTA_SignalEvent` is processed, so cleanup operations like freeing the OTA buffers can be done\.

**`OtaJobEventSelfTestFailed`**  
The OTA self\-test failed for the current job\. The default handling for this event is to shut down the OTA Agent and restart it so that the device rolls back to the previous image\.

**`OtaJobEventUpdateComplete`**  
The notification event for OTA job update completion\.