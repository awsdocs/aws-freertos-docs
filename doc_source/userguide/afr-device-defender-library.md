# AWS IoT Device Defender library<a name="afr-device-defender-library"></a>

## Overview<a name="freertos-defender-overview"></a>

[AWS IoT Device Defender](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender.html) is an AWS IoT service that enables you to monitor connected devices to detect abnormal behavior and to mitigate security risks\. With AWS IoT Device Defender, you can enforce consistent IoT configurations across your AWS IoT device fleet and respond quickly when devices are compromised\.

FreeRTOS provides a [library ](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/defender/index.html) that allows your FreeRTOS\-based devices to work with AWS IoT Device Defender\. You can download FreeRTOS with the Device Defender library from the [FreeRTOS Console](https://console.aws.amazon.com/freertos) by adding the Device Defender library to your software configuration\. You can also clone the FreeRTOS GitHub repository, which includes all FreeRTOS libraries\. See the [ README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\.

**Note**  
The FreeRTOS AWS IoT Device Defender library only supports a subset of the [device\-side AWS IoT Device Defender metrics](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender-detect.html#DetectMetricsMessages) related to connection metrics\. For more information, see [Usage restrictions](#freertos-defender-restrictions)\.

## Dependencies and requirements<a name="freertos-defender-dependencies"></a>

The Device Defender library has the following dependencies:
+ [Linear Containers library](lib-linear.md)\.
+ [Logging library](lib-logging.md) \(if the configuration parameter `AWS_IOT_LOG_LEVEL_DEFENDER` is not set to `IOT_LOG_NONE`\)\.
+ [Static Memory library](lib-static.md) \(if Static Memory only\)\.
+ A platform layer that provides an interface to the operating system for thread management, timers, clock functions, etc\.
+ [Task Pool library](task-pool.md)\.
+ [MQTT library, version 2\.0\.0](freertos-mqtt-2.md)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/defender-dependencies.png)

## Troubleshooting<a name="freertos-defender-troubleshooting"></a>

### FreeRTOS Device Defender error codes<a name="afr-device-defender-error-codes"></a>

The Device Defender library returns error codes as positive values\. For more information about each error code, see `AwsIotDefenderError_t` in the [Device Defender C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/defender/index.html)\.

### FreeRTOS Device Defender events<a name="afr-device-defender-events"></a>

The Device Defender library includes the `AwsIotDefenderCallback_t` callback function, which returns positive, enumerated values known as "events" that indicate success or failure\. For more information about event types, see `AwsIotDefenderEventType_t` in the [Device Defender C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/defender/index.html)\.

### Debugging FreeRTOS Device Defender<a name="afr-device-defender-debugging"></a>

To enable the debugging for the Device Defender library, set the log level for Device Defender to debug mode in the [global configuration file](dev-guide-freertos-libraries.md#lib-config):

```
#define  AWS_IOT_LOG_LEVEL_DEFENDER  IOT_LOG_DEBUG
```

For more information, see the [Global Configuration File Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/main/global_library_config.html#IOT_CONFIG_FILE)\.

## Developer support<a name="freertos-defender-support"></a>

The Device Defender library includes the `AwsIotDefender_strerror` helper function, which returns a string that describes the error that you provide to the function:

```
const char * AwsIotDefender_strerror( AwsIotDefenderError_t error );
```

## Usage restrictions<a name="freertos-defender-restrictions"></a>

Although the AWS IoT Device Defender service supports both JSON and CBOR formats for data serialization, the FreeRTOS Device Defender library currently only supports CBOR, which is controlled by the configuration option `AWS_IOT_DEFENDER_FORMAT`\.

Additionally, the FreeRTOS AWS IoT Device Defender library only supports a subset of [device\-side AWS IoT Device Defender metrics](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender-detect.html#DetectMetricsMessages):


| Long Name | Short Name | Parent Element | Description | 
| --- | --- | --- | --- | 
| remote\_addr | rad | connections | Lists the remote address of a TCP connection\. | 
| total | t | established\_connections | Lists the number of established TCP connections\. | 

For example:

```
{
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
}
```

This JSON document is for example purposes only, as FreeRTOS Device Defender library does not support JSON\-formatted metrics\.

## Initialization<a name="afr-device-defender-init"></a>

The macro `AWS_IOT_SECURE_SOCKETS_METRICS_ENABLED` must be defined to enable the secure sockets metrics\. Leaving this macro undefined could result in unpredictable behavior\.

## FreeRTOS Device Defender API<a name="afr-device-defender-api"></a>

For a full API reference, see the [Device Defender C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/defender/index.html)\.

## Example usage<a name="freertos-defender-example"></a>

For a full example of the Device Defender library in use, see [AWS IoT Device Defender demo](dd-demo.md)\.