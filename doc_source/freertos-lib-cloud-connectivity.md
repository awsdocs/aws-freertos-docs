# Cloud Connectivity<a name="freertos-lib-cloud-connectivity"></a>

**Topics**
+ [MQTT](#freertos-lib-cloud-mqtt)
+ [Device Shadows](#freertos-lib-cloud-shadows)

## MQTT<a name="freertos-lib-cloud-mqtt"></a>

The MQTT agent implements the MQTT protocol, which is a lightweight protocol designed for constrained devices\. The MQTT agent runs in a separate FreeRTOS task and automatically sends regular keep\-alive messages, as documented by the MQTT protocol specification\. All the MQTT APIs are blocking and take a timeout parameter, which is the maximum amount of time the API waits for the corresponding operation to complete\. If the operation does not complete in the provided time, the API returns timeout error code\.

### Callback<a name="freertos-lib-cloud-mqtt-callback"></a>

You can specify an optional callback that is invoked whenever the MQTT agent is disconnected from the broker or whenever a publish message is received from the broker\. The received publish message is stored in a buffer taken from the central buffer pool\. This message is passed to the callback\. This callback runs in the context of the MQTT task and therefore must be quick\. If you need to do longer processing, you must take the ownership of the buffer by returning `pdTRUE` from the callback\. You must then return the buffer back to the pool whenever you are done by calling `FreeRTOS_Agent_ReturnBuffer`\.

### Subscription Management<a name="freertos-lib-cloud-mqtt-sub-mgmt"></a>

Subscription management enables you to register a callback per subscription filter\. You supply this callback while subscribing\. It is invoked whenever a publish message received on a topic matches the subscribed topic filter\. The buffer ownership works the same way as described in the case of generic callback\.

### MQTT Task Wakeup<a name="freertos-lib-cloud-mqtt-wakeup"></a>

MQTT task wakeup wakes up whenever the user calls an API to perform any operation or whenever a publish message is received from the broker\. This asynchronous wakeup upon receipt of a publish message is possible on platforms that are capable of informing the host MCU about the data received on a connected socket\. Platforms that do not have this capability require the MQTT task to continuously poll for the received data on the connected socket\. To ensure the delay between receiving a publish message and invoking the callback is minimal, the `mqttconfigMQTT_TASK_MAX_BLOCK_TICKS` macro controls the maximum time an MQTT task can remain blocked\. This value must be short for the platforms that lack the capability to inform the host MCU about received data on a connected socket\.

### Major Configurations<a name="freertos-lib-cloud-mqtt-config"></a>

These flags can be specified during the MQTT connection request:
+ `mqttconfigKEEP_ALIVE_ACTUAL_INTERVAL_TICKS`: The frequency of the keep\-alive messages sent\.
+ `mqttconfigENABLE_SUBSCRIPTION_MANAGEMENT`: Enable subscription management\.
+ `mqttconfigMAX_BROKERS`: Maximum number of simultaneous MQTT clients\.
+ `mqttconfigMQTT_TASK_STACK_DEPTH`: The task stack depth\.
+ `mqttconfigMQTT_TASK_PRIORITY`: The priority of the MQTT task\.
+ `mqttconfigRX_BUFFER_SIZE`: Length of the buffer used to receive data\.
+ `mqttagentURL_IS_IP_ADDRESS`: Set this bit in `xFlags` if the provided URL is an IP address\.
+ `mqttagentREQUIRE_TLS`: Set this bit in `xFlags` to use TLS\.
+ `mqttagentUSE_AWS_IOT_ALPN_443`: Set this bit in `xFlags` to use AWS IoT support for MQTT over TLS port 443\.

For more information about ALPN, see the [AWS IoT Protocols](http://docs.aws.amazon.com/iot/latest/developerguide/protocols.html) in the AWS IoT Developer Guide and the [MQTT with TLS Client Authentication on Port 443: Why It Is Useful and How It Works](https://aws.amazon.com/blogs/iot/mqtt-with-tls-client-authentication-on-port-443-why-it-is-useful-and-how-it-works) on the Internet of Things on AWS blog\. 

## Device Shadows<a name="freertos-lib-cloud-shadows"></a>

The Amazon FreeRTOS API provides functions to create, delete, and update a device's shadow\. For more information, see [Device Shadows](http://docs.aws.amazon.com/iot/latest/developerguide/iot-thing-shadows.html)\. Device shadows are accessed using the MQTT protocol\. The FreeRTOS device shadow API works with the MQTT API and handles the details of working with the MQTT protocol\.

The Amazon FreeRTOS device shadow APIs define functions to create, update, and delete device shadows\. 

### Prerequisites For Using the Device Shadows API<a name="freertos-lib-cloud-shadows-prereq"></a>

You need to create a thing in AWS IoT, including a certificate and policy\. For more information, see [AWS IoT Getting Started](http://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html)\. You must set values for the following constants in the `AmazonFreeRTOS/demos/common/include/aws_client_credentials.h` file:

`clientcredentialMQTT_BROKER_ENDPOINT`  
Your AWS IoT endpoint\.

`clientcredentialIOT_THING_NAME`  
The name of your IoT thing\.

`clientcredentialWIFI_SSID`  
The SSID of your Wi\-Fi network\.

`clientcredentialWIFI_PASSWORD`  
Your Wi\-Fi password\.

`clientcredentialWIFI_SECURITY`  
The type of Wi\-Fi security used by your network\.

`clientcredentialCLIENT_CERTIFICATE_PEM`  
The certificate PEM associated with your IoT thing\. For more information, see [Configure Your AWS IoT Credentials](getting_started_st.md#ti-configure-credentials)\.

`clientcredentialCLIENT_PRIVATE_KEY_PEM`  
The private key PEM associated with your IoT thing\. For more information, see [Configure Your AWS IoT Credentials](getting_started_st.md#ti-configure-credentials)\.

Make sure the Amazon FreeRTOS MQTT library is installed on your device\. For more information, see [MQTT](#freertos-lib-cloud-mqtt)\. Make sure that the MQTT buffers are large enough to contain the shadow JSON files\. The maximum size for a device shadow document is 8 KB\. All default settings for the device shadow API can be set in the `lib\include\private\aws_shadow_config_defaults.h` file\. You can modify any of these settings in the `demos/<platform>/common/config_files/aws_shadow_config.h` file\.

You must have an IoT thing registered with AWS IoT, including a certificate with a policy that permits accessing the device shadow\. For more information, see [AWS IoT Getting Started](http://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html)\.

### Using the Device Shadow APIs<a name="freertos-lib-cloud-shadows-api"></a>

1. Use the `SHADOW_ClientCreate` API to create a shadow client\. For most applications, the only field to fill is `xCreateParams.xMQTTClientType = eDedicatedMQTTClient`\.

1. Establish an MQTT connection by calling the `SHADOW_ClientConnect` API, passing the client handle returned by `SHADOW_ClientCreate`\.

1. Call the `SHADOW_RegisterCallbacks` API to configure callbacks for shadow update, get, and delete\.

After a connection is established, you can use the following APIs to work with the device shadow:

`SHADOW_Delete`  
Delete the device shadow\.

`SHADOW_Get`  
Get the current device shadow\.

`SHADOW_Update`  
Update the device shadow\.

**Note**  
When you are done working with the device shadow, call `SHADOW_ClientDisconnect` to disconnect the shadow client and free system resources\.