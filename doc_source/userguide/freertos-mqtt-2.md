# MQTT Library, Version 2\.0\.0<a name="freertos-mqtt-2"></a>

## Overview<a name="freertos-mqtt-2-overview"></a>

You can use the FreeRTOS MQTT library to create applications that publish and subscribe to MQTT topics, as MQTT clients on a network\. The FreeRTOS MQTT library implements the MQTT 3\.1\.1 standard for compatibility with the [AWS IoT MQTT server](https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html#mqtt)\. The library is also compatible with other MQTT servers\.

The source files for the FreeRTOS MQTT library are located in `<freertos>/.../mqtt`\. These files implement version 2\.0\.0 of the FreeRTOS MQTT library\. FreeRTOS also includes a backward\-compatibility layer for version 1\.0\.0 of the FreeRTOS MQTT library\. For information about FreeRTOS MQTT version 1\.0\.0, see [MQTT Library, Version 1\.0\.0](freertos-lib-cloud-mqtt.md)\.

## Dependencies and Requirements<a name="freertos-mqtt-2-dependencies"></a>

The FreeRTOS MQTT library has the following dependencies:
+ [Linear Containers Library](lib-linear.md)\.
+ [Logging Library](lib-logging.md) \(if the configuration parameter `AWS_IOT_MQTT_LOG_LEVEL` is not set to `AWS_IOT_LOG_NONE`\)\.
+ [Static Memory Library](lib-static.md) \(if static memory only\)\.
+ [Task Pool Library](task-pool.md)\.
+ The platform layer that provides an interface to the operating system for thread management, clock functions, networking, and other platform\-level functionality
+ C standard library headers

The diagram below illustrates these dependencies\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/MQTT2dependencies.png)

## Features<a name="freertos-mqtt-2-features"></a>

The FreeRTOS MQTT library has the following features:
+ By default, the library has a fully asynchronous MQTT API\. You can opt to use the library synchronously with the `AwsIotMqtt_Wait` function\.
+ The library is thread\-aware and parallelizable for high throughput\.
+ The library features scalable performance and footprint\. Use the configuration setting to tailor the library to a system's resources\.

## API Reference<a name="freertos-mqtt-2-api"></a>

FreeRTOS and the AWS IoT Device SDK for Embedded C have the same MQTT v2\.0\.0 library APIs\. For a full API reference, see [MQTT \(v2\.0\.0\) C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/mqtt/index.html)\.

## Example Usage<a name="freertos-mqtt-2-example"></a>

For example usage of the FreeRTOS MQTT library, see MQTT demo application defined in `iot_demo_mqtt.c`\.

The MQTT demo demonstrates the subscribe\-publish workflow of MQTT\. After subscribing to multiple topic filters, the application publishes bursts of data to various topic names\. As each message arrives, the demo publishes an acknowledgement message back to the MQTT server\.

To use the MQTT demo, you must create a thing in AWS IoT, including a certificate and policy\. For more information, see [ AWS IoT Getting Started](https://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html)\.Global Demo Configuration Parameters

You must set values for the following constants in the `<freertos>/demos/include/aws_clientcredential.h` file:

`clientcredentialMQTT_BROKER_ENDPOINT`  
Your AWS IoT endpoint\.

`clientcredentialIOT_THING_NAME`  
The name of your IoT thing\.

`clientcredentialWIFI_SSID`  
The SSID for your Wi\-Fi network\.

`clientcredentialWIFI_PASSWORD`  
Your Wi\-Fi password\.

`clientcredentialWIFI_SECURITY`  
The type of security used by your Wi\-Fi network\.

You must also set values for the following constants in the `<freertos>/demos/include/aws_clientcredential_keys.h` file: 

`keyCLIENT_CERTIFICATE_PEM`  
The certificate PEM associated with your thing\.

`keyCLIENT_PRIVATE_KEY_PEM`  
The private key PEM associated with your thing\.MQTT Demo Configuration Parameters

These configuration parameters apply to the MQTT demo\.

`AWS_IOT_DEMO_MQTT_PUBLISH_BURST_SIZE`  
The number of messages to publish in each burst\.

`AWS_IOT_DEMO_MQTT_PUBLISH_BURST_COUNT`  
The number of publish bursts in this demo\.