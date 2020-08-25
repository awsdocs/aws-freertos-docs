# Integrating the OTA Agent into your application<a name="integrate-ota-agent"></a>

The over\-the\-air \(OTA\) Agent is designed to simplify the amount of code you must write to add OTA update functionality to your product\. That integration burden consists primarily of initialization of the OTA Agent and, optionally, creating a custom callback function for responding to the OTA completion event messages\.

**Note**  
Although the integration of the OTA update feature into your application is rather simple, the OTA update system requires an understanding of more than just device code integration\. To familiarize yourself with how to configure your AWS account with AWS IoT things, credentials, code\-signing certificates, provisioning devices, and OTA update jobs, see [FreeRTOS Prerequisites](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-prereqs.html)\. 

## Connection management<a name="ota-agent-http-mqtt"></a>

The OTA Agent uses the MQTT protocol for all control communication operations involving AWS IoT services, but it doesn't manage the MQTT connection\. To ensure that the OTA Agent doesn't interfere with the connection management policy of your application, the MQTT connection \(including disconnect and any reconnect functionality\) must be handled by the main user application\. The file can be downloaded over the MQTT or HTTP protocol\. You can choose which protocol when you create the OTA job\. If you choose MQTT, the OTA Agent uses the same connection for control operations and for downloading file\. If you choose HTTP, the OTA Agent handles the HTTP connections\. 

## Simple OTA demo<a name="simple-demo-agent"></a>

The following is an excerpt of a simple OTA demo that shows you how the Agent connects to the MQTT broker and initializes the OTA Agent\. In this example, we configure the demo to use the default OTA completion callback and to return some statistics once per second\. For brevity, we leave out some details from this demo\.

The OTA demo also demonstrates MQTT connection management by monitoring the disconnect callback and reestablishing the connection\. When a disconnect happens, the demo first suspends the OTA Agent operations and then attempts to reestablish the MQTT connection\. The MQTT reconnection attempts are delayed by a time which is exponentially increased up to a maximum value and a jitter is also added\. If the connection is reestablished, the OTA Agent continues its operations\. 

For a working example that uses the AWS IoT MQTT broker, see the OTA demo code in the `demos/ota` directory\.

Because the OTA Agent is its own task, the intentional one\-second delay in this example affects this application only\. It has no impact on the performance of the Agent\.

