# Amazon FreeRTOS Libraries<a name="dev-guide-freertos-libraries"></a>

Amazon FreeRTOS libraries provide additional functionality to the FreeRTOS kernel and its internal libraries\. You can use Amazon FreeRTOS libraries for networking and security in embedded applications\. Amazon FreeRTOS libraries also enable your applications to interact with AWS IoT services\.

The `libraries` directory contains the source code of the Amazon FreeRTOS libraries\. There are helper functions that assist in implementing the library functionality\. We do not recommend that you change these helper functions\.

## Amazon FreeRTOS Porting Libraries<a name="dev-guide-freertos-porting-libraries"></a>

The following porting libraries are included in configurations of Amazon FreeRTOS that are available for download on the Amazon FreeRTOS console\. These libraries are platform\-dependent\. Their contents change according to your hardware platform\. For information about porting these libraries to a device, see the [Amazon FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)\. 


**Amazon FreeRTOS Porting Libraries**  

| Library | API Reference | Description | 
| --- | --- | --- | 
| Bluetooth Low Energy | [Bluetooth Low Energy API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/ble/bt__hal__gatt__client_8h.html) | Using the Amazon FreeRTOS Bluetooth Low Energy library, your microcontroller can communicate with the AWS IoT MQTT broker through a gateway device\. For more information, see [Amazon FreeRTOS Bluetooth Low Energy Library](freertos-ble-library.md)\.  | 
| Over\-the\-Air Updates | [OTA Agent API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__ota__agent_8h.html) |  The Amazon FreeRTOS AWS IoT Over\-the\-Air \(OTA\) Agent library connects your Amazon FreeRTOS device to the AWS IoT OTA agent\. For more information, see [Amazon FreeRTOS Over\-the\-Air \(OTA\) Agent Library](ota-agent-library.md)\.  | 
| FreeRTOS\+POSIX | [FreeRTOS\+POSIX API Reference](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_POSIX/API/API_details_doxygen/index.html) |  You can use the FreeRTOS\+POSIX library to port POSIX\-compliant applications to the Amazon FreeRTOS ecosystem\. For more information, see [FreeRTOS\+POSIX](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_POSIX/index.html)\. | 
| Secure Sockets | [Secure Sockets API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html2/secure_sockets/index.html) | For more information, see [Amazon FreeRTOS Secure Sockets Library](secure-sockets.md)\. | 
| FreeRTOS\+TCP | [FreeRTOS\+TCP API Reference](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/FreeRTOS_TCP_API_Functions.html) |  FreeRTOS\+TCP is a scalable, open source and thread safe TCP/IP stack for FreeRTOS\. For more information, see [FreeRTOS\+TCP](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/index.html)\.  | 
| Wi\-Fi | [Wi\-Fi API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html2/wifi/index.html) |  The Amazon FreeRTOS Wi\-Fi library enables you to interface with your microcontroller's lower\-level wireless stack\. For more information, see [Amazon FreeRTOS Wi\-Fi Library](freertos-wifi.md)\. | 
| PKCS \#11 |  |  The Amazon FreeRTOS PKCS \#11 library is a reference implementation of the Public Key Cryptography Standard \#11, to support provisioning and TLS client authentication\. For more information, see [Amazon FreeRTOS Public Key Cryptography Standard \(PKCS\) \#11 Library](security-pkcs.md)\.  | 
| TLS |  |  For more information, see [Amazon FreeRTOS Transport Layer Security \(TLS\)](security-tls.md)\.  | 

## Amazon FreeRTOS Application Libraries<a name="dev-guide-freertos-application-libraries"></a>

You can optionally include the following standalone application libraries in your Amazon FreeRTOS configuration to interact with AWS IoT services on the cloud\.

**Note**  
Some of the application libraries have the same APIs as libraries in the AWS IoT Device SDK for Embedded C\. For these libraries, see the [AWS IoT Device SDK C API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/main/index.html)\. For more information about the the AWS IoT Device SDK for Embedded C, see [AWS IoT Device SDK for Embedded C](c-sdk.md)\.


**Amazon FreeRTOS application libraries**  

