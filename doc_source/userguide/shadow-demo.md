# AWS IoT Device Shadow demo application<a name="shadow-demo"></a>

## Introduction<a name="shadow-demo-introduction"></a>

This demo shows how to use the AWS IoT Device Shadow library to connect to the [AWS Device Shadow service](https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html)\. It uses the [coreMQTT library](coremqtt.md) to establish an MQTT connection with TLS \(Mutual Authentication\) to the AWS IoT MQTT Broker and the  coreJSON library parser to parse shadow documents received from the AWS Shadow service\. The demo shows basic shadow operations, such as how to update a shadow document and how to delete a shadow document\. The demo also shows how to register a callback function with the coreMQTT library to handle messages like the shadow `/update` and `/update/delta` messages that are sent from the AWS IoT Device Shadow service\.

This demo is intended as a learning exercise only because the request to update the shadow document \(state\) and the update response are done by the same application\. In a realistic production scenario, an external application would request an update of the state of the device remotely, even if the device is not currently connected\. The device will acknowledge the update request when it is connected\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

## Functionality<a name="shadow-demo-functionality"></a>

The demo creates a single application task that loops through a set of examples that demonstrate shadow `/update` and `/update/delta` callbacks to simulate toggling a remote device's state\. It sends a shadow update with the new `desired` state and waits for the device to change its `reported` state in response to the new `desired` state\. In addition, a shadow `/update` callback is used to print the changing shadow states\. This demo also uses a secure MQTT connection to the AWS IoT MQTT Broker, and assumes there is a `powerOn` state in the device shadow\.

The demo performs the following operations:

1. Establish an MQTT connection by using the helper functions in `shadow_demo_helpers.c`\.

1. Assemble MQTT topic strings for device shadow operations, using macros defined by the AWS IoT Device Shadow library\.

1. Publish to the MQTT topic used for deleting a device shadow to delete any existing device shadow\.

1. Subscribe to the MQTT topics for `/update/delta`, `/update/accepted` and `/update/rejected` using helper functions in `shadow_demo_helpers.c`\.

1. Publish a desired state of `powerOn` using helper functions in `shadow_demo_helpers.c`\. This will cause an `/update/delta` message to be sent to the device\.

1. Handle incoming MQTT messages in `prvEventCallback`, and determine whether the message is related to the device shadow by using a function defined by the AWS IoT Device Shadow library \(`Shadow_MatchTopic`\)\. If the message is a device shadow `/update/delta` message, then the main demo function will publish a second message to update the reported state to `powerOn`\. If an `/update/accepted` message is received, verify that it has the same `clientToken` as previously published in the update message\. That will mark the end of the demo\.

