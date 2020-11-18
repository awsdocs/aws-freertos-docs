# AWS IoT Device Shadow demo application<a name="shadow-demo"></a>

## Introduction<a name="shadow-demo-introduction"></a>

This demo shows how to use the AWS IoT Device Shadow library to connect to the [AWS Device Shadow service](https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html)\. It uses the [coreMQTT library](coremqtt.md) to establish an MQTT connection with TLS \(Mutual Authentication\) to the AWS IoT MQTT Broker and the  coreJSON library parser to parse shadow documents received from the AWS Shadow service\. The demo shows basic shadow operations, such as how to update a shadow document and how to delete a shadow document\. The demo also shows how to register a callback function with the coreMQTT library to handle messages like the shadow `/update` and `/update/delta` messages that are sent from the AWS IoT Device Shadow service\.

This demo is intended as a learning exercise only because the request to update the shadow document \(state\) and the update response are done by the same application\. In a realistic production scenario, an external application would request an update of the state of the device remotely, even if the device is not currently connected\. The device will acknowledge the update request when it is connected\.

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

To connect to the AWS IoT MQTT broker, we use the same method as `MQTT_Connect()` in the [ coreMQTT Mutual Authentication democoreMQTT  Add the missing abstract here to help customers find the topic\.    Introduction  The coreMQTT \(Mutual Authentication\) demo project shows you how to establish a connection to an MQTT broker using TLS with mutual authentication between the client and the server\. This demo uses an mbedTLS\-based transport interface implementation to establish a server and client\-authenticated TLS connection, and demonstrates the subscribe\-publish workflow of MQTT at [ QoS 1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/errata01/os/mqtt-v3.1.1-errata01-os-complete.html#_Toc442180914) level\. After it subscribes to a single topic filter, it publishes to the same topic and waits for receipt of that message back from the server at QoS 1 level\. This cycle of publishing to the broker and receiving the same message back from the broker is repeated indefinitely\. Messages in this demo are sent at QoS 1, which guarantees at least one delivery according to the MQTT spec\.   Functionality  The demo creates a single application task that loops through a set of examples that demonstrate how to connect to the broker, subscribe to a topic on the broker, publish to a topic on the broker, then finally, disconnect from the broker\. The demo application both subscribes to and publishes to the same topic\. Each time the demo publishes a message to the MQTT broker, the broker sends the same message back to the demo application\. A successful completion of the demo will generate an output similar to the following image\. 

![\[MQTT demo terminal output on successful completion\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coremqtt-mad-output.png) The AWS IoT console will generate an output similar to the following image\. 

![\[MQTT demo console output on successful completion\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coremqtt-mad-console.png) The following is the structure of the demo\. 

```
static void prvMQTTDemoTask( void * pvParameters )
{
    uint32_t ulPublishCount = 0U, ulTopicCount = 0U;
    const uint32_t ulMaxPublishCount = 5UL;
    NetworkContext_t xNetworkContext = { 0 };
    NetworkCredentials_t xNetworkCredentials = { 0 };
    MQTTContext_t xMQTTContext = { 0 };
    MQTTStatus_t xMQTTStatus;
    TlsTransportStatus_t xNetworkStatus;

    /* Remove compiler warnings about unused parameters. */
    ( void ) pvParameters;

    /* Set the entry time of the demo application. This entry time will be used
     * to calculate relative time elapsed in the execution of the demo application,
     * by the timer utility function that is provided to the MQTT library.
     */
    ulGlobalEntryTimeMs = prvGetTimeMs();

    for( ; ; )
    {
        /****************************** Connect. ******************************/

        /* Attempt to establish TLS session with MQTT broker. If connection fails,
         * retry after a timeout. Timeout value will be exponentially increased
         * until  the maximum number of attempts are reached or the maximum timeout
         * value is reached. The function returns a failure status if the TCP
         * connection cannot be established to the broker after the configured
         * number of attempts. */
        xNetworkStatus = prvConnectToServerWithBackoffRetries( &xNetworkCredentials,
                                                               &xNetworkContext );
        configASSERT( xNetworkStatus == TLS_TRANSPORT_SUCCESS );

        /* Sends an MQTT Connect packet over the already established TLS connection,
         * and waits for connection acknowledgment (CONNACK) packet. */
        LogInfo( ( "Creating an MQTT connection to %s.\r\n", democonfigMQTT_BROKER_ENDPOINT ) );
        prvCreateMQTTConnectionWithBroker( &xMQTTContext, &xNetworkContext );

        /**************************** Subscribe. ******************************/

        /* If server rejected the subscription request, attempt to resubscribe to
         * topic. Attempts are made according to the exponential backoff retry
         * strategy implemented in retryUtils. */
        prvMQTTSubscribeWithBackoffRetries( &xMQTTContext );

        /****************** Publish and Keep Alive Loop. **********************/
        /* Publish messages with QoS1, send and process Keep alive messages. */
        for( ulPublishCount = 0; ulPublishCount < ulMaxPublishCount; ulPublishCount++ )
        {
            LogInfo( ( "Publish to the MQTT topic %s.\r\n", mqttexampleTOPIC ) );
            prvMQTTPublishToTopic( &xMQTTContext );

            /* Process incoming publish echo, since application subscribed to the
             * same topic, the broker will send publish message back to the
             * application. */
            LogInfo( ( "Attempt to receive publish message from broker.\r\n" ) );
            xMQTTStatus = MQTT_ProcessLoop( &xMQTTContext, mqttexamplePROCESS_LOOP_TIMEOUT_MS );
            configASSERT( xMQTTStatus == MQTTSuccess );

            /* Leave Connection Idle for some time. */
            LogInfo( ( "Keeping Connection Idle...\r\n\r\n" ) );
            vTaskDelay( mqttexampleDELAY_BETWEEN_PUBLISHES_TICKS );
        }

        /******************** Unsubscribe from the topic. *********************/
        LogInfo( ( "Unsubscribe from the MQTT topic %s.\r\n", mqttexampleTOPIC ) );
        prvMQTTUnsubscribeFromTopic( &xMQTTContext );

        /* Process incoming UNSUBACK packet from the broker. */
        xMQTTStatus = MQTT_ProcessLoop( &xMQTTContext, mqttexamplePROCESS_LOOP_TIMEOUT_MS );
        configASSERT( xMQTTStatus == MQTTSuccess );

        /**************************** Disconnect. *****************************/

        /* Send an MQTT Disconnect packet over the already connected TLS over
         * TCP connection. There is no corresponding response for the disconnect
         * packet. After sending disconnect, client must close the network
         * connection. */
        LogInfo( ( "Disconnecting the MQTT connection with %s.\r\n",
                   democonfigMQTT_BROKER_ENDPOINT ) );
        xMQTTStatus = MQTT_Disconnect( &xMQTTContext );
        configASSERT( xMQTTStatus == MQTTSuccess );

        /* Close the network connection.  */
        TLS_FreeRTOS_Disconnect( &xNetworkContext );

        /* Reset SUBACK status for each topic filter after completion of
         * subscription request cycle. */
        for( ulTopicCount = 0; ulTopicCount < mqttexampleTOPIC_COUNT; ulTopicCount++ )
        {
            xTopicFilterContext[ ulTopicCount ].xSubAckStatus = MQTTSubAckFailure;
        }

        /* Wait for some time between two iterations to ensure that we do not
         * bombard the broker. */
        LogInfo( ( "prvMQTTDemoTask() completed an iteration successfully. "
                   "Total free heap is %u.\r\n",
                   xPortGetFreeHeapSize() ) );
        LogInfo( ( "Demo completed successfully.\r\n" ) );
        LogInfo( ( "Short delay before starting the next iteration.... \r\n\r\n" ) );
        vTaskDelay( mqttexampleDELAY_BETWEEN_DEMO_ITERATIONS_TICKS );
    }
}
```   Connecting to the MQTT broker  The `prvConnectToServerWithBackoffRetries` function attempts to make a mutually authenticated TLS connection to the MQTT broker\. If the connection fails, it retries after a timeout\. The timeout value will exponentially increase until the maximum number of attempts are reached or the maximum timeout value is reached\. The `RetryUtils_BackoffAndSleep` function provides exponentially increasing timeout value and returns `RetryUtilsRetriesExhausted` when the maximum number of attempts have been reached\. The `prvConnectToServerWithBackoffRetries` function returns a failure status if the TLS connection can't be established to the broker after the configured number of attempts\. 

```
static TlsTransportStatus_t prvConnectToServerWithBackoffRetries( NetworkCredentials_t * pxNetworkCredentials,
                                                                  NetworkContext_t * pxNetworkContext )
{
    TlsTransportStatus_t xNetworkStatus;
    RetryUtilsStatus_t xRetryUtilsStatus = RetryUtilsSuccess;
    RetryUtilsParams_t xReconnectParams;

    #ifdef democonfigUSE_AWS_IOT_CORE_BROKER

        /* ALPN protocols must be a NULL-terminated list of strings. Therefore,
         * the first entry will contain the actual ALPN protocol string while the
         * second entry must remain NULL. */
        char * pcAlpnProtocols[] = { NULL, NULL };

        /* The ALPN string changes depending on whether username/password authentication is used. */
        #ifdef democonfigCLIENT_USERNAME
            pcAlpnProtocols[ 0 ] = AWS_IOT_CUSTOM_AUTH_ALPN;
        #else
            pcAlpnProtocols[ 0 ] = AWS_IOT_MQTT_ALPN;
        #endif
        pxNetworkCredentials->pAlpnProtos = pcAlpnProtocols;
    #endif /* ifdef democonfigUSE_AWS_IOT_CORE_BROKER */

    pxNetworkCredentials->disableSni = democonfigDISABLE_SNI;
    /* Set the credentials for establishing a TLS connection. */
    pxNetworkCredentials->pRootCa = ( const unsigned char * ) democonfigROOT_CA_PEM;
    pxNetworkCredentials->rootCaSize = sizeof( democonfigROOT_CA_PEM );
    #ifdef democonfigCLIENT_CERTIFICATE_PEM
        pxNetworkCredentials->pClientCert = ( const unsigned char * ) democonfigCLIENT_CERTIFICATE_PEM;
        pxNetworkCredentials->clientCertSize = sizeof( democonfigCLIENT_CERTIFICATE_PEM );
        pxNetworkCredentials->pPrivateKey = ( const unsigned char * ) democonfigCLIENT_PRIVATE_KEY_PEM;
        pxNetworkCredentials->privateKeySize = sizeof( democonfigCLIENT_PRIVATE_KEY_PEM );
    #endif
    /* Initialize reconnect attempts and interval. */
    RetryUtils_ParamsReset( &xReconnectParams );
    xReconnectParams.maxRetryAttempts = MAX_RETRY_ATTEMPTS;

    /* Attempt to connect to MQTT broker. If connection fails, retry after
     * a timeout. Timeout value will exponentially increase till maximum
     * attempts are reached.
     */
    do
    {
        /* Establish a TLS session with the MQTT broker. This example connects to
         * the MQTT broker as specified in democonfigMQTT_BROKER_ENDPOINT and
         * democonfigMQTT_BROKER_PORT at the top of this file. */
        LogInfo( ( "Creating a TLS connection to %s:%u.\r\n",
                   democonfigMQTT_BROKER_ENDPOINT,
                   democonfigMQTT_BROKER_PORT ) );
        /* Attempt to create a mutually authenticated TLS connection. */
        xNetworkStatus = TLS_FreeRTOS_Connect( pxNetworkContext,
                                               democonfigMQTT_BROKER_ENDPOINT,
                                               democonfigMQTT_BROKER_PORT,
                                               pxNetworkCredentials,
                                               mqttexampleTRANSPORT_SEND_RECV_TIMEOUT_MS,
                                               mqttexampleTRANSPORT_SEND_RECV_TIMEOUT_MS );

        if( xNetworkStatus != TLS_TRANSPORT_SUCCESS )
        {
            LogWarn( ( "Connection to the broker failed. Retrying connection with backoff and jitter." ) );
            xRetryUtilsStatus = RetryUtils_BackoffAndSleep( &xReconnectParams );
        }

        if( xRetryUtilsStatus == RetryUtilsRetriesExhausted )
        {
            LogError( ( "Connection to the broker failed, all attempts exhausted." ) );
            xNetworkStatus = TLS_TRANSPORT_CONNECT_FAILURE;
        }
    } while( ( xNetworkStatus != TLS_TRANSPORT_SUCCESS ) && ( xRetryUtilsStatus == RetryUtilsSuccess ) );

    return xNetworkStatus;
}
``` The `prvCreateMQTTConnectionWithBroker` function demonstrates how to establish an MQTT connection to an MQTT broker with a clean session\. It uses the TLS transport interface, which is implemented in the `FreeRTOS-Plus/Source/Application-Protocols/platform/freertos/transport/src/tls_freertos.c` file\. The definition of the `prvCreateMQTTConnectionWithBroker` function is shown below\. Keep in mind that we are setting the keep\-alive seconds for the broker in `xConnectInfo`\. The next function shows how the TLS transport interface and time function are set in an MQTT context using the `MQTT_Init` function\. It also shows how an event callback function pointer \(`prvEventCallback`\) is set\. This callback is used for reporting incoming messages\. 

```
static void prvCreateMQTTConnectionWithBroker( MQTTContext_t * pxMQTTContext,
                                               NetworkContext_t * pxNetworkContext )
{
    MQTTStatus_t xResult;
    MQTTConnectInfo_t xConnectInfo;
    bool xSessionPresent;
    TransportInterface_t xTransport;

    /***
     * For readability, error handling in this function is restricted to the use of
     * asserts().
     ***/

    /* Fill in Transport Interface send and receive function pointers. */
    xTransport.pNetworkContext = pxNetworkContext;
    xTransport.send = TLS_FreeRTOS_send;
    xTransport.recv = TLS_FreeRTOS_recv;

    /* Initialize MQTT library. */
    xResult = MQTT_Init( pxMQTTContext, &xTransport, prvGetTimeMs, prvEventCallback, &xBuffer );
    configASSERT( xResult == MQTTSuccess );

    /* Some fields are not used in this demo so start with everything at 0. */
    ( void ) memset( ( void * ) &xConnectInfo, 0x00, sizeof( xConnectInfo ) );

    /* Start with a clean session i.e. direct the MQTT broker to discard any
     * previous session data. Also, establishing a connection with clean session
     * will ensure that the broker does not store any data when this client
     * gets disconnected. */
    xConnectInfo.cleanSession = true;

    /* The client identifier is used to uniquely identify this MQTT client to
     * the MQTT broker. In a production device the identifier can be something
     * unique, such as a device serial number. */
    xConnectInfo.pClientIdentifier = democonfigCLIENT_IDENTIFIER;
    xConnectInfo.clientIdentifierLength = ( uint16_t ) strlen( democonfigCLIENT_IDENTIFIER );

    /* Set MQTT keep-alive period. If the application does not send packets at an interval less than
     * the keep-alive period, the MQTT library will send PINGREQ packets. */
    xConnectInfo.keepAliveSeconds = mqttexampleKEEP_ALIVE_TIMEOUT_SECONDS;

    /* Append metrics when connecting to the AWS IoT Core broker. */
    #ifdef democonfigUSE_AWS_IOT_CORE_BROKER
        #ifdef democonfigCLIENT_USERNAME
            xConnectInfo.pUserName = CLIENT_USERNAME_WITH_METRICS;
            xConnectInfo.userNameLength = ( uint16_t ) strlen( CLIENT_USERNAME_WITH_METRICS );
            xConnectInfo.pPassword = democonfigCLIENT_PASSWORD;
            xConnectInfo.passwordLength = ( uint16_t ) strlen( democonfigCLIENT_PASSWORD );
        #else
            xConnectInfo.pUserName = AWS_IOT_METRICS_STRING;
            xConnectInfo.userNameLength = AWS_IOT_METRICS_STRING_LENGTH;
            /* Password for authentication is not used. */
            xConnectInfo.pPassword = NULL;
            xConnectInfo.passwordLength = 0U;
        #endif
    #else /* ifdef democonfigUSE_AWS_IOT_CORE_BROKER */
        #ifdef democonfigCLIENT_USERNAME
            xConnectInfo.pUserName = democonfigCLIENT_USERNAME;
            xConnectInfo.userNameLength = ( uint16_t ) strlen( democonfigCLIENT_USERNAME );
            xConnectInfo.pPassword = democonfigCLIENT_PASSWORD;
            xConnectInfo.passwordLength = ( uint16_t ) strlen( democonfigCLIENT_PASSWORD );
        #endif /* ifdef democonfigCLIENT_USERNAME */
    #endif /* ifdef democonfigUSE_AWS_IOT_CORE_BROKER */

    /* Send MQTT CONNECT packet to broker. LWT is not used in this demo, so it
     * is passed as NULL. */
    xResult = MQTT_Connect( pxMQTTContext,
                            &xConnectInfo,
                            NULL,
                            mqttexampleCONNACK_RECV_TIMEOUT_MS,
                            &xSessionPresent );
    configASSERT( xResult == MQTTSuccess );

    /* Successfully established and MQTT connection with the broker. */
    LogInfo( ( "An MQTT connection is established with %s.", democonfigMQTT_BROKER_ENDPOINT ) );
}
```   Subscribing to an MQTT topic  The `prvMQTTSubscribeWithBackoffRetries` function demonstrates how to subscribe to a topic filter on the MQTT broker\. The example demonstrates how to subscribe to one topic filter, but it's possible to pass a list of topic filters in the same subscribe API call to subscribe to more than one topic filter\. Also, in case the MQTT broker rejects the subscription request, the subscription will retry for `MAX_RETRY_ATTEMPTS`\. The definition of the function is shown here\. 

```
static void prvMQTTSubscribeWithBackoffRetries( MQTTContext_t * pxMQTTContext )
{
    MQTTStatus_t xResult = MQTTSuccess;
    RetryUtilsStatus_t xRetryUtilsStatus = RetryUtilsSuccess;
    RetryUtilsParams_t xRetryParams;
    MQTTSubscribeInfo_t xMQTTSubscription[ mqttexampleTOPIC_COUNT ];
    bool xFailedSubscribeToTopic = false;
    uint32_t ulTopicCount = 0U;

    /* Some fields not used by this demo so start with everything at 0. */
    ( void ) memset( ( void * ) &xMQTTSubscription, 0x00, sizeof( xMQTTSubscription ) );

    /* Get a unique packet id. */
    usSubscribePacketIdentifier = MQTT_GetPacketId( pxMQTTContext );

    /* Subscribe to the mqttexampleTOPIC topic filter. This example subscribes to
     * only one topic and uses QoS1. */
    xMQTTSubscription[ 0 ].qos = MQTTQoS1;
    xMQTTSubscription[ 0 ].pTopicFilter = mqttexampleTOPIC;
    xMQTTSubscription[ 0 ].topicFilterLength = ( uint16_t ) strlen( mqttexampleTOPIC );

    /* Initialize retry attempts and interval. */
    RetryUtils_ParamsReset( &xRetryParams );
    xRetryParams.maxRetryAttempts = MAX_RETRY_ATTEMPTS;

    do
    {
        /* The client is now connected to the broker. Subscribe to the topic
         * as specified in mqttexampleTOPIC at the top of this file by sending a
         * subscribe packet then waiting for a subscribe acknowledgment (SUBACK).
         * This client will then publish to the same topic it subscribed to, so it
         * will expect all the messages it sends to the broker to be sent back to it
         * from the broker. This demo uses QOS0 in Subscribe, therefore, the Publish
         * messages received from the broker will have QOS0. */
        LogInfo( ( "Attempt to subscribe to the MQTT topic %s.\r\n", mqttexampleTOPIC ) );
        xResult = MQTT_Subscribe( pxMQTTContext,
                                  xMQTTSubscription,
                                  sizeof( xMQTTSubscription ) / sizeof( MQTTSubscribeInfo_t ),
                                  usSubscribePacketIdentifier );
        configASSERT( xResult == MQTTSuccess );

        LogInfo( ( "SUBSCRIBE sent for topic %s to broker.\n\n", mqttexampleTOPIC ) );

        /* Process incoming packet from the broker. After sending the subscribe, the
         * client may receive a publish before it receives a subscribe ack. Therefore,
         * call generic incoming packet processing function. Since this demo is
         * subscribing to the topic to which no one is publishing, probability of
         * receiving Publish message before subscribe ack is zero; but application
         * must be ready to receive any packet.  This demo uses the generic packet
         * processing function everywhere to highlight this fact. */
        xResult = MQTT_ProcessLoop( pxMQTTContext, mqttexamplePROCESS_LOOP_TIMEOUT_MS );
        configASSERT( xResult == MQTTSuccess );

        /* Reset flag before checking suback responses. */
        xFailedSubscribeToTopic = false;

        /* Check if recent subscription request has been rejected. #xTopicFilterContext is updated
         * in the event callback to reflect the status of the SUBACK sent by the broker. It represents
         * either the QoS level granted by the server upon subscription, or acknowledgement of
         * server rejection of the subscription request. */
        for( ulTopicCount = 0; ulTopicCount < mqttexampleTOPIC_COUNT; ulTopicCount++ )
        {
            if( xTopicFilterContext[ ulTopicCount ].xSubAckStatus == MQTTSubAckFailure )
            {
               LogWarn( ( "Server rejected subscription request. Attempting to re-subscribe to topic %s.",
                          xTopicFilterContext[ ulTopicCount ].pcTopicFilter ) );
               xFailedSubscribeToTopic = true;
               xRetryUtilsStatus = RetryUtils_BackoffAndSleep( &xRetryParams );
               break;
            }
        }

        configASSERT( xRetryUtilsStatus != RetryUtilsRetriesExhausted );
    } while( ( xFailedSubscribeToTopic == true ) && ( xRetryUtilsStatus == RetryUtilsSuccess ) );
}
```   Publishing to a topic  The `prvMQTTPublishToTopic` function demonstrates how to publish to a topic filter on the MQTT broker\. The definition of the function is shown below\. 

```
static void prvMQTTPublishToTopic( MQTTContext_t * pxMQTTContext )
{
    MQTTStatus_t xResult;
    MQTTPublishInfo_t xMQTTPublishInfo;

    /***
     * For readability, error handling in this function is restricted to the use of
     * asserts().
     ***/

    /* Some fields are not used by this demo so start with everything at 0. */
    ( void ) memset( ( void * ) &xMQTTPublishInfo, 0x00, sizeof( xMQTTPublishInfo ) );

    /* This demo uses QoS1. */
    xMQTTPublishInfo.qos = MQTTQoS1;
    xMQTTPublishInfo.retain = false;
    xMQTTPublishInfo.pTopicName = mqttexampleTOPIC;
    xMQTTPublishInfo.topicNameLength = ( uint16_t ) strlen( mqttexampleTOPIC );
    xMQTTPublishInfo.pPayload = mqttexampleMESSAGE;
    xMQTTPublishInfo.payloadLength = strlen( mqttexampleMESSAGE );

    /* Get a unique packet id. */
    usPublishPacketIdentifier = MQTT_GetPacketId( pxMQTTContext );

    /* Send PUBLISH packet. Packet ID is not used for a QoS1 publish. */
    xResult = MQTT_Publish( pxMQTTContext, &xMQTTPublishInfo, usPublishPacketIdentifier );

    configASSERT( xResult == MQTTSuccess );
}
```   Receiving incoming messages  The application registers an event callback function before it connects to the broker, as described earlier\. The `prvMQTTDemoTask` function calls the `MQTT_ProcessLoop` function to receive incoming messages\. When an incoming MQTT message is received, it calls the event callback function registered by the application\. The `prvEventCallback` function is an example of such an event callback function\. `prvEventCallback` examines the incoming packet type and calls the appropriate handler\. In the example below, the function either calls `prvMQTTProcessIncomingPublish()` for handling incoming publish messages or `prvMQTTProcessResponse()` to handle acknowledgements \(ACK\)\. 

```
static void prvEventCallback( MQTTContext_t * pxMQTTContext,
                              MQTTPacketInfo_t * pxPacketInfo,
                              MQTTDeserializedInfo_t * pxDeserializedInfo )
{
    /* The MQTT context is not used for this demo. */
    ( void ) pxMQTTContext;

    if( ( pxPacketInfo->type & 0xF0U ) == MQTT_PACKET_TYPE_PUBLISH )
    {
        prvMQTTProcessIncomingPublish( pxDeserializedInfo->pPublishInfo );
    }
    else
    {
        prvMQTTProcessResponse( pxPacketInfo, pxDeserializedInfo->packetIdentifier );
    }
}
```   Processing incoming MQTT publish packets  The `prvMQTTProcessIncomingPublish` functions demonstrates how to process a publish packet from the MQTT broker\. The definition of the function is shown here\. 

```
static void prvMQTTProcessIncomingPublish( MQTTPublishInfo_t * pxPublishInfo )
{
    configASSERT( pxPublishInfo != NULL );

    /* Process incoming Publish. */
    LogInfo( ( "Incoming QoS : %d\n", pxPublishInfo->qos ) );

    /* Verify the received publish is for the we have subscribed to. */
    if( ( pxPublishInfo->topicNameLength == strlen( mqttexampleTOPIC ) ) &&
        ( 0 == strncmp( mqttexampleTOPIC, pxPublishInfo->pTopicName, pxPublishInfo->topicNameLength ) ) )
    {
        LogInfo( ( "\r\nIncoming Publish Topic Name: %.*s matches subscribed topic.\r\n"
                   "Incoming Publish Message : %.*s\r\n",
                   pxPublishInfo->topicNameLength,
                   pxPublishInfo->pTopicName,
                   pxPublishInfo->payloadLength,
                   pxPublishInfo->pPayload ) );
    }
    else
    {
        LogInfo( ( "Incoming Publish Topic Name: %.*s does not match subscribed topic.\r\n",
                   pxPublishInfo->topicNameLength,
                   pxPublishInfo->pTopicName ) );
    }
}
```   Unsubscribing from a topic  The last step in the workflow is to unsubscribe from the topic so that the broker won't send any published messages from `mqttexampleTOPIC`\. The definition of the function is shown here\. 

```
static void prvMQTTUnsubscribeFromTopic( MQTTContext_t * pxMQTTContext )
{
    MQTTStatus_t xResult;
    MQTTSubscribeInfo_t xMQTTSubscription[ mqttexampleTOPIC_COUNT ];

    /* Some fields not used by this demo so start with everything at 0. */
    ( void ) memset( ( void * ) &xMQTTSubscription, 0x00, sizeof( xMQTTSubscription ) );

    /* Get a unique packet id. */
    usSubscribePacketIdentifier = MQTT_GetPacketId( pxMQTTContext );

    /* Subscribe to the mqttexampleTOPIC topic filter. This example subscribes to
     * only one topic and uses QoS1. */
    xMQTTSubscription[ 0 ].qos = MQTTQoS1;
    xMQTTSubscription[ 0 ].pTopicFilter = mqttexampleTOPIC;
    xMQTTSubscription[ 0 ].topicFilterLength = ( uint16_t ) strlen( mqttexampleTOPIC );

    /* Get next unique packet identifier. */
    usUnsubscribePacketIdentifier = MQTT_GetPacketId( pxMQTTContext );

    /* Send UNSUBSCRIBE packet. */
    xResult = MQTT_Unsubscribe( pxMQTTContext,
                                xMQTTSubscription,
                                sizeof( xMQTTSubscription ) / sizeof( MQTTSubscribeInfo_t ),
                                usUnsubscribePacketIdentifier );

    configASSERT( xResult == MQTTSuccess );
}
```  ](mqtt-demo.md)\.

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