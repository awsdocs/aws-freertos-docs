# FreeRTOS Libraries<a name="dev-guide-freertos-libraries"></a>

FreeRTOS libraries provide additional functionality to the FreeRTOS kernel and its internal libraries\. You can use FreeRTOS libraries for networking and security in embedded applications\. FreeRTOS libraries also enable your applications to interact with AWS IoT services\. FreeRTOS includes libraries that make it possible to:
+ Securely connect devices to the AWS IoT Cloud using MQTT and device shadows\.
+ Discover and connect to AWS IoT Greengrass cores\.
+ Manage Wi\-Fi connections\.
+ Listen for and process [FreeRTOS Over\-the\-Air Updates](freertos-ota-dev.md)\.

The `libraries` directory contains the source code of the FreeRTOS libraries\. There are helper functions that assist in implementing the library functionality\. We do not recommend that you change these helper functions\.

## FreeRTOS porting libraries<a name="dev-guide-freertos-porting-libraries"></a>

The following porting libraries are included in configurations of FreeRTOS that are available for download on the FreeRTOS console\. These libraries are platform\-dependent\. Their contents change according to your hardware platform\. For information about porting these libraries to a device, see the [FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)\.


**FreeRTOS porting libraries**  

| Library | API Reference | Description | 
| --- | --- | --- | 
| Bluetooth Low Energy |  [Bluetooth Low Energy API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html2/ble/index.html)  | Using the FreeRTOS Bluetooth Low Energy library, your microcontroller can communicate with the AWS IoT MQTT broker through a gateway device\. For more information, see [Bluetooth Low Energy library](freertos-ble-library.md)\.  | 
| Over\-the\-Air Updates | [ AWS IoT Over\-the\-air update API reference](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/index.html) |  The FreeRTOS AWS IoT Over\-the\-air \(OTA\) update library lets you manage update notifications, download updates, and perform cryptographic verification of firmware updates on your FreeRTOS device\. For more information, see [AWS IoT Over the air \(OTA\) library](ota-update-library.md)\.  | 
| FreeRTOS\+POSIX | [FreeRTOS\+POSIX API Reference](https://freertos.org/Documentation/api-ref/POSIX/index.html) |  You can use the FreeRTOS\+POSIX library to port POSIX\-compliant applications to the FreeRTOS ecosystem\. For more information, see [FreeRTOS\+POSIX](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_POSIX/index.html)\.  | 
| Secure Sockets | [ Secure Sockets API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html2/secure_sockets/index.html) | For more information, see [Secure Sockets library](secure-sockets.md)\. | 
| FreeRTOS\+TCP | [ FreeRTOS\+TCP API Reference](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/FreeRTOS_TCP_API_Functions.html) |  FreeRTOS\+TCP is a scalable, open source and thread safe TCP/IP stack for FreeRTOS\. For more information, see [FreeRTOS\+TCP](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/index.html)\.   | 
| Wi\-Fi | [Wi\-Fi API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html2/wifi/index.html) |  The FreeRTOS Wi\-Fi library enables you to interface with your microcontroller's lower\-level wireless stack\. For more information, see the [Wi\-Fi library](freertos-wifi.md)\.  | 
| corePKCS11 |  |  The corePKCS11 library is a reference implementation of the Public Key Cryptography Standard \#11, to support provisioning and TLS client authentication\. For more information, see the [corePKCS11 library](security-pkcs.md)\.  | 
| TLS |  |  For more information, see [Transport Layer Security](security-tls.md)\.  | 
| Common I/O | Common I/O API Reference |  For more information, see [Common I/O](common-io.md)\.  | 
| Cellular Interface | Cellular Interface API Reference |  The FreeRTOS Cellular Interface library exposes the capabilities of a few popular cellular modems through a uniform API\. For more information, see the [Cellular Interface library](cellular-interface.md)\.  | 

## FreeRTOS application libraries<a name="dev-guide-freertos-application-libraries"></a>

You can optionally include the following standalone application libraries in your FreeRTOS configuration to interact with AWS IoT services on the cloud\.

**Note**  
Some of the application libraries have the same APIs as libraries in the AWS IoT Device SDK for Embedded C\. For these libraries, see the [AWS IoT Device SDK C API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/main/index.html)\. For more information about the AWS IoT Device SDK for Embedded C, see [AWS IoT Device SDK for Embedded C](c-sdk.md)\.


**FreeRTOS application libraries**  

| Library | API Reference | Description | 
| --- | --- | --- | 
| AWS IoT Device Defender |   [ Device Defender C SDK API Reference](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/aws/device-defender-for-aws-iot-embedded-sdk/docs/doxygen/output/html/index.html)  |  The FreeRTOS AWS IoT Device Defender library connects your FreeRTOS device to AWS IoT Device Defender\. For more information, see [AWS IoT Device Defender library](afr-device-defender-library.md)\.  | 
| AWS IoT Greengrass | [ Greengrass API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__greengrass__discovery_8h.html)  |  The FreeRTOS AWS IoT Greengrass library connects your FreeRTOS device to AWS IoT Greengrass\. For more information, see [AWS IoT Greengrass Discovery library](freertos-lib-gg-connectivity.md)\.  | 
| MQTT |  [ MQTT \(v1\.x\.x\) Library API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__mqtt__lib_8h.html) [ MQTT \(v1\) Agent API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__mqtt__agent_8h.html) [MQTT \(v2\.x\.x\) C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/mqtt/index.html)  |  The coreMQTT library provides a client for your FreeRTOS device to publish and subscribe to MQTT topics\. MQTT is the protocol that devices use to interact with AWS IoT\.  For more information about the coreMQTT library version 3\.0\.0, see [coreMQTT library](coremqtt.md)\.  | 
| coreMQTT Agent |  [ coreMQTT Agent Library API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/embedded-csdk/libraries/standard/coreMQTT-Agent/docs/doxygen/output/html/index.html)   |  The coreMQTT Agent library is a high level API that adds thread safety to the coreMQTT library\. It lets you create a dedicated MQTT agent task that manages an MQTT connection in the background and doesn't need any intervention from other tasks\. The library provides thread safe equivalents to the coreMQTT's APIs, so it can be used in multi\-threaded environments\. For more information about the coreMQTT Agent library see [coreMQTT Agent library](coremqtt-agent.md)\.  | 
| AWS IoT Device Shadow | [Device Shadow C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html1/aws__shadow_8h.html) |  The AWS IoT Device Shadow library enables your FreeRTOS device to interact with AWS IoT device shadows\. For more information, see [AWS IoT Device Shadow library](freertos-lib-cloud-shadows.md)\.  | 

## Configuring the FreeRTOS libraries<a name="lib-config"></a>

Configuration settings for FreeRTOS and the AWS IoT Device SDK for Embedded C are defined as C preprocessor constants\. You can set configuration settings with a global configuration file, or by using a compiler option such as `-D` in `gcc`\. Because configuration settings are defined as compile\-time constants, a library must be rebuilt if a configuration setting is changed\.

If you want to use a global configuration file to set configuration options, create and save the file with the name `iot_config.h`, and place it in your include path\. Within the file, use `#define` directives to configure the FreeRTOS libraries, demos, and tests\.

For more information about the supported global configuration options, see the [Global Configuration File Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/main/global_library_config.html#IOT_CONFIG_FILE)\.