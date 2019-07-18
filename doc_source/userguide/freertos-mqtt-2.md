# Amazon FreeRTOS MQTT Library, Version 2\.0\.0<a name="freertos-mqtt-2"></a>

## Overview<a name="freertos-mqtt-2-overview"></a>

You can use the Amazon FreeRTOS MQTT library to create applications that publish and subscribe to MQTT topics, as MQTT clients on a network\. The Amazon FreeRTOS MQTT library implements the MQTT 3\.1\.1 standard for compatibility with the [AWS IoT MQTT server](https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html#mqtt)\. The library is also compatible with other MQTT servers\.

The source files for the Amazon FreeRTOS MQTT library are located in `<amazon-freertos>/.../mqtt`\. These files implement version 2\.0\.0 of the Amazon FreeRTOS MQTT library\. Amazon FreeRTOS also includes a backward\-compatibility layer for version 1\.0\.0 of the Amazon FreeRTOS MQTT library\. For information about Amazon FreeRTOS MQTT version 1\.0\.0, see [Amazon FreeRTOS MQTT Library, Version 1\.0\.0](freertos-lib-cloud-mqtt.md)\.

## Dependencies and Requirements<a name="freertos-mqtt-2-dependencies"></a>

The Amazon FreeRTOS MQTT library has the following dependencies:
+ [Amazon FreeRTOS Linear Containers Library](lib-linear.md)\.
+ [Amazon FreeRTOS Logging Library](lib-logging.md) \(if the configuration parameter `AWS_IOT_MQTT_LOG_LEVEL` is not set to `AWS_IOT_LOG_NONE`\)\.
+ [Amazon FreeRTOS Static Memory Library](lib-static.md) \(if static memory only\)\.
+ [Amazon FreeRTOS Task Pool Library](task-pool.md)\.
+ The platform layer that provides an interface to the operating system for thread management, clock functions, networking, and other platform\-level functionality
+ C standard library headers

The diagram below illustrates these dependencies\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/MQTT2dependencies.png)

## Features<a name="freertos-mqtt-2-features"></a>

The Amazon FreeRTOS MQTT library has the following features:
+ By default, the library has a fully asynchronous MQTT API\. You can opt to use the library synchronously with the `AwsIotMqtt_Wait` function\.
+ The library is thread\-aware and parallelizable for high throughput\.
+ The library features scalable performance and footprint\. Use the configuration setting to tailor the library to a system's resources\.

## API Reference<a name="freertos-mqtt-2-api"></a>

Amazon FreeRTOS and the AWS IoT Device SDK for Embedded C have the same MQTT v2\.0\.0 library APIs\. For a full API reference, see [MQTT \(v2\.0\.0\) C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/mqtt/index.html)\.

## Example Usage<a name="freertos-mqtt-2-example"></a>

For example usage of the Amazon FreeRTOS MQTT library, see MQTT demo application defined in `iot_demo_mqtt.c`\.

The MQTT demo demonstrates the subscribe\-publish workflow of MQTT\. After subscribing to multiple topic filters, the application publishes bursts of data to various topic names\. As each message arrives, the demo publishes an acknowledgement message back to the MQTT server\.

To run the MQTT demo, you need to configure the following parameters in the [global configuration file](dev-guide-freertos-libraries.md#lib-config):Global Demo Configuration Parameters

These configuration parameters apply to all demos\.

`AWS_IOT_DEMO_SECURED_CONNECTION`  
Determines if the demo uses a TLS\-secured connection with the remote host by default\.

`AWS_IOT_DEMO_SERVER`  
The default remote host to use\.

`AWS_IOT_DEMO_PORT`  
The default remote port to use\.

`AWS_IOT_DEMO_ROOT_CA`  
The path to the default trusted server root certificate to use\.

`AWS_IOT_DEMO_CLIENT_CERT`  
The path to the default client certificate to use\.

`AWS_IOT_DEMO_PRIVATE_KEY`  
The path to the default client certificate private key to use\.MQTT Demo Configuration Parameters

These configuration parameters apply to the MQTT demo\.

`AWS_IOT_DEMO_MQTT_PUBLISH_BURST_SIZE`  
The number of messages to publish in each burst\.

`AWS_IOT_DEMO_MQTT_PUBLISH_BURST_COUNT`  
The number of publish bursts in this demo\.