# AWS IoT Device Defender demo<a name="dd-demo"></a>

## Introduction<a name="dd-demo-introduction"></a>

This demo shows you how to use the AWS IoT Device Shadow library to connect to [AWS IoT Device Defender](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender.html)\. The demo uses the coreMQTT library to establish an MQTT connection with TLS \(mutual authentication\) to the AWS IoT MQTT Broker and the coreJSON library to validate and parse responses received from AWS IoT Device Defender\. The demo shows how to create a JSON formatted report using metrics collected from the device, and how to submit the report to AWS IoT Device Defender\. The demo also shows how to register a callback function with the coreMQTT library to handle the responses that AWS IoT Device Defender sends to confirm whether a report was accepted or rejected\. 

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

## Functionality<a name="dd-demo-functionality"></a>

This demo creates a single application task that demonstrates how to collect metrics, construct a device defender report in JSON format, and submit it to the AWS IoT Device Defender through a secure MQTT connection to the AWS IoT MQTT Broker\.

How we collect metrics depends on the TCP/IP stack in use\. For FreeRTOS\+TCP and supported lwIP configurations, we provide metrics collection implementations that collect real metrics from the device and submit them in the AWS IoT Device Defender report\. You can find the implemenatations for [ FreeRTOS\+TCP](https://github.com/aws/amazon-freertos/blob/master/demos/device_defender_for_aws/metrics_collector/freertos_plus_tcp/metrics_collector.c) and [ lwIP](https://github.com/aws/amazon-freertos/blob/master/demos/device_defender_for_aws/metrics_collector/lwip/metrics_collector.c) on GitHub\.

For boards using any other TCP/IP stack, stub definitions of the metrics collection functions that return zeros for all metrics are provided\. To send real metrics on boards using this stub implementation, implement the functions in `freertos/demos/device_defender_for_aws/metrics_collector/stub/metrics_collector.c` for your network stack\. The file is also available on the [ GitHub](https://github.com/aws/amazon-freertos/blob/master/demos/device_defender_for_aws/metrics_collector/stub/metrics_collector.c) website\.

For ESP32, the default lwIP configuration does not use core locking and therefore the demo will use stubbed metrics\. If you want to use the reference lwIP metrics collection implementation, define the following macros in `lwiopts.h`:

```
#define LINK_SPEED_OF_YOUR_NETIF_IN_BPS 0
#define LWIP_TCPIP_CORE_LOCKING         1
#define LWIP_STATS                      1
#define MIB2_STATS                      1
```

The following is an example output when you run the demo\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/Defender_p4_supported.png)

If your board isn't using FreeRTOS\+TCP or a supported lwIP configuration, the output will look like the following\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/Defender_p4_unsupported.png)

The source code of the demo is in your download in `freertos/demos/device_defender_for_aws/` directory or on the [GitHub](https://github.com/aws/amazon-freertos/tree/master/demos/device_defender_for_aws) website\.

### Subscribing to AWS IoT Device Defender topics<a name="dd-demo-subscribing"></a>

The [ subscribeToDefenderTopics](https://github.com/aws/amazon-freertos/blob/master/demos/device_defender_for_aws/defender_demo.c#L434-L450) function subscribes to the MQTT topics on which responses to published Device Defender reports will be received\. It uses the macro `DEFENDER_API_JSON_ACCEPTED` to construct the topic string on which responses for accepted device defender reports are received\. It uses the macro `DEFENDER_API_JSON_REJECTED` to construct the topic string on which responses for rejected device defender reports will be received\.

### Collecting device metrics<a name="dd-demo-collecting-metrics"></a>

The [ `collectDeviceMetrics`](https://github.com/aws/amazon-freertos/blob/master/demos/device_defender_for_aws/defender_demo.c#L360-L431) function gathers networking metrics using the functions defined in `metrics_collector.h`\. The metrics collected are the number of bytes and packets sent and received, the open TCP ports, the open UDP ports, and the established TCP connections\.

### Generating the AWS IoT Device Defender report<a name="dd-demo-generating-report"></a>

The [ `generateDeviceMetricsReport`](https://github.com/aws/amazon-freertos/blob/master/demos/device_defender_for_aws/defender_demo.c#L472-L501)function generates a device defender report using the function defined in `report_builder.h`\. That function takes the networking metrics and a buffer, creates a JSON document in the format as expected by AWS IoT Device Defender and writes it to the provided buffer\. The format of the JSON document expected by AWS IoT Device Defender is specified in [Device\-side metrics](https://docs.aws.amazon.com/iot/latest/developerguide/detect-device-side-metrics.html) in the *AWS IoT Developer Guide*\.

### Publishing the AWS IoT Device Defender report<a name="dd-demo-publishing-report"></a>

The AWS IoT Device Defender report is published on the MQTT topic for publishing JSON AWS IoT Device Defender reports\. The report is constructed using the macro `DEFENDER_API_JSON_PUBLISH`, as shown in this [ code snippet](https://github.com/aws/amazon-freertos/blob/master/demos/device_defender_for_aws/defender_demo.c#L602-L612) on the GitHub website\. 

### Callback for handling responses<a name="dd-demo-callback-handling"></a>

The [ `publishCallback`](https://github.com/aws/amazon-freertos/blob/master/demos/device_defender_for_aws/defender_demo.c#L286-L357) function handles incoming MQTT publish messages\. It uses the `Defender_MatchTopic` API from the AWS IoT Device Defender library to check if the incoming MQTT message is from the AWS IoT Device Defender service\. If the message is from AWS IoT Device Defender, it parses the received JSON response and extracts the report ID in the response\. The report ID is then verified to be the same as the one sent in the report\.