# coreMQTT Mutual Authentication demo<a name="mqtt-demo"></a>

## Introduction<a name="mqtt-demo-introduction"></a>

The coreMQTT \(Mutual Authentication\) demo project shows you how to establish a connection to an MQTT broker using TLS with mutual authentication between the client and the server\. This demo uses an mbedTLS\-based transport interface implementation to establish a server and client\-authenticated TLS connection, and demonstrates the subscribe\-publish workflow of MQTT at [ QoS 1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/errata01/os/mqtt-v3.1.1-errata01-os-complete.html#_Toc442180914) level\. After it subscribes to a single topic filter, it publishes to the same topic and waits for receipt of that message back from the server at QoS 1 level\. This cycle of publishing to the broker and receiving the same message back from the broker is repeated indefinitely\. Messages in this demo are sent at QoS 1, which guarantees at least one delivery according to the MQTT spec\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

## Functionality<a name="mqtt-demo-functionality"></a>

The demo creates a single application task that loops through a set of examples that demonstrate how to connect to the broker, subscribe to a topic on the broker, publish to a topic on the broker, then finally, disconnect from the broker\. The demo application both subscribes to and publishes to the same topic\. Each time the demo publishes a message to the MQTT broker, the broker sends the same message back to the demo application\.

A successful completion of the demo will generate an output similar to the following image\.

![\[MQTT demo terminal output on successful completion\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coremqtt-mad-output.png)

The AWS IoT console will generate an output similar to the following image\.

![\[MQTT demo console output on successful completion\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coremqtt-mad-console.png)

The following is the structure of the demo\.

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
```

## Retry logic with exponential backoff and jitter<a name="mqtt-demo-retry-logic"></a>

The [ prvBackoffForRetry](https://github.com/aws/amazon-freertos/blob/master/demos/coreMQTT/mqtt_demo_mutual_auth.c#L652-L698) function shows how failed network operations with the server, for example, TLS connections or MQTT subscribe requests, can be retried with exponential backoff and jitter\. The function calculates the backoff period for the next retry attempt, and performs the backoff delay if the retry attempts have not been exhausted\. Because the calculation of the backoff period requires the generation of a random number, the function uses the PKCS11 module to generate the random number\. Use of the PKCS11 module allows access to a True Random Number Generator \(TRNG\) if the vendor platform supports it\. We recommended that you seed the random number generator with a device\-specific entropy source so that the probability of collisions from devices during connection retries is mitigated\.

## Connecting to the MQTT broker<a name="mqtt-demo-connecting"></a>

The [ prvConnectToServerWithBackoffRetries](https://github.com/aws/amazon-freertos/blob/master/demos/coreMQTT/mqtt_demo_mutual_auth.c#L702-L764) function attempts to make a mutually authenticated TLS connection to the MQTT broker\. If the connection fails, it retries after a backoff period\. The backoff period will exponentially increase until the maximum number of attempts is reached or the maximum backoff period is reached\. The `BackoffAlgorithm_GetNextBackoff` function provides an exponentially increasing backoff value and returns `RetryUtilsRetriesExhausted` when the maximum number of attempts has been reached\. The `prvConnectToServerWithBackoffRetries` function returns a failure status if the TLS connection to the broker can't be established after the configured number of attempts\.

The `prvCreateMQTTConnectionWithBroker` function demonstrates how to establish an MQTT connection to an MQTT broker with a clean session\. It uses the TLS transport interface, which is implemented in the `FreeRTOS-Plus/Source/Application-Protocols/platform/freertos/transport/src/tls_freertos.c` file\. The definition of the `prvCreateMQTTConnectionWithBroker` function is shown below\. Keep in mind that we are setting the keep\-alive seconds for the broker in `xConnectInfo`\.

The next function shows how the TLS transport interface and time function are set in an MQTT context using the `MQTT_Init` function\. It also shows how an event callback function pointer \(`prvEventCallback`\) is set\. This callback is used for reporting incoming messages\.

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
    xResult = MQTT_Init( pxMQTTContext, 
                         &xTransport, 
                         prvGetTimeMs, 
                         prvEventCallback, 
                         &xBuffer );
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

    /* Set MQTT keep-alive period. If the application does not send packets at 
     * an interval less than the keep-alive period, the MQTT library will send 
     * PINGREQ packets. */
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
```

## Subscribing to an MQTT topic<a name="mqtt-demo-subscribing"></a>

The [ prvMQTTSubscribeWithBackoffRetries](https://github.com/aws/amazon-freertos/blob/master/demos/coreMQTT/mqtt_demo_mutual_auth.c#L848-L946) function demonstrates how to subscribe to a topic filter on the MQTT broker\. The example demonstrates how to subscribe to one topic filter, but it's possible to pass a list of topic filters in the same subscribe API call to subscribe to more than one topic filter\. Also, in case the MQTT broker rejects the subscription request, the subscription will retry, with exponential backoff, for `RETRY_MAX_ATTEMPTS`\.

## Publishing to a topic<a name="mqtt-demo-publishing"></a>

The `prvMQTTPublishToTopic` function demonstrates how to publish to a topic filter on the MQTT broker\. The definition of the function is shown below\.

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
```

## Receiving incoming messages<a name="mqtt-demo-receiving"></a>

The application registers an event callback function before it connects to the broker, as described earlier\. The `prvMQTTDemoTask` function calls the `MQTT_ProcessLoop` function to receive incoming messages\. When an incoming MQTT message is received, it calls the event callback function registered by the application\. The `prvEventCallback` function is an example of such an event callback function\. `prvEventCallback` examines the incoming packet type and calls the appropriate handler\. In the example below, the function either calls `prvMQTTProcessIncomingPublish()` for handling incoming publish messages or `prvMQTTProcessResponse()` to handle acknowledgements \(ACK\)\.

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
```

## Processing incoming MQTT publish packets<a name="mqtt-demo-processing"></a>

The `prvMQTTProcessIncomingPublish` functions demonstrates how to process a publish packet from the MQTT broker\. The definition of the function is shown here\.

```
static void prvMQTTProcessIncomingPublish( MQTTPublishInfo_t * pxPublishInfo )
{
    configASSERT( pxPublishInfo != NULL );

    /* Process incoming Publish. */
    LogInfo( ( "Incoming QoS : %d\n", pxPublishInfo->qos ) );

    /* Verify the received publish is for the we have subscribed to. */
    if( ( pxPublishInfo->topicNameLength == strlen( mqttexampleTOPIC ) ) &&
        ( 0 == strncmp( mqttexampleTOPIC, 
                        pxPublishInfo->pTopicName, 
                        pxPublishInfo->topicNameLength ) ) )
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
```

## Unsubscribing from a topic<a name="mqtt-demo-unsubscribing"></a>

The last step in the workflow is to unsubscribe from the topic so that the broker won't send any published messages from `mqttexampleTOPIC`\. The definition of the function is shown here\.

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
```