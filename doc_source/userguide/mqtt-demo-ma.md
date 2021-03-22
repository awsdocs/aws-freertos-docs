# coreMQTT mutual authentication demo<a name="mqtt-demo-ma"></a>

## Introduction<a name="mqtt-demo-ma-introduction"></a>

The coreMQTT mutual authentication demo project shows you how to establish a connection to an MQTT broker using TLS with mutual authentication between the client and the server\. This demo uses an mbedTLS\-based transport interface implementation to establish a server and client\-authenticated TLS connection, and demonstrates the subscribe\-publish workflow of MQTT at [ QoS 1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/errata01/os/mqtt-v3.1.1-errata01-os-complete.html#_Toc442180914) level\. It subscribes to a topic filter, then publishes to topics that match the filter and waits for receipt of those messages back from the server at QoS 1 level\. This cycle of publishing to the broker and receiving the same message back from the broker is repeated indefinitely\. Messages in this demo are sent at QoS 1, which guarantees at least one delivery according to the MQTT spec\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

## Source code<a name="mqtt-demo-ma-source-code"></a>

The demo source file is named `mqtt_demo_mutual_auth.c` and can be found in the `freertos/demos/coreMQTT/` directory and the [GitHub](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_mutual_auth.c) website\.

## Functionality<a name="mqtt-demo-ma-functionality"></a>

The demo creates a single application task that loops through a set of examples that demonstrate how to connect to the broker, subscribe to a topic on the broker, publish to a topic on the broker, then finally, disconnect from the broker\. The demo application both subscribes to and publishes to the same topic\. Each time the demo publishes a message to the MQTT broker, the broker sends the same message back to the demo application\.

A successful completion of the demo will generate an output similar to the following image\.

![\[MQTT demo terminal output on successful completion\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coremqtt-mad-output.png)

The AWS IoT console will generate an output similar to the following image\.

![\[MQTT demo console output on successful completion\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coremqtt-mad-console.png)

## Retry logic with exponential backoff and jitter<a name="mqtt-demo-ma-retry-logic"></a>

The [ `prvBackoffForRetry`](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_mutual_auth.c#L652-L698) function shows how failed network operations with the server, for example, TLS connections or MQTT subscribe requests, can be retried with exponential backoff and jitter\. The function calculates the backoff period for the next retry attempt, and performs the backoff delay if the retry attempts haven't been exhausted\. Because the calculation of the backoff period requires the generation of a random number, the function uses the PKCS11 module to generate the random number\. Use of the PKCS11 module allows access to a True Random Number Generator \(TRNG\) if the vendor platform supports it\. We recommended that you seed the random number generator with a device\-specific entropy source so that the probability of collisions from devices during connection retries is mitigated\.

## Connecting to the MQTT broker<a name="mqtt-demo-ma-connecting"></a>

The [ `prvConnectToServerWithBackoffRetries`](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_mutual_auth.c#L702-L764) function attempts to make a mutually authenticated TLS connection to the MQTT broker\. If the connection fails, it retries after a backoff period\. The backoff period will exponentially increase until the maximum number of attempts is reached or the maximum backoff period is reached\. The `BackoffAlgorithm_GetNextBackoff` function provides an exponentially increasing backoff value and returns `RetryUtilsRetriesExhausted` when the maximum number of attempts has been reached\. The `prvConnectToServerWithBackoffRetries` function returns a failure status if the TLS connection to the broker can't be established after the configured number of attempts\.

The [ `prvCreateMQTTConnectionWithBroker`](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_mutual_auth.c#L767-L825) function demonstrates how to establish an MQTT connection to an MQTT broker with a clean session\. It uses the TLS transport interface, which is implemented in the `FreeRTOS-Plus/Source/Application-Protocols/platform/freertos/transport/src/tls_freertos.c` file\. Keep in mind that we're setting the keep\-alive seconds for the broker in `xConnectInfo`\.

The next function shows how the TLS transport interface and time function are set in an MQTT context using the `MQTT_Init` function\. It also shows how an event callback function pointer \(`prvEventCallback`\) is set\. This callback is used for reporting incoming messages\.

## Subscribing to an MQTT topic<a name="mqtt-demo-ma-subscribing"></a>

The [ `prvMQTTSubscribeWithBackoffRetries`](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_mutual_auth.c#L848-L946) function demonstrates how to subscribe to a topic filter on the MQTT broker\. The example demonstrates how to subscribe to one topic filter, but it's possible to pass a list of topic filters in the same subscribe API call to subscribe to more than one topic filter\. Also, in case the MQTT broker rejects the subscription request, the subscription will retry, with exponential backoff, for `RETRY_MAX_ATTEMPTS`\.

## Publishing to a topic<a name="mqtt-demo-ma-publishing"></a>

The [ `prvMQTTPublishToTopic`](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_mutual_auth.c#L949-L981) function demonstrates how to publish to a topic on the MQTT broker\. 

## Receiving incoming messages<a name="mqtt-demo-ma-receiving"></a>

The application registers an event callback function before it connects to the broker, as described earlier\. The `prvMQTTDemoTask` function calls the `MQTT_ProcessLoop` function to receive incoming messages\. When an incoming MQTT message is received, it calls the event callback function registered by the application\. The [ `prvEventCallback`](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_mutual_auth.c#L1116-L1131) function is an example of such an event callback function\. `prvEventCallback` examines the incoming packet type and calls the appropriate handler\. In the example below, the function either calls `prvMQTTProcessIncomingPublish()` for handling incoming publish messages or `prvMQTTProcessResponse()` to handle acknowledgements \(ACK\)\.

## Processing incoming MQTT publish packets<a name="mqtt-demo-ma-processing"></a>

The [ `prvMQTTProcessIncomingPublish`](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_mutual_auth.c#L1085-L1112) function demonstrates how to process a publish packet from the MQTT broker\. 

## Unsubscribing from a topic<a name="mqtt-demo-ma-unsubscribing"></a>

The last step in the workflow is to unsubscribe from the topic so that the broker won't send any published messages from `mqttexampleTOPIC`\. Here is the definition of the function [ `prvMQTTUnsubscribeFromTopic`](https://github.com/aws/amazon-freertos/blob/202012.00/demos/coreMQTT/mqtt_demo_mutual_auth.c#L984-L1020)\.