| Library | API Reference | Description | 
| --- | --- | --- | 
| AWS IoT Device Defender | [Device Defender C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/defender/index.html) |  The Amazon FreeRTOS AWS IoT Device Defender library connects your Amazon FreeRTOS device to AWS IoT Device Defender\. For more information, see [Amazon FreeRTOS AWS IoT Device Defender Library](afr-device-defender-library.md)\.  | 
| AWS IoT Greengrass | [Greengrass API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__greengrass__discovery_8h.html) |  The Amazon FreeRTOS AWS IoT Greengrass library connects your Amazon FreeRTOS device to AWS IoT Greengrass\. For more information, see [Amazon FreeRTOS AWS IoT Greengrass Discovery Library](freertos-lib-gg-connectivity.md)\.  | 
| MQTT |  [MQTT \(v1\.0\.0\) Library API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__mqtt__lib_8h.html) [MQTT \(v1\) Agent API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__mqtt__agent_8h.html) [MQTT \(v2\.0\.0\) C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/mqtt/index.html)  |  The Amazon FreeRTOS MQTT library provides a client for your Amazon FreeRTOS device to publish and subscribe to MQTT topics\. MQTT is the protocol that devices use to interact with AWS IoT\. For more information about the Amazon FreeRTOS MQTT library version 1\.0\.0, see [Amazon FreeRTOS MQTT Library, Version 1\.0\.0](freertos-lib-cloud-mqtt.md)\. For more information about the Amazon FreeRTOS MQTT library version 2\.0\.0, see [Amazon FreeRTOS MQTT Library, Version 2\.0\.0](freertos-mqtt-2.md)\.  | 
| AWS IoT Device Shadow | [Device Shadow C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__shadow_8h.html) |  The AWS IoT Device Shadow library enables your Amazon FreeRTOS device to interact with AWS IoT device shadows\. For more information, see [Amazon FreeRTOS AWS IoT Device Shadow Library](freertos-lib-cloud-shadows.md)\.  | 

## Amazon FreeRTOS Common Libraries<a name="dev-guide-freertos-common-libraries"></a>

The following common libraries extend the kernel functionality with additional data structures and functions for embedded application development\. These libraries are often dependencies of the Amazon FreeRTOS porting and application libraries\.

**Note**  
The AWS IoT Device SDK for Embedded C includes common libraries with APIs and functionality identical to these libraries\. For an API reference, see the [AWS IoT Device SDK C API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/main/index.html)\.


**Amazon FreeRTOS Common Libraries**  

| Library | API Reference | Description | 
| --- | --- | --- | 
| Atomic Operations | [Atomic Operations C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/platform/iot__atomic__generic_8h.html) | For more information, see [Amazon FreeRTOS Atomic Operations](atomic.md)\.  | 
| Linear Containers | [Linear Containers C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/linear_containers/index.html) | For more information, see [Amazon FreeRTOS Linear Containers Library](lib-linear.md)\.  | 
| Logging | [Logging C SDKAPI Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/logging/index.html) | For more information, see [Amazon FreeRTOS Logging Library](lib-logging.md)\.  | 
| Static Memory | [Static Memory C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/static_memory/index.html) | For more information, see [Amazon FreeRTOS Static Memory Library](lib-static.md)\.  | 
| Task Pool | [Task Pool C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/taskpool/index.html) | For more information, see [Amazon FreeRTOS Task Pool Library](task-pool.md)\. | 

## Configuring the Amazon FreeRTOS Libraries<a name="lib-config"></a>

Configuration settings for Amazon FreeRTOS and the AWS IoT Device SDK for Embedded C are defined as C preprocessor constants\. You can set configuration settings with a global configuration file, or by using a compiler option such as `-D` in `gcc`\. Because configuration settings are defined as compile\-time constants, a library must be rebuilt if a configuration setting is changed\.

If you want to use a global configuration file to set configuration options, create and save the file with the name `iot_config.h`, and place it in your include path\. Within the file, use `#define` directives to configure the Amazon FreeRTOS libraries, demos, and tests\.

For more information about the supported global configuration options, see the [Global Configuration File Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/main/global_library_config.html#IOT_CONFIG_FILE)\.