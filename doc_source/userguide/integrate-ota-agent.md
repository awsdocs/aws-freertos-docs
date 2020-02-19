# Integrating the OTA Agent into Your Application<a name="integrate-ota-agent"></a>

The over\-the\-air \(OTA\) agent is designed to simplify the amount of code you must write to add OTA update functionality to your product\. That integration burden consists primarily of initialization of the OTA agent and, optionally, creating a custom callback function for responding to the OTA completion event messages\.

**Note**  
Although the integration of the OTA update feature into your application is rather simple, the OTA update system requires an understanding of more than just device code integration\. To familiarize yourself with how to configure your AWS account with AWS IoT things, credentials, code\-signing certificates, provisioning devices, and OTA update jobs, see [FreeRTOS Prerequisites](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-prereqs.html)\. 

## Connection Management<a name="ota-agent-http-mqtt"></a>

The OTA agent uses the MQTT protocol for all control communication operations involving AWS IoT services, but it doesn't manage the MQTT connection\. To ensure that the OTA agent doesn't interfere with the connection management policy of your application, the MQTT connection \(including disconnect and any reconnect functionality\) must be handled by the main user application\. The file can be downloaded over the MQTT or HTTP protocol\. You can choose which protocol when you create the OTA job\. If you choose MQTT, the OTA agent uses the same connection for control operations and for downloading file\. If you choose HTTP, the OTA agent handles the HTTP connections\. 

## Simple OTA Demo Using MQTT<a name="simple-demo-agent"></a>

The following is an excerpt of a simple OTA demo that shows you how the agent connects to the MQTT broker and initializes the OTA agent\. In this example, we configure the demo to use the default OTA completion callback and print out some statistics once per second\. For brevity, we leave out some details from this demo\.

For a working example that uses the AWS IoT MQTT broker, see the OTA demo code in the `demos/ota` directory\.

Because the OTA agent is its own task, the intentional one\-second delay in this example affects this application only\. It has no impact on the performance of the agent\.

```
void vRunOTAUpdateDemo( const IotNetworkInterface_t * pNetworkInterface,
                        void * pNetworkCredentialInfo )
{
    IotMqttConnectInfo_t xConnectInfo = IOT_MQTT_CONNECT_INFO_INITIALIZER;
    OTA_State_t eState;
    OTA_ConnectionContext_t xOTAConnectionCtx = { 0 };

    configPRINTF( ( "OTA demo version %u.%u.%u\r\n",
                    xAppFirmwareVersion.u.x.ucMajor,
                    xAppFirmwareVersion.u.x.ucMinor,
                    xAppFirmwareVersion.u.x.usBuild ) );
    configPRINTF( ( "Creating MQTT Client...\r\n" ) );

    /* Create the MQTT Client. */

    for( ; ; )
    {
        xNetworkConnected = prxCreateNetworkConnection();

        if( xNetworkConnected )
        {
            configPRINTF( ( "Connecting to broker...\r\n" ) );
            memset( &xConnectInfo, 0, sizeof( xConnectInfo ) );

            if( xConnection.ulNetworkType == AWSIOT_NETWORK_TYPE_BLE )
            {
                xConnectInfo.awsIotMqttMode = false;
                xConnectInfo.keepAliveSeconds = 0;
            }
            else
            {
                xConnectInfo.awsIotMqttMode = true;
                xConnectInfo.keepAliveSeconds = otaDemoKEEPALIVE_SECONDS;
            }

            xConnectInfo.cleanSession = true;
            xConnectInfo.clientIdentifierLength = ( uint16_t ) strlen( clientcredentialIOT_THING_NAME );
            xConnectInfo.pClientIdentifier = clientcredentialIOT_THING_NAME;

            /* Connect to the broker. */
            if( IotMqtt_Connect( &( xConnection.xNetworkInfo ),
                                 &xConnectInfo,
                                 otaDemoCONN_TIMEOUT_MS, &( xConnection.xMqttConnection ) ) == IOT_MQTT_SUCCESS )
            {
                configPRINTF( ( "Connected to broker.\r\n" ) );
                xOTAConnectionCtx.pvControlClient = xConnection.xMqttConnection;
                xOTAConnectionCtx.pxNetworkInterface = ( void * ) pNetworkInterface;
                xOTAConnectionCtx.pvNetworkCredentials = pNetworkCredentialInfo;

                OTA_AgentInit( ( void * ) ( &xOTAConnectionCtx ), ( const uint8_t * ) ( clientcredentialIOT_THING_NAME ), App_OTACompleteCallback, ( TickType_t ) ~0 );

                while( ( eState = OTA_GetAgentState() ) != eOTA_AgentState_Stopped )
                {
                    /* Wait forever for OTA traffic but allow other tasks to run and output statistics only once per second. */
                    vTaskDelay( myappONE_SECOND_DELAY_IN_TICKS );
                    configPRINTF( ( "State: %s  Received: %u   Queued: %u   Processed: %u   Dropped: %u\r\n", pcStateStr[ eState ],
                                    OTA_GetPacketsReceived(), OTA_GetPacketsQueued(), OTA_GetPacketsProcessed(), OTA_GetPacketsDropped() ) );
                }

                IotMqtt_Disconnect( xConnection.xMqttConnection, false );
            }
            else
            {
                configPRINTF( ( "ERROR:  MQTT_AGENT_Connect() Failed.\r\n" ) );
            }

            vMqttDemoDeleteNetworkConnection( &xConnection );

            /* After failure to connect or a disconnect, wait an arbitrary one second before retry. */
            vTaskDelay( myappONE_SECOND_DELAY_IN_TICKS );
        }
        else
        {
            configPRINTF( ( "Failed to create MQTT client.\r\n" ) );
        }
    }
}
```

