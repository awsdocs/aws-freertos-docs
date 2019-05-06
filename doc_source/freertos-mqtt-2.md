# Amazon FreeRTOS MQTT Library \(Beta\)<a name="freertos-mqtt-2"></a>


|  | 
| --- |
| The new MQTT library is in public beta release for Amazon FreeRTOS and is subject to change\. | 

## Overview<a name="freertos-mqtt-2-overview"></a>

You can use the Amazon FreeRTOS MQTT library to create applications that publish and subscribe to MQTT topics, as MQTT clients on a network\. The Amazon FreeRTOS MQTT library implements the MQTT 3\.1\.1 standard for compatibility with the [AWS IoT MQTT server](https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html#mqtt)\. The library is also compatible with other MQTT servers\.

The source files for the Amazon FreeRTOS MQTT library are located in [https://github.com/aws/amazon-freertos/blob/master/lib/mqtt](https://github.com/aws/amazon-freertos/blob/master/lib/mqtt)\.

The Amazon FreeRTOS MQTT library documented here is in public beta\. For more information about the legacy Amazon FreeRTOS MQTT library, see [Amazon FreeRTOS MQTT Library \(Legacy\)](freertos-lib-cloud-mqtt.md)\.

## Dependencies and Requirements<a name="freertos-mqtt-2-dependencies"></a>

The Amazon FreeRTOS MQTT library has the following dependencies:
+ The queue library for maintaining the data structures that manage in\-progress MQTT operations
+ The logging library, if the configuration parameter `AWS_IOT_MQTT_LOG_LEVEL` is not set to `AWS_IOT_LOG_NONE`
+ The platform layer that provides an interface to the operating system for thread management, clock functions, networking, and other platform\-level functionality
+ C standard library headers

The diagram below illustrates these dependencies\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/MQTT2dependencies.png)

## Features<a name="freertos-mqtt-2-features"></a>

The Amazon FreeRTOS MQTT library has the following features:
+ By default, the library has a fully asynchronous MQTT API\. You can opt to use the library synchronously with the `AwsIotMqtt_Wait` function\.
+ The library is thread\-aware and parallelizable for high throughput\.
+ The library features scalable performance and footprint\. Use the configuration setting to tailor the library to a system's resources\.

## Configuration<a name="freertos-mqtt-2-config"></a>

Configuration settings for the Amazon FreeRTOS MQTT library are defined as C preprocessor constants\. Set configuration settings as \#define constants in a file named `AWS_IOT_CONFIG_FILE`, or by using a compiler option such as `-D` in `gcc`\. Because configuration settings are defined as compile\-time constants, a library must be rebuilt if a configuration setting is changed\. The MQTT library uses default values when configuration settings are not defined\.

For more information about configuring the Amazon FreeRTOS MQTT library, see [MQTT API Reference \(Beta\)](https://docs.aws.amazon.com/freertos/latest/lib-ref/html3/mqtt/index.html)\.

## API Reference<a name="freertos-mqtt-2-api"></a>

For a full API reference, see [MQTT API Reference \(Beta\)](https://docs.aws.amazon.com/freertos/latest/lib-ref/html3/mqtt/index.html)\.

## Example Usage<a name="freertos-mqtt-2-example"></a>

### `aws_iot_demo_mqtt.c`<a name="mqtt-2-demo-file"></a>

For example usage of the Amazon FreeRTOS MQTT library, see MQTT demo application defined in `aws_iot_demo_mqtt.c`\.

The MQTT demo demonstrates the subscribe\-publish workflow of MQTT\. After subscribing to multiple topic filters, the application publishes bursts of data to various topic names\. As each message arrives, the demo publishes an acknowledgement message back to the MQTT server\.

To run the MQTT demo, you need to configure the following parameters:Global Demo Configuration Parameters

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