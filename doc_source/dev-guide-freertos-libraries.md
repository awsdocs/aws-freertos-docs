# Amazon FreeRTOS Libraries<a name="dev-guide-freertos-libraries"></a>

Amazon FreeRTOS libraries provide additional functionality to the FreeRTOS kernel and its internal libraries\. You can use Amazon FreeRTOS libraries for networking and security in embedded applications\. Amazon FreeRTOS libraries also enable your applications to interact with AWS IoT services\.

You can download versions of Amazon FreeRTOS that are configured for Amazon FreeRTOS\-qualified platforms from the [Amazon FreeRTOS console]()\. For a list of qualified platforms, see the [Amazon FreeRTOS Partners](https://aws.amazon.com/freertos/partners/) website\. Amazon FreeRTOS is also available on [GitHub](https://github.com/aws/amazon-freertos)\.

## Amazon FreeRTOS Porting Libraries<a name="dev-guide-freertos-porting-libraries"></a>

The following porting libraries are included in configurations of Amazon FreeRTOS that are available for download on the Amazon FreeRTOS console\. These libraries are platform\-dependent\. Their contents change according to your hardware platform\.


**Amazon FreeRTOS Porting Libraries**  

| Library | API Reference | Description | 
| --- | --- | --- | 
| Bluetooth Low Energy | [Bluetooth Low Energy \(BLE\) API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html4/bt__hal__gatt__client_8h.html) | Using the Amazon FreeRTOS Bluetooth Low Energy \(BLE\) library, your microcontroller can communicate with the AWS IoT MQTT broker through a gateway device\. For more information, see [Amazon FreeRTOS Bluetooth Low Energy Library \(Beta\)](freertos-ble-library.md)\.  The Amazon FreeRTOS BLE library is in public beta\.   | 
| Over\-the\-Air Updates | [OTA Agent API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__ota__agent_8h.html) |  The Amazon FreeRTOS AWS IoT Over\-the\-Air \(OTA\) Agent library connects your Amazon FreeRTOS device to the AWS IoT OTA agent\. For more information, see [Amazon FreeRTOS Over\-the\-Air \(OTA\) Agent Library](ota-agent-library.md)\.  | 
| FreeRTOS\+POSIX | [FreeRTOS\+POSIX API Reference](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_POSIX/API/API_details_doxygen/index.html) |  You can use the FreeRTOS\+POSIX library to port POSIX\-compliant applications to the Amazon FreeRTOS ecosystem\. For more information, see [FreeRTOS\+POSIX](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_POSIX/index.html)\. | 
| Secure Sockets | [Secure Sockets API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html2/secure_sockets/index.html) | For more information, see [Amazon FreeRTOS Secure Sockets Library](secure-sockets.md)\. | 
| FreeRTOS\+TCP | [FreeRTOS\+TCP API Reference](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/FreeRTOS_TCP_API_Functions.html) |  FreeRTOS\+TCP is a scalable, open source and thread safe TCP/IP stack for FreeRTOS\. For more information, see [FreeRTOS\+TCP](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/index.html)\.  | 
| Wi\-Fi | [Wi\-Fi API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html2/wifi/index.html) |  The Amazon FreeRTOS Wi\-Fi library enables you to interface with your microcontroller's lower\-level wireless stack\. For more information, see [Amazon FreeRTOS Wi\-Fi Library](freertos-wifi.md)\. | 
| PKCS \#11 |  |  The Amazon FreeRTOS PKCS \#11 library is a reference implementation of the Public Key Cryptography Standard \#11, to support provisioning and TLS client authentication\. For more information, see [Amazon FreeRTOS Public Key Cryptography Standard \(PKCS\) \#11 Library](security-pkcs.md)\.  | 
| TLS |  |  For more information, see [Amazon FreeRTOS Transport Layer Security \(TLS\)](security-tls.md)\.  | 

## Amazon FreeRTOS Application Libraries<a name="dev-guide-freertos-application-libraries"></a>

You can optionally include the following standalone application libraries in your Amazon FreeRTOS configuration to interact with AWS IoT\. 


**Amazon FreeRTOS application libraries**  

| Library | API Reference | Description | 
| --- | --- | --- | 
| Greengrass | [Greengrass API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__greengrass__discovery_8h.html) |  The Amazon FreeRTOS AWS IoT Greengrass library connects your Amazon FreeRTOS device to AWS IoT Greengrass\. For more information, see [Amazon FreeRTOS AWS IoT Greengrass Discovery Library](freertos-lib-gg-connectivity.md)\.  | 
| MQTT |  [MQTT Library API Reference \(Legacy\)](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__mqtt__lib_8h.html) [MQTT Agent API Reference \(Legacy\)](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__mqtt__agent_8h.html) [MQTT API Reference \(Beta\)](https://docs.aws.amazon.com/freertos/latest/lib-ref/html3/mqtt/index.html)  |  The Amazon FreeRTOS MQTT library provides a client for your Amazon FreeRTOS device to publish and subscribe to MQTT topics\. MQTT is the protocol that devices use to interact with AWS IoT\. For more information about the legacy Amazon FreeRTOS MQTT library, see [Amazon FreeRTOS MQTT Library \(Legacy\)](freertos-lib-cloud-mqtt.md)\. For more information about the new Amazon FreeRTOS MQTT library, in public beta, see [Amazon FreeRTOS MQTT Library \(Beta\)](freertos-mqtt-2.md)\.  | 
| Device Shadow | [Device Shadow API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__shadow_8h.html) |  The AWS IoT Device Shadow library enables your Amazon FreeRTOS device to interact with AWS IoT device shadows\. For more information, see [Amazon FreeRTOS AWS IoT Device Shadow Library](freertos-lib-cloud-shadows.md)\.  | 
| Device Defender |  |  The Amazon FreeRTOS AWS IoT Device Defender library connects your Amazon FreeRTOS device to AWS IoT Device Defender\. For more information, see [Amazon FreeRTOS AWS IoT Device Defender Library](afr-device-defender-library.md)\.  | 