```
void vRunOTAUpdateDemo( bool awsIotMqttMode,
                        const char * pIdentifier,
                        void * pNetworkServerInfo,
                        void * pNetworkCredentialInfo,
                        const IotNetworkInterface_t * pNetworkInterface )
{
    OTA_State_t eState;
    static OTA_ConnectionContext_t xOTAConnectionCtx;

    IotLogInfo( "OTA demo version %u.%u.%u\r\n",
                xAppFirmwareVersion.u.x.ucMajor,
                xAppFirmwareVersion.u.x.ucMinor,
                xAppFirmwareVersion.u.x.usBuild );

    for( ; ; )
    {
        IotLogInfo( "Connecting to broker...\r\n" );

        /* Establish a new MQTT connection. */
        if( _establishMqttConnection( awsIotMqttMode,
                                      pIdentifier,
                                      pNetworkServerInfo,
                                      pNetworkCredentialInfo,
                                      pNetworkInterface,
                                      &_mqttConnection ) == EXIT_SUCCESS )
        {
            /* Update the connection context shared with OTA Agent.*/
            xOTAConnectionCtx.pxNetworkInterface = ( void * ) pNetworkInterface;
            xOTAConnectionCtx.pvNetworkCredentials = pNetworkCredentialInfo;
            xOTAConnectionCtx.pvControlClient = _mqttConnection;

            /* Set the base interval for connection retry.*/
            _retryInterval = OTA_DEMO_CONN_RETRY_BASE_INTERVAL_SECONDS;

            /* Update the connection available flag.*/
            _networkConnected = true;

            /* Check if OTA Agent is suspended and resume.*/
            if( ( eState = OTA_GetAgentState() ) == eOTA_AgentState_Suspended )
            {
                OTA_Resume( &xOTAConnectionCtx );
            }

            /* Initialize the OTA Agent , if it is resuming the OTA statistics will be cleared for new connection.*/
            OTA_AgentInit( ( void * ) ( &xOTAConnectionCtx ),
                           ( const uint8_t * ) ( clientcredentialIOT_THING_NAME ),
                           App_OTACompleteCallback,
                           ( TickType_t ) ~0 );

            while( ( ( eState = OTA_GetAgentState() ) != eOTA_AgentState_Stopped ) && _networkConnected )
            {
                /* Wait forever for OTA traffic but allow other tasks to run and output statistics only once per second. */
                IotClock_SleepMs( OTA_DEMO_TASK_DELAY_SECONDS * 1000 );

                IotLogInfo( "State: %s  Received: %u   Queued: %u   Processed: %u   Dropped: %u\r\n", _pStateStr[ eState ],
                            OTA_GetPacketsReceived(), OTA_GetPacketsQueued(), OTA_GetPacketsProcessed(), OTA_GetPacketsDropped() );
            }

            /* Check if we got network disconnect callback and suspend OTA Agent.*/
            if( _networkConnected == false )
            {
                /* Suspend OTA agent.*/
                if( OTA_Suspend() == kOTA_Err_None )
                {
                    while( ( eState = OTA_GetAgentState() ) != eOTA_AgentState_Suspended )
                    {
                        /* Wait for OTA Agent to process the suspend event. */
                        IotClock_SleepMs( OTA_DEMO_TASK_DELAY_SECONDS * 1000 );
                    }
                }
            }
            else
            {
                /* Try to close the MQTT connection. */
                if( _mqttConnection != NULL )
                {
                    IotMqtt_Disconnect( _mqttConnection, 0 );
                }
            }
        }
        else
        {
            IotLogError( "ERROR:  MQTT_AGENT_Connect() Failed.\r\n" );
        }

        /* After failure to connect or a disconnect, delay for retrying connection. */
        _connectionRetryDelay();
    }
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

## Using a custom callback for OTA completion events<a name="custom-callback-ota"></a>

The previous example used `App_OTACompleteCallback` as the callback handler for OTA completion events\. \(See the third parameter to the `OTA_AgentInit` API call\.\) `App_OTACompleteCallback` is defined in `freertos/demos/ota/aws_iot_ota_update_demo.c`\. If you want to implement custom handling of the completion events, you must pass the function address of your callback handler to the `OTA_AgentInit` API\. During the OTA process, the Agent can send one of the following event enums to the callback handler\. It is up to the application developer to decide how and when to handle these events\.

```
/**
* @brief OTA Job callback events.
*
* After an OTA update image is received and authenticated, the Agent calls the user
* callback (set with the OTA_AgentInit API) with the value eOTA_JobEvent_Activate to
* signal that the device must be rebooted to activate the new image. When the device
* boots, if the OTA job status is in self test mode, the Agent calls the user callback
* with the value eOTA_JobEvent_StartTest, signaling that any additional self tests
* should be performed.
*
* If the OTA receive fails for any reason, the Agent calls the user callback with
* the value eOTA_JobEvent_Fail instead to allow the user to log the failure and take
* any action deemed appropriate by the user code.
*
*/
typedef enum {
    eOTA_JobEvent_Activate,  /*! OTA receive is authenticated and ready to activate. */
    eOTA_JobEvent_Fail,      /*! OTA receive failed. Unable to use this update. */
    eOTA_JobEvent_StartTest  /*! OTA job is now in self test, perform user tests. */
} OTA_JobEvent_t;
```

The OTA Agent can receive an update in the background during active processing of the main application\. The purpose of delivering these events is to allow the application to decide if action can be taken immediately or if it should be deferred until after completion of some other application\-specific processing\. This prevents an unanticipated interruption of your device during active processing \(for example, vacuuming\) that would be caused by a reset after a firmware update\. These are the job events received by the callback handler:

`eOTA_JobEvent_Activate event`  
When this event is received by the callback handler, you can either reset the device immediately or schedule a call to reset the device later\. This allows you to postpone the device reset and self\-test phase, if necessary\.

`eOTA_JobEvent_Fail event`  
When this event is received by the callback handler, the update has failed\. You do not need to do anything in this case\. You might want to output a log message or do something application\-specific\.

`eOTA_JobEvent_StartTest event`  
The self\-test phase is meant to allow newly updated firmware to execute and test itself before determining that it is properly functioning and commit it to be the latest permanent application image\. When a new update is received and authenticated and the device has been reset, the OTA Agent sends the `eOTA_JobEvent_StartTest` event to the callback function when it is ready for testing\. The developer can add any required tests to determine if the device firmware is functioning properly after update\. When the device firmware is deemed reliable by the self tests, the code must commit the firmware as the new permanent image by calling the `OTA_SetImageState( eOTA_ImageState_Accepted )` function\.

If your device has no special hardware or mechanisms that need to be tested, you can use the default callback handler\. Upon receipt of the `eOTA_JobEvent_Activate` event, the default handler resets the device immediately\.