# coreMQTT mutual authentication demo<a name="mqtt-demo-ma"></a>

## Introduction<a name="mqtt-demo-ma-introduction"></a>

The coreMQTT mutual authentication demo project shows you how to establish a connection to an MQTT broker using TLS with mutual authentication between the client and the server\. This demo uses an mbedTLS\-based transport interface implementation to establish a server and client\-authenticated TLS connection, and demonstrates the subscribe\-publish workflow of MQTT at [ QoS 1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/errata01/os/mqtt-v3.1.1-errata01-os-complete.html#_Toc442180914) level\. It subscribes to a topic filter, then publishes to topics that match the filter and waits for receipt of those messages back from the server at QoS 1 level\. This cycle of publishing to the broker and receiving the same message back from the broker is repeated indefinitely\. Messages in this demo are sent at QoS 1, which guarantees at least one delivery according to the MQTT spec\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

## Source code<a name="mqtt-demo-ma-source-code"></a>

The demo source file is named `mqtt_demo_mutual_auth.c` and can be found in the `freertos/demos/coreMQTT/` directory and the [ GitHub](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT/mqtt_demo_mutual_auth.c) website\.

## Functionality<a name="mqtt-demo-ma-functionality"></a>

The demo creates a single application task that loops through a set of examples that demonstrate how to connect to the broker, subscribe to a topic on the broker, publish to a topic on the broker, then finally, disconnect from the broker\. The demo application both subscribes to and publishes to the same topic\. Each time the demo publishes a message to the MQTT broker, the broker sends the same message back to the demo application\.

A successful completion of the demo will generate an output similar to the following image\.

![\[MQTT demo terminal output on successful completion\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coremqtt-mad-output.png)

The AWS IoT console will generate an output similar to the following image\.

![\[MQTT demo console output on successful completion\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/coremqtt-mad-console.png)

## Retry logic with exponential backoff and jitter<a name="mqtt-demo-ma-retry-logic"></a>

The [ prvBackoffForRetry](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT/mqtt_demo_mutual_auth.c#L671-L717) function shows how failed network operations with the server, for example, TLS connections or MQTT subscribe requests, can be retried with exponential backoff and jitter\. The function calculates the backoff period for the next retry attempt, and performs the backoff delay if the retry attempts haven't been exhausted\. Because the calculation of the backoff period requires the generation of a random number, the function uses the PKCS11 module to generate the random number\. Use of the PKCS11 module allows access to a True Random Number Generator \(TRNG\) if the vendor platform supports it\. We recommended that you seed the random number generator with a device\-specific entropy source so that the probability of collisions from devices during connection retries is mitigated\.

## Connecting to the MQTT broker<a name="mqtt-demo-ma-connecting"></a>

The [ prvConnectToServerWithBackoffRetries](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT/mqtt_demo_mutual_auth.c#L721-L782) function attempts to make a mutually authenticated TLS connection to the MQTT broker\. If the connection fails, it retries after a backoff period\. The backoff period will exponentially increase until the maximum number of attempts is reached or the maximum backoff period is reached\. The `BackoffAlgorithm_GetNextBackoff` function provides an exponentially increasing backoff value and returns `RetryUtilsRetriesExhausted` when the maximum number of attempts has been reached\. The `prvConnectToServerWithBackoffRetries` function returns a failure status if the TLS connection to the broker can't be established after the configured number of attempts\.

The [ prvCreateMQTTConnectionWithBroker](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT/mqtt_demo_mutual_auth.c#L785-L848) function demonstrates how to establish an MQTT connection to an MQTT broker with a clean session\. It uses the TLS transport interface, which is implemented in the `FreeRTOS-Plus/Source/Application-Protocols/platform/freertos/transport/src/tls_freertos.c` file\. Keep in mind that we're setting the keep\-alive seconds for the broker in `xConnectInfo`\.

The next function shows how the TLS transport interface and time function are set in an MQTT context using the `MQTT_Init` function\. It also shows how an event callback function pointer \(`prvEventCallback`\) is set\. This callback is used for reporting incoming messages\.

## Subscribing to an MQTT topic<a name="mqtt-demo-ma-subscribing"></a>

The [ prvMQTTSubscribeWithBackoffRetries](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT/mqtt_demo_mutual_auth.c#L871-L969) function demonstrates how to subscribe to a topic filter on the MQTT broker\. The example demonstrates how to subscribe to one topic filter, but it's possible to pass a list of topic filters in the same subscribe API call to subscribe to more than one topic filter\. Also, in case the MQTT broker rejects the subscription request, the subscription will retry, with exponential backoff, for `RETRY_MAX_ATTEMPTS`\.

## Publishing to a topic<a name="mqtt-demo-ma-publishing"></a>

The [ prvMQTTPublishToTopic](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT/mqtt_demo_mutual_auth.c#L972-L1004) function demonstrates how to publish to a topic on the MQTT broker\. 

## Receiving incoming messages<a name="mqtt-demo-ma-receiving"></a>

The application registers an event callback function before it connects to the broker, as described earlier\. The `prvMQTTDemoTask` function calls the `MQTT_ProcessLoop` function to receive incoming messages\. When an incoming MQTT message is received, it calls the event callback function registered by the application\. The [ prvEventCallback](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT/mqtt_demo_mutual_auth.c#L1139-L1154) function is an example of such an event callback function\. `prvEventCallback` examines the incoming packet type and calls the appropriate handler\. In the example below, the function either calls `prvMQTTProcessIncomingPublish()` for handling incoming publish messages or `prvMQTTProcessResponse()` to handle acknowledgements \(ACK\)\.

## Processing incoming MQTT publish packets<a name="mqtt-demo-ma-processing"></a>

The [ prvMQTTProcessIncomingPublish](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT/mqtt_demo_mutual_auth.c#L1108-L1135) function demonstrates how to process a publish packet from the MQTT broker\. 

## Unsubscribing from a topic<a name="mqtt-demo-ma-unsubscribing"></a>

The last step in the workflow is to unsubscribe from the topic so that the broker won't send any published messages from `mqttexampleTOPIC`\. Here is the definition of the function [ prvMQTTUnsubscribeFromTopic](https://github.com/aws/amazon-freertos/blob/main/demos/coreMQTT/mqtt_demo_mutual_auth.c#L1007-L1043)\.

## Changing the root CA used in the demo<a name="mqtt-demo-ma-root-ca"></a>

By default, the FreeRTOS demos use the Amazon Root CA 1 certificate \(RSA 2048 bit key\) to authenticate with the AWS IoT Core server\. It is possible to use other [ CA certificates for server authentication](https://docs.aws.amazon.com/iot/latest/developerguide/server-authentication.html#server-authentication-certs), including the Amazon Root CA 3 certificate \(ECC 256 bit key\)\. To change the root CA for the coreMQTT mutual authentication demo: 

1. In a text editor, open the `freertos/vendors/vendor/boards/board/aws_demos/config_files/mqtt_demo_mutual_auth_config.h` file\.

1. In the file, locate the following line\.

   ```
    * #define democonfigROOT_CA_PEM    "...insert here..." 
   ```

   Uncomment this line and, if necessary, move it past the comment block end ` */`\.

1. Copy the CA certificate that you want to use and then paste it in the `"...insert here..."` text\. The result should look like the following example\.

   ```
   #define democonfigROOT_CA_PEM   "-----BEGIN CERTIFICATE-----\n"\
   "MIIBtjCCAVugAwIBAgITBmyf1XSXNmY/Owua2eiedgPySjAKBggqhkjOPQQDAjA5\n"\
   "MQswCQYDVQQGEwJVUzEPMA0GA1UEChMGQW1hem9uMRkwFwYDVQQDExBBbWF6b24g\n"\
   "Um9vdCBDQSAzMB4XDTE1MDUyNjAwMDAwMFoXDTQwMDUyNjAwMDAwMFowOTELMAkG\n"\
   "A1UEBhMCVVMxDzANBgNVBAoTBkFtYXpvbjEZMBcGA1UEAxMQQW1hem9uIFJvb3Qg\n"\
   "Q0EgMzBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABCmXp8ZBf8ANm+gBG1bG8lKl\n"\
   "ui2yEujSLtf6ycXYqm0fc4E7O5hrOXwzpcVOho6AF2hiRVd9RFgdszflZwjrZt6j\n"\
   "QjBAMA8GA1UdEwEB/wQFMAMBAf8wDgYDVR0PAQH/BAQDAgGGMB0GA1UdDgQWBBSr\n"\
   "ttvXBp43rDCGB5Fwx5zEGbF4wDAKBggqhkjOPQQDAgNJADBGAiEA4IWSoxe3jfkr\n"\
   "BqWTrBqYaGFy+uGh0PsceGCmQ5nFuMQCIQCcAu/xlJyzlvnrxir4tiz+OpAUFteM\n"\
   "YyRIHN8wfdVoOw==\n"\
   "-----END CERTIFICATE-----\n"
   ```

1. \(Optional\) You can change the root CA for other demos\. Repeat steps 1 through 3 for each `freertos/vendors/vendor/boards/board/aws_demos/config_files/demo-name_config.h` file\.