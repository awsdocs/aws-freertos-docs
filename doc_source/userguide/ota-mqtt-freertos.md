# Prerequisites for OTA updates using MQTT<a name="ota-mqtt-freertos"></a>

This section describes the general requirements for using MQTT to perform over\-the\-air \(OTA updates\)\.

## Minimum requirements<a name="ota-mqtt-freertos-min-requirements"></a>
+ Device firmware must include the necessary FreeRTOS libraries \(MQTT, OTA Agent, and their dependencies\)\.
+ FreeRTOS version 1\.4\.0 or later is required\. However, we recommend that you use the latest version when possible\.

## Configurations<a name="ota-mqtt-freertos-config"></a>

Beginning with version 201912\.00, FreeRTOS OTA can use either the HTTP or MQTT protocol to transfer firmware update images from AWS IoT to devices\. If you specify both protocols when you create an OTA update in FreeRTOS, each device will determine the protocol used to transfer the image\. See [Prerequisites for OTA updates using HTTP](ota-http-freertos.md) for more information\.

By default, the configuration of the OTA protocols in `aws_ota_agent_config.h` is to use the MQTT protocol:

```
/**
 * @brief The protocol selected for OTA control operations.
 * This configuration parameter sets the default protocol for all the OTA control
 * operations like requesting OTA job, updating the job status etc.
 *
 * Note - Only MQTT is supported at this time for control operations.
 */
#define configENABLED_CONTROL_PROTOCOL       ( OTA_CONTROL_OVER_MQTT )
/**
 * @brief The protocol selected for OTA data operations.
 * This configuration parameter sets the protocols selected for the data operations
 * like requesting file blocks from the service.
 *
 * Note - Both MQTT and HTTP are supported for data transfer. This configuration parameter
 * can be set to the following -
 * Enable data over MQTT - ( OTA_DATA_OVER_MQTT )
 * Enable data over HTTP - ( OTA_DATA_OVER_HTTP)
 * Enable data over both MQTT & HTTP ( OTA_DATA_OVER_MQTT | OTA_DATA_OVER_HTTP )
 */
#define configENABLED_DATA_PROTOCOLS         ( OTA_DATA_OVER_MQTT )
 /**
  * @brief The preferred protocol selected for OTA data operations.
  *
  * Primary data protocol will be the protocol used for downloading files if more than
  * one protocol is selected while creating OTA job. Default primary data protocol is MQTT
  * and the following update here switches to HTTP as primary.
  *
  * Note - use OTA_DATA_OVER_HTTP for HTTP as primary data protocol.
  */
#define configOTA_PRIMARY_DATA_PROTOCOL     ( OTA_DATA_OVER_MQTT )
```

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