# Integrating the OTA Agent into Your Application<a name="integrate-ota-agent"></a>

The OTA agent is designed to simplify the amount of code you must write to add OTA update functionality to your product\. That integration burden consists primarily of initialization of the OTA agent and, optionally, creating a custom callback function for responding to the OTA completion event messages\.

**Note**  
Although the integration of the OTA update feature into your application is rather simple, the OTA update system requires an understanding of more than just device code integration\. To familiarize yourself with how to configure your AWS account with AWS IoT things, credentials, code\-signing certificates, provisioning devices, and OTA update jobs, see [Amazon FreeRTOS Prerequisites](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-prereqs.html)\. 

## MQTT Connection Management<a name="ota-agent-mqtt"></a>

The OTA agent uses the MQTT protocol for all of its communication with AWS IoT services, but it does not manage the MQTT connection\. To assure that the OTA agent does not interfere with the connection management policy of your application, the MQTT connection, including disconnect and any reconnect functionality, must be handled by the main “user” application\. 

## Simple OTA Demo<a name="simple-demo-agent"></a>

The following is an excerpt of a simple OTA demo that shows how the agent connects to the MQTT broker and initializes the OTA agent\. In this example, we configure the demo to use the default OTA completion callback and simply print out some statistics once per second\. For brevity, we leave out some details from this demo\.

For a working example that uses the AWS IoT MQTT broker, see the OTA demo code\.

Because the OTA agent is its own task, the intentional one\-second delay in this example affects this application only\. It has no impact on the performance of the agent\.

```
/* Create the MQTT Client. */
if( MQTT_AGENT_Create( &( xMQTT_h ) ) == eMQTTAgentSuccess )
{
    for ( ; ; )
    {
        memset( &xConnParm, 0, sizeof( xConnParm ) );

        /* ... Set MQTT connection parameters here per your application needs ... */

        configPRINTF( ( "Connecting to %s\r\n", clientcredentialMQTT_BROKER_ENDPOINT ) );
        if( MQTT_AGENT_Connect( xMQTT_h, &( xConnParm ), myappMAX_AWS_CONNECT_WAIT_IN_TICKS ) == eMQTTAgentSuccess )
        {
    		configPRINTF( ( "Connected to broker.\r\n" ) );

	    	/* Initialize the OTA Agent with the default completion callback handler. */
    		OTA_AgentInit( xMQTT_h, ( const uint8_t * ) ( clientcredentialIOT_THING_NAME ), NULL,		/* NULL uses the default
    		callback handler. */ ( TickType_t ) ~0 );

            while( ( eState = OTA_GetAgentState() ) != eOTA_AgentState_NotReady )
            {
                /* Wait forever for OTA traffic but allow other tasks to run
                   and output statistics only once per second. */

                vTaskDelay( myappONE_SECOND_DELAY_IN_TICKS );
                configPRINTF( ( "State: %s  Received: %u   Queued: %u   Processed: %u   Dropped: %u\r\n",
                    pcStateStr[eState],
                    OTA_GetPacketsReceived(),
                    OTA_GetPacketsQueued(),
                    OTA_GetPacketsProcessed(),
                    OTA_GetPacketsDropped() ) );
            }
            /* ... Handle MQTT disconnect per your application needs ... */
        }
        else
        {
            configPRINTF( ( "ERROR:  MQTT_AGENT_Connect() Failed.\r\n" ) );
        }
        /* After failure to connect or a disconnect, wait an arbitrary one second before retry. */
        vTaskDelay( myappONE_SECOND_DELAY_IN_TICKS );
    }
}
else
{
    configPRINTF( ( "Failed to create MQTT client.\r\n" ) );
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

eOTA\_JobEvent\_Activate event  
When this event is received by the callback handler, you can either reset the device immediately or schedule a call to reset the device later\. This allows you to postpone the device reset and self\-test, if necessary\.

eOTA\_JobEvent\_Fail event  
When this event is received by the callback handler, the update has failed\. You do not need to do anything in this case\. You might want to output a log message or do something application\-specific\.

eOTA\_JobEvent\_StartTest event  
The self test phase is meant to allow newly updated firmware to execute and test itself before determining that it is properly functioning and commit it to be the latest permanent application image\. When a new update is received and authenticated and the device has been reset, the OTA agent will send the eOTA\_JobEvent\_StartTest event to the callback function when it is ready for testing\. The developer may choose to add any tests deemed required to determine if the device firmware is functioning properly after update\. When the device firmware is deemed reliable by the self tests, the code must commit the firmware as the new permanent image by calling the OTA\_SetImageState\( eOTA\_ImageState\_Accepted \) function\.

If your device has no special hardware or mechanisms that need to be tested, you can use the default callback handler\. Upon receipt of the eOTA\_JobEvent\_Activate event, the default handler resets the device immediately\.