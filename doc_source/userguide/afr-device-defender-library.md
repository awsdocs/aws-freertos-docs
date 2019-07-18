# Amazon FreeRTOS AWS IoT Device Defender Library<a name="afr-device-defender-library"></a>

## Overview<a name="freertos-defender-overview"></a>

[AWS IoT Device Defender](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender.html) is an AWS IoT service that enables you to monitor connected devices to detect abnormal behavior and to mitigate security risks\. With AWS IoT Device Defender, you can enforce consistent IoT configurations across your AWS IoT device fleet and respond quickly when devices are compromised\.

Amazon FreeRTOS provides a library that allows your Amazon FreeRTOS\-based devices to work with AWS IoT Device Defender\. You can download Amazon FreeRTOS with the Device Defender library from the [Amazon FreeRTOS Console](http://console.aws.amazon.com/freertos) by adding the Device Defender library to your software configuration\. You can also clone the Amazon FreeRTOS GitHub repository, which includes all Amazon FreeRTOS libraries\.

**Note**  
The Amazon FreeRTOS AWS IoT Device Defender library only supports a subset of the [device\-side AWS IoT Device Defender metrics](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender-detect.html#DetectMetricsMessages) related to connection metrics\. For more information, see [Usage Restrictions](#freertos-defender-restrictions)\.

## Dependencies and Requirements<a name="freertos-defender-dependencies"></a>

The Device Defender library has the following dependencies:
+ [Amazon FreeRTOS Linear Containers Library](lib-linear.md)\.
+ [Amazon FreeRTOS Logging Library](lib-logging.md) \(if the configuration parameter `AWS_IOT_LOG_LEVEL_DEFENDER` is not set to `IOT_LOG_NONE`\)\.
+ [Amazon FreeRTOS Static Memory Library](lib-static.md) \(if static memory only\)\.
+ A platform layer that provides an interface to the operating system for thread management, timers, clock functions, etc\.
+ [Amazon FreeRTOS Task Pool Library](task-pool.md)\.
+ [Amazon FreeRTOS MQTT Library, Version 2\.0\.0](freertos-mqtt-2.md)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/defender-dependencies.png)

## Troubleshooting<a name="freertos-defender-troubleshooting"></a>

### Amazon FreeRTOS Device Defender Error Codes<a name="afr-device-defender-error-codes"></a>

The Device Defender library returns error codes as positive values\. For more information about each error code, see `AwsIotDefenderError_t` in the [Device Defender C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/defender/index.html)\.

### Amazon FreeRTOS Device Defender Events<a name="afr-device-defender-events"></a>

The Device Defender library includes the `AwsIotDefenderCallback_t` callback function, which returns positive, enumerated values known as "events" that indicate success or failure\. For more information about event types, see `AwsIotDefenderEventType_t` in the [Device Defender C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/defender/index.html)\.

### Debugging Amazon FreeRTOS Device Defender<a name="afr-device-defender-debugging"></a>

To enable the debugging for the Device Defender library, set the log level for Device Defender to debug mode in the [global configuration file](dev-guide-freertos-libraries.md#lib-config):

```
#define  AWS_IOT_LOG_LEVEL_DEFENDER  IOT_LOG_DEBUG
```

For more information, see the [Global Configuration File Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/main/global_library_config.html#IOT_CONFIG_FILE)\.

## Developer Support<a name="freertos-defender-support"></a>

The Device Defender library includes the `AwsIotDefender_strerror` helper function, which returns a string that describes the error that you provide to the function:

```
const char * AwsIotDefender_strerror( AwsIotDefenderError_t error );
```

## Usage Restrictions<a name="freertos-defender-restrictions"></a>

Although the AWS IoT Device Defender service supports both JSON and CBOR formats for data serialization, the Amazon FreeRTOS Device Defender library currently only supports CBOR, which is controlled by the configuration option `AWS_IOT_DEFENDER_FORMAT`\.

Additionally, the Amazon FreeRTOS AWS IoT Device Defender library only supports a subset of [device\-side AWS IoT Device Defender metrics](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender-detect.html#DetectMetricsMessages):


| Long Name | Short Name | Parent Element | Description | 
| --- | --- | --- | --- | 
| remote\_addr | rad | connections | Lists the remote address of a TCP connection\. | 
| total | t | established\_connections | Lists the number of established TCP connections\. | 

For example:

```
"tcp_connections": {
            "established_connections": {
                "connections": [
                    {
                        "remote_addr": "192.168.0.1:8000"
                    },
                    {
                        "remote_addr": "192.168.0.2:8000"
                    }
                ],
                "total": 2
            }
        }
```

This JSON document is for example purposes only, as Amazon FreeRTOS Device Defender library does not support JSON\-formatted metrics\.

## Initialization<a name="afr-device-defender-init"></a>

The macro `AWS_IOT_SECURE_SOCKETS_METRICS_ENABLED` must be defined to enable the secure sockets metrics\. Leaving this macro undefined could result in unpredictable behavior\.

## Amazon FreeRTOS Device Defender API<a name="afr-device-defender-api"></a>

For a full API reference, see the [Device Defender C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/defender/index.html)\.

## Example Usage<a name="freertos-defender-example"></a>

For a full example of the Device Defender library in use, see [AWS IoT Device Defender Demo](dd-demo.md)\.