Here is the high\-level flow of this demo application:
+ Create an MQTT agent context\.
+ Connect to your AWS IoT endpoint\.
+ Initialize the OTA agent\.
+ Loop allowing an OTA update job and output statistics once a second\.
+ If the agent stops, wait one second and try connecting again\.

## Using a Custom Callback for OTA Completion Events<a name="custom-callback-ota"></a>

The previous example used the built\-in callback handler for OTA completion events by specifying `NULL` for the third parameter to the `OTA_AgentInit` API\. If you want to implement custom handling of the completion events, you must pass the function address of your callback handler to the `OTA_AgentInit` API\. During the OTA process, the agent can send one of the following event enums to the callback handler\. It is up to the application developer to decide how and when to handle these events\.

```
/**
* @brief OTA Job callback events.
*
* After an OTA update image is received and authenticated, the agent calls the user
* callback (set with the OTA_AgentInit API) with the value eOTA_JobEvent_Activate to
* signal that the device must be rebooted to activate the new image. When the device
* boots, if the OTA job status is in self test mode, the agent calls the user callback
* with the value eOTA_JobEvent_StartTest, signaling that any additional self tests
* should be performed.
*
* If the OTA receive fails for any reason, the agent calls the user callback with
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

The OTA agent can receive an update in the background during active processing of the main application\. The purpose of delivering these events is to allow the application to decide if action can be taken immediately or if it should be deferred until after completion of some other application\-specific processing\. This prevents an unanticipated interruption of your device during active processing \(for example, vacuuming\) that would be caused by a reset after a firmware update\. These are the job events received by the callback handler:

`eOTA_JobEvent_Activate event`  
When this event is received by the callback handler, you can either reset the device immediately or schedule a call to reset the device later\. This allows you to postpone the device reset and self\-test phase, if necessary\.

`eOTA_JobEvent_Fail event`  
When this event is received by the callback handler, the update has failed\. You do not need to do anything in this case\. You might want to output a log message or do something application\-specific\.

`eOTA_JobEvent_StartTest event`  
The self\-test phase is meant to allow newly updated firmware to execute and test itself before determining that it is properly functioning and commit it to be the latest permanent application image\. When a new update is received and authenticated and the device has been reset, the OTA agent sends the `eOTA_JobEvent_StartTest` event to the callback function when it is ready for testing\. The developer can add any required tests to determine if the device firmware is functioning properly after update\. When the device firmware is deemed reliable by the self tests, the code must commit the firmware as the new permanent image by calling the `OTA_SetImageState( eOTA_ImageState_Accepted )` function\.

If your device has no special hardware or mechanisms that need to be tested, you can use the default callback handler\. Upon receipt of the `eOTA_JobEvent_Activate` event, the default handler resets the device immediately\.