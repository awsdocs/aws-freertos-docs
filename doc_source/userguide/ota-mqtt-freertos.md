# Prerequisites for OTA updates using MQTT<a name="ota-mqtt-freertos"></a>

This section describes the general requirements for using MQTT to perform over\-the\-air \(OTA updates\)\.

## Minimum requirements<a name="ota-mqtt-freertos-min-requirements"></a>
+ Device firmware must include the necessary FreeRTOS libraries \(coreMQTT Agent, OTA update, and their dependencies\)\.
+ FreeRTOS version 1\.4\.0 or later is required\. However, we recommend that you use the latest version when possible\.

## Configurations<a name="ota-mqtt-freertos-config"></a>

Beginning with version 201912\.00, FreeRTOS OTA can use either the HTTP or MQTT protocol to transfer firmware update images from AWS IoT to devices\. If you specify both protocols when you create an OTA update in FreeRTOS, each device will determine the protocol used to transfer the image\. See [Prerequisites for OTA updates using HTTP](ota-http-freertos.md) for more information\.

By default, the configuration of the OTA protocols in [ `ota_config.h`](https://github.com/aws/amazon-freertos/blob/main/vendors/vendor/boards/board/aws_demos/config_files/ota_config.h) is to use the MQTT protocol\.

## Device specific configurations<a name="ota-mqtt-freertos-dev-config"></a>

None\.

## Memory usage<a name="ota-mqtt-freertos-memory"></a>

When MQTT is used for data transfer, no additional memory is required for the MQTT connection because it's shared between control and data operations\. 

## Device policy<a name="ota-mqtt-freertos-device-policy"></a>

Each device that receives an OTA update using MQTT must be registered as a thing in AWS IoT and the thing must have an attached policy like the one listed here\. You can find more information about the items in the `"Action"` and `"Resource"` objects at [AWS IoT Core Policy Actions](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policy-actions.html) and [AWS IoT Core Action Resources](https://docs.aws.amazon.com/iot/latest/developerguide/iot-action-resources.html)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "iot:Connect",
            "Resource": "arn:partition:iot:region:account:client/${iot:Connection.Thing.ThingName}"
        },
        {
            "Effect": "Allow",
            "Action": "iot:Subscribe",
            "Resource": [
                "arn:partition:iot:region:account:topicfilter/$aws/things/${iot:Connection.Thing.ThingName}/streams/*",
                "arn:partition:iot:region:account:topicfilter/$aws/things/${iot:Connection.Thing.ThingName}/jobs/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "iot:Publish",
                "iot:Receive"
            ],
            "Resource": [
                "arn:partition:iot:region:account:topic/$aws/things/${iot:Connection.Thing.ThingName}/streams/*",
                "arn:partition:iot:region:account:topic/$aws/things/${iot:Connection.Thing.ThingName}/jobs/*"
            ]
        }
    ]
}
```

**Notes**
+ The `iot:Connect` permissions allow your device to connect to AWS IoT over MQTT\.
+ The `iot:Subscribe` and `iot:Publish` permissions on the topics of AWS IoT jobs \(`.../jobs/*`\) allow the connected device to receive job notifications and job documents, and to publish the completion state of a job execution\.
+ The `iot:Subscribe` and `iot:Publish` permissions on the topics of AWS IoT OTA streams \(`.../streams/*`\) allow the connected device to fetch OTA update data from AWS IoT\. These permissions are required to perform firmware updates over MQTT\.
+ The `iot:Receive` permissions allow AWS IoT Core to publish messages on those topics to the connected device\. This permission is checked on every delivery of an MQTT message\. You can use this permission to revoke access to clients that are currently subscribed to a topic\.