![\[shadow demo terminal output\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/shadow-demo-output.png)

The structure of the demo is shown here\.

```
void prvShadowDemoTask( void * pvParameters )
{
    BaseType_t demoStatus = pdPASS;

    /* A buffer containing the update document. It has static duration to 
     * prevent it from being placed on the call stack. */
    static char pcUpdateDocument[ SHADOW_REPORTED_JSON_LENGTH + 1 ] = { 0 };

    demoStatus = xEstablishMqttSession( prvEventCallback );

    if( pdFAIL == demoStatus )
    {
        /* Log error to indicate connection failure. */
        LogError( ( "Failed to connect to MQTT broker." ) );
    }
    else
    {
        /* First of all, try to delete any Shadow document in the cloud. */
        demoStatus = xPublishToTopic( 
                       SHADOW_TOPIC_STRING_DELETE( THING_NAME ),
                       SHADOW_TOPIC_LENGTH_DELETE( THING_NAME_LENGTH ),
                       pcUpdateDocument,
                       0U );

        /* Then try to subscribe to the shadow topics. */
        if( demoStatus == pdPASS )
        {
            demoStatus = xSubscribeToTopic( 
                           SHADOW_TOPIC_STRING_UPDATE_DELTA( THING_NAME ),
                           SHADOW_TOPIC_LENGTH_UPDATE_DELTA( THING_NAME_LENGTH ) );
        }

        if( demoStatus == pdPASS )
        {
            demoStatus = xSubscribeToTopic( 
                           SHADOW_TOPIC_STRING_UPDATE_ACCEPTED( THING_NAME ),
                           SHADOW_TOPIC_LENGTH_UPDATE_ACCEPTED( THING_NAME_LENGTH ) );
        }

        if( demoStatus == pdPASS )
        {
            demoStatus = xSubscribeToTopic( 
                           SHADOW_TOPIC_STRING_UPDATE_REJECTED( THING_NAME ),
                           SHADOW_TOPIC_LENGTH_UPDATE_REJECTED( THING_NAME_LENGTH ) );
        }

        /* This demo uses a constant #THING_NAME known at compile time 
         * therefore we can use macros to assemble shadow topic strings.
         * If the thing name is known at run time, then we could use the API 
         * #Shadow_GetTopicString to assemble shadow topic strings, here is 
         * the example for /update/delta:
         *
         * For /update/delta:
         *
         * #define SHADOW_TOPIC_MAX_LENGTH  (256U)
         *
         * ShadowStatus_t shadowStatus = SHADOW_STATUS_SUCCESS;
         * char cTopicBuffer[ SHADOW_TOPIC_MAX_LENGTH ] = { 0 };
         * uint16_t usBufferSize = SHADOW_TOPIC_MAX_LENGTH;
         * uint16_t usOutLength = 0;
         * const char * pcThingName = "TestThingName";
         * uint16_t usThingNameLength  = ( sizeof( pcThingName ) - 1U );
         *
         * shadowStatus = Shadow_GetTopicString( 
         *                  SHADOW_TOPIC_STRING_TYPE_UPDATE_DELTA,
         *                  pcThingName,
         *                  usThingNameLength,
         *                  & ( cTopicBuffer[ 0 ] ),
         *                  usBufferSize,
         *                  & usOutLength );
         */

        /* Then we publish a desired state to the /update topic. Since we've 
         * deleted the device shadow at the beginning of the demo, this will 
         * cause a delta message to be published, which we have subscribed to.
         * In many real applications, the desired state is not published by
         * the device itself. But for the purpose of making this demo 
         * self-contained, we publish one here so that we can receive a delta 
         * message later.
         */
        if( demoStatus == pdPASS )
        {
            /* Desired power on state . */
            LogInfo( ( "Send desired power state with 1." ) );

            ( void ) memset( pcUpdateDocument,
                             0x00,
                             sizeof( pcUpdateDocument ) );

            snprintf( pcUpdateDocument,
                      SHADOW_DESIRED_JSON_LENGTH + 1,
                      SHADOW_DESIRED_JSON,
                      ( int ) 1,
                      ( long unsigned ) ( xTaskGetTickCount() % 1000000 ) );

            demoStatus = xPublishToTopic( 
                           SHADOW_TOPIC_STRING_UPDATE( THING_NAME ),
                           SHADOW_TOPIC_LENGTH_UPDATE( THING_NAME_LENGTH ),
                           pcUpdateDocument,
                           ( SHADOW_DESIRED_JSON_LENGTH + 1 ) );
        }

        if( demoStatus == pdPASS )
        {
            /* Note that PublishToTopic already called MQTT_ProcessLoop,
             * therefore responses may have been received and the 
             * prvEventCallback may have been called, which may have changed 
             * the stateChanged flag. Check if the state change flag has been 
             * modified or not. If it's modified, then we publish reported 
             * state to update topic.
             */
            if( stateChanged == true )
            {
                /* Report the latest power state back to device shadow. */
                LogInfo( ( "Report to the state change: %d", ulCurrentPowerOnState ) );
                ( void ) memset( pcUpdateDocument,
                                 0x00,
                                 sizeof( pcUpdateDocument ) );

                /* Keep the client token in global variable used to compare if
                 * the same token in /update/accepted. */
                ulClientToken = ( xTaskGetTickCount() % 1000000 );

                snprintf( pcUpdateDocument,
                          SHADOW_REPORTED_JSON_LENGTH + 1,
                          SHADOW_REPORTED_JSON,
                          ( int ) ulCurrentPowerOnState,
                          ( long unsigned ) ulClientToken );

                demoStatus = xPublishToTopic( 
                               SHADOW_TOPIC_STRING_UPDATE( THING_NAME ),
                               SHADOW_TOPIC_LENGTH_UPDATE( THING_NAME_LENGTH ),
                               pcUpdateDocument,
                               ( SHADOW_DESIRED_JSON_LENGTH + 1 ) );
            }
            else
            {
                LogInfo( ( 
                  "No change from /update/delta, unsubscribe all shadow topics and disconnect from MQTT.\r\n" ) );
            }
        }

        if( demoStatus == pdPASS )
        {
            LogInfo( ( "Start to unsubscribe shadow topics and disconnect from MQTT. \r\n" ) );

            demoStatus = xUnsubscribeFromTopic( 
                           SHADOW_TOPIC_STRING_UPDATE_DELTA( THING_NAME ),
                           SHADOW_TOPIC_LENGTH_UPDATE_DELTA( THING_NAME_LENGTH ) );

            if( demoStatus != pdPASS )
            {
                LogError( ( "Failed to unsubscribe the topic %s",
                            SHADOW_TOPIC_STRING_UPDATE_DELTA( THING_NAME ) ) );
            }
        }

        if( demoStatus == pdPASS )
        {
            demoStatus = xUnsubscribeFromTopic( 
                           SHADOW_TOPIC_STRING_UPDATE_ACCEPTED( THING_NAME ),
                           SHADOW_TOPIC_LENGTH_UPDATE_ACCEPTED( THING_NAME_LENGTH ) );

            if( demoStatus != pdPASS )
            {
                LogError( ( "Failed to unsubscribe the topic %s",
                            SHADOW_TOPIC_STRING_UPDATE_ACCEPTED( THING_NAME ) ) );
            }
        }

        if( demoStatus == pdPASS )
        {
            demoStatus = xUnsubscribeFromTopic( 
                           SHADOW_TOPIC_STRING_UPDATE_REJECTED( THING_NAME ),
                           SHADOW_TOPIC_LENGTH_UPDATE_REJECTED( THING_NAME_LENGTH ) );

            if( demoStatus != pdPASS )
            {
                LogError( ( "Failed to unsubscribe the topic %s",
                            SHADOW_TOPIC_STRING_UPDATE_REJECTED( THING_NAME ) ) );
            }
        }

        /* The MQTT session is always disconnected, even there were prior 
         * failures. */
        demoStatus = xDisconnectMqttSession();

        /* This demo performs only Device Shadow operations. If matching the 
         * Shadow MQTT topic fails or there are failure in parsing the 
         * received JSON document, then this demo was not successful. */
        if( ( xUpdateAcceptedReturn != pdPASS ) || ( xUpdateDeltaReturn != pdPASS ) )
        {
            LogError( ( "Callback function failed." ) );
        }

        if( demoStatus == pdPASS )
        {
            LogInfo( ( "Demo completed successfully." ) );
        }
        else
        {
            LogError( ( "Shadow Demo failed." ) );
        }
    }

    /* Delete this task. */
    LogInfo( ( "Deleting Shadow Demo task." ) );
    vTaskDelete( NULL );
}
```

The following screenshot shows the expected output when the demo succeeds\.

![\[shadow demo terminal output showing success\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/shadow-demo-screenshot.png)

## Connect to the AWS IoT MQTT broker<a name="shadow-demo-connect-mqtt"></a>

To connect to the AWS IoT MQTT broker, we use the same method as `MQTT_Connect()` in the [coreMQTT Mutual Authentication demo](mqtt-demo.md)\.

## Delete the shadow document<a name="shadow-demo-delete-document"></a>

To delete the shadow document, call `xPublishToTopic` with an empty message, using macros defined by the AWS IoT Device Shadow library\. This uses `MQTT_Publish` to publish to the `/delete` topic\. The following code section shows how this is done in the function `prvShadowDemoTask`\.

```
/* First of all, try to delete any Shadow document in the cloud. */
returnStatus = PublishToTopic( SHADOW_TOPIC_STRING_DELETE( THING_NAME ),
                               SHADOW_TOPIC_LENGTH_DELETE( THING_NAME_LENGTH ),
                               pcUpdateDocument,
                               0U );
```

## Subscribe to shadow topics<a name="shadow-demo-subscribe"></a>

Subscribe to the Device Shadow topics to receive notifications from the AWS IoT broker about shadow changes\. The Device Shadow topics are assembled by macros defined in the Device Shadow library\. The following code section shows how this is done in the `prvShadowDemoTask` function\.

```
/* Then try to subscribe shadow topics. */
if( returnStatus == EXIT_SUCCESS )
{
    returnStatus = SubscribeToTopic( 
                     SHADOW_TOPIC_STRING_UPDATE_DELTA( THING_NAME ),
                     SHADOW_TOPIC_LENGTH_UPDATE_DELTA( THING_NAME_LENGTH ) );
}

if( returnStatus == EXIT_SUCCESS )
{
    returnStatus = SubscribeToTopic( 
                     SHADOW_TOPIC_STRING_UPDATE_ACCEPTED( THING_NAME ),
                     SHADOW_TOPIC_LENGTH_UPDATE_ACCEPTED( THING_NAME_LENGTH ) );
}

if( returnStatus == EXIT_SUCCESS )
{
    returnStatus = SubscribeToTopic( 
                     SHADOW_TOPIC_STRING_UPDATE_REJECTED( THING_NAME ),
                     SHADOW_TOPIC_LENGTH_UPDATE_REJECTED( THING_NAME_LENGTH ) );
}
```

## Send Shadow Updates<a name="shadow-demo-send-updates"></a>

To send a shadow update, the demo calls `xPublishToTopic` with a message in JSON format, using macros defined by the Device Shadow library\. This uses `MQTT_Publish` to publish to the `/delete` topic\. The following code section shows how this is done in the `prvShadowDemoTask` function\.

```
#define SHADOW_REPORTED_JSON    \
    "{"                         \
    "\"state\":{"               \
    "\"reported\":{"            \
    "\"powerOn\":%01d"          \
    "}"                         \
    "},"                        \
    "\"clientToken\":\"%06lu\"" \
    "}"
snprintf( pcUpdateDocument,
          SHADOW_REPORTED_JSON_LENGTH + 1,
          SHADOW_REPORTED_JSON,
           ( int ) ulCurrentPowerOnState,
           ( long unsigned ) ulClientToken );

xPublishToTopic( SHADOW_TOPIC_STRING_UPDATE( THING_NAME ),
                 SHADOW_TOPIC_LENGTH_UPDATE( THING_NAME_LENGTH ),
                 pcUpdateDocument,
                 ( SHADOW_DESIRED_JSON_LENGTH + 1 ) );
```

## Handle shadow delta messages and shadow update messages<a name="shadow-demo-delta-and-update"></a>

The user callback function, that was registered to the [coreMQTT Client Library](https://freertos-wordpress.corp.amazon.com/shadow/device-shadow-demo.html#handle-shadow-messages) using the `MQTT_Init` function, will notify us about an incoming packet event\. Here's the callback function\.

```
/* This is the callback function invoked by the MQTT stack when it 
 * receives incoming messages. This function demonstrates how to use the 
 * Shadow_MatchTopic function to determine whether the incoming message is 
 * a device shadow message or not. If it is, it handles the message 
 * depending on the message type.
 */
static void prvEventCallback( MQTTContext_t * pxMqttContext,
                              MQTTPacketInfo_t * pxPacketInfo,
                              MQTTDeserializedInfo_t * pxDeserializedInfo )
{
    ShadowMessageType_t messageType = ShadowMessageTypeMaxNum;
    const char * pcThingName = NULL;
    uint16_t usThingNameLength = 0U;
    uint16_t usPacketIdentifier;

    ( void ) pxMqttContext;

    configASSERT( pxDeserializedInfo != NULL );
    configASSERT( pxMqttContext != NULL );
    configASSERT( pxPacketInfo != NULL );

    usPacketIdentifier = pxDeserializedInfo->packetIdentifier;

    /* Handle incoming publish. The lower 4 bits of the publish packet
     * type is used for the dup, QoS, and retain flags. Hence masking
     * out the lower bits to check if the packet is publish. */
    if( ( pxPacketInfo->type & 0xF0U ) == MQTT_PACKET_TYPE_PUBLISH )
    {
        configASSERT( pxDeserializedInfo->pPublishInfo != NULL );
        LogInfo( ( "pPublishInfo->pTopicName:%s.", 
                   pxDeserializedInfo->pPublishInfo->pTopicName ) );

        /* Let the Device Shadow library tell us whether this is a device 
         * shadow message. */
        if( SHADOW_SUCCESS == Shadow_MatchTopic( 
                                pxDeserializedInfo->pPublishInfo->pTopicName,
                                pxDeserializedInfo->pPublishInfo->topicNameLength,
                                &messageType,
                                &pcThingName,
                                &usThingNameLength ) )
        {
            /* Upon successful return, the messageType has been filled in. */
            if( messageType == ShadowMessageTypeUpdateDelta )
            {
                /* Handler function to process payload. */
                prvUpdateDeltaHandler( pxDeserializedInfo->pPublishInfo );
            }
            else if( messageType == ShadowMessageTypeUpdateAccepted )
            {
                /* Handler function to process payload. */
                prvUpdateAcceptedHandler( pxDeserializedInfo->pPublishInfo );
            }
            else if( messageType == ShadowMessageTypeUpdateDocuments )
            {
                LogInfo( ( "/update/documents json payload:%s.", 
                           ( const char * ) pxDeserializedInfo->pPublishInfo->pPayload ) );
            }
            else if( messageType == ShadowMessageTypeUpdateRejected )
            {
                LogInfo( ( "/update/rejected json payload:%s.", 
                           ( const char * ) pxDeserializedInfo->pPublishInfo->pPayload ) );
            }
            else
            {
                LogInfo( ( "Other message type:%d !!", messageType ) );
            }
        }
        else
        {
            LogError( ( "Shadow_MatchTopic parse failed:%s !!", 
                        ( const char * ) pxDeserializedInfo->pPublishInfo->pTopicName ) );
        }
    }
    else
    {
        vHandleOtherIncomingPacket( pxPacketInfo, usPacketIdentifier );
    }
}
```

The callback function confirms the incoming packet is of type `MQTT_PACKET_TYPE_PUBLISH`, and uses the Device Shadow Library API `Shadow_MatchTopic` to confirm that the incoming message is a shadow message\.

If the incoming message is a shadow message with type `ShadowMessageTypeUpdateDelta`, then we call `prvUpdateDeltaHandler` to handle this message\. The handler `prvUpdateDeltaHandler` uses the  coreJSON library to parse the message to get the delta value for the `powerOn` state and compares this against the current device state maintained locally\. If those are different, the local device state is updated to reflect the new value of the `powerOn` state from the shadow document\.

```
static void prvUpdateDeltaHandler( MQTTPublishInfo_t * pxPublishInfo )
{
    static uint32_t ulCurrentVersion = 0; /* Remember the latestVersion # we've ever received */
    uint32_t ulVersion = 0U;
    uint32_t ulNewState = 0U;
    char * pcOutValue = NULL;
    uint32_t ulOutValueLength = 0U;
    JSONStatus_t result = JSONSuccess;

    configASSERT( pxPublishInfo != NULL );
    configASSERT( pxPublishInfo->pPayload != NULL );

    LogInfo( ( "/update/delta json payload:%s.", 
               ( const char * ) pxPublishInfo->pPayload ) );

    /* The payload will look similar to this:
     * {
     *      "version": 12,
     *      "timestamp": 1595437367,
     *      "state": {
     *          "powerOn": 1
     *      },
     *      "metadata": {
     *          "powerOn": {
     *          "timestamp": 1595437367
     *          }
     *      },
     *      "clientToken": "388062"
     *  }
     */

    /* Make sure the payload is a valid json document. */
    result = JSON_Validate( pxPublishInfo->pPayload,
                            pxPublishInfo->payloadLength );

    if( result == JSONSuccess )
    {
        /* Then we start to get the version value by JSON keyword "version". */
        result = JSON_Search( ( char * ) pxPublishInfo->pPayload,
                              pxPublishInfo->payloadLength,
                              "version",
                              sizeof( "version" ) - 1,
                              '.',
                              &pcOutValue,
                              ( size_t * ) &ulOutValueLength );
    }
    else
    {
        LogError( ( "The json document is invalid!!" ) );
    }

    if( result == JSONSuccess )
    {
        LogInfo( ( "version: %.*s",
                   ulOutValueLength,
                   pcOutValue ) );

        /* Convert the extracted value to an unsigned integer value. */
        ulVersion = ( uint32_t ) strtoul( pcOutValue, NULL, 10 );
    }
    else
    {
        LogError( ( "No version in json document!!" ) );
    }

    LogInfo( ( "version:%d, ulCurrentVersion:%d \r\n", 
               ulVersion, ulCurrentVersion ) );

    /* When the version is much newer than the on we retained, that means 
     * the powerOn state is valid for us. */
    if( ulVersion > ulCurrentVersion )
    {
        /* Set to received version as the current version. */
        ulCurrentVersion = ulVersion;

        /* Get powerOn state from json documents. */
        result = JSON_Search( ( char * ) pxPublishInfo->pPayload,
                              pxPublishInfo->payloadLength,
                              "state.powerOn",
                              sizeof( "state.powerOn" ) - 1,
                              '.',
                              &pcOutValue,
                              ( size_t * ) &ulOutValueLength );
    }
    else
    {
        /* In this demo, we discard the incoming message
         * if the version number is not newer than the latest
         * that we've received before. Your application may use a
         * different approach.
         */
        LogWarn( ( "The received version is smaller than current one!!" ) );
    }

    if( result == JSONSuccess )
    {
        /* Convert the powerOn state value to an unsigned integer value. */
        ulNewState = ( uint32_t ) strtoul( pcOutValue, NULL, 10 );

        LogInfo( ( "The new power on state newState:%d, ulCurrentPowerOnState:%d \r\n",
                   ulNewState, ulCurrentPowerOnState ) );

        if( ulNewState != ulCurrentPowerOnState )
        {
            /* The received powerOn state is different from the one we 
             * retained before, so we switch them and set the flag. */
            ulCurrentPowerOnState = ulNewState;

            /* State change will be handled in main(), where we will publish 
             * a "reported" state to the device shadow. We do not do it here 
             * because we are inside of a callback from the MQTT library, so 
             * that we don't re-enter the MQTT library. */
            stateChanged = true;
        }
    }
    else
    {
        LogError( ( "No powerOn in json document!!" ) );
        xUpdateDeltaReturn = pdFAIL;
    }
}
```

If the incoming message is a shadow message with type `ShadowMessageTypeUpdateAccepted`, then we call `prvUpdateAcceptedHandler` to handle this message\. The handler `prvUpdateAcceptedHandler` parses the message using the  coreJSON library to get the `clientToken` from the message\. This handler function checks that the client token from the JSON message matches the client token used by the application\. If it doesn't match, the function logs a warning message\.

```
static void prvUpdateAcceptedHandler( MQTTPublishInfo_t * pxPublishInfo )
{
    char * pcOutValue = NULL;
    uint32_t ulOutValueLength = 0U;
    uint32_t ulReceivedToken = 0U;
    JSONStatus_t result = JSONSuccess;

    assert( pxPublishInfo != NULL );
    assert( pxPublishInfo->pPayload != NULL );

    LogInfo( ( "/update/accepted json payload:%s.", 
               ( const char * ) pxPublishInfo->pPayload ) );

    /* Handle the reported state with state change in /update/accepted topic.
     * Thus we will retrieve the client token from the json document to see if
     * it's the same one we sent with reported state on the /update topic.
     * The payload will look similar to this:
     *  {
     *      "state": {
     *          "reported": {
     *          "powerOn": 1
     *          }
     *      },
     *      "metadata": {
     *          "reported": {
     *          "powerOn": {
     *              "timestamp": 1596573647
     *          }
     *          }
     *      },
     *      "version": 14698,
     *      "timestamp": 1596573647,
     *      "clientToken": "022485"
     *  }
     */

    /* Make sure the payload is a valid json document. */
    result = JSON_Validate( pxPublishInfo->pPayload,
                            pxPublishInfo->payloadLength );

    if( result == JSONSuccess )
    {
        /* Get clientToken from json documents. */
        result = JSON_Search( ( char * ) pxPublishInfo->pPayload,
                              pxPublishInfo->payloadLength,
                              "clientToken",
                              sizeof( "clientToken" ) - 1,
                              '.',
                              &pcOutValue,
                              ( size_t * ) &ulOutValueLength );
    }
    else
    {
        LogError( ( "Invalid json documents !!" ) );
    }

    if( result == JSONSuccess )
    {
        LogInfo( ( "clientToken: %.*s", ulOutValueLength,
                   pcOutValue ) );

        /* Convert the code to an unsigned integer value. */
        ulReceivedToken = ( uint32_t ) strtoul( pcOutValue, NULL, 10 );

        LogInfo( ( "receivedToken:%d, clientToken:%u \r\n", 
                   ulReceivedToken, ulClientToken ) );

        /* If the clientToken in this update/accepted message matches the one 
         * we published before, it means the device shadow has accepted our 
         * latest reported state. We are done. */
        if( ulReceivedToken == ulClientToken )
        {
            LogInfo( ( "Received response from the device shadow. Previously published "
                       "update with clientToken=%u has been accepted. ", ulClientToken ) );
        }
        else
        {
            LogWarn( ( "The received clientToken=%u is not identical with the one=%u we sent ",
                       ulReceivedToken, ulClientToken ) );
        }
    }
    else
    {
        LogError( ( "No clientToken in json document!!" ) );
        lUpdateAcceptedReturn = EXIT_FAILURE;
    }
}
```