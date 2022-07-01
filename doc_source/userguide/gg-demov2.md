# AWS IoT Greengrass V2<a name="gg-demov2"></a>

## Compatibility with AWS IoT Greengrass V2 devices<a name="ggv2-compatibility"></a>

 AWS IoT Greengrass V2 support for client devices is backwards\-compatible with AWS IoT Greengrass V1\. You can connect FreeRTOS client devices to V2 core devices without changing the application code\. To enable client devices to connect to a V2 core device, do the following\. 
+  Deploy Greengrass software to the Greengrass core device\. See [ Connect client devices to core devices](https://docs.aws.amazon.com//greengrass/v2/developerguide/connect-client-devices.html) to connect a device to AWS IoT Greengrass V2\.
+  To relay messages \(including Lambda functions\) between client devices, AWS IoT Core cloud service, and Greengrass components, deploy and configure the [ MQTT bridge component](https://docs.aws.amazon.com//greengrass/v2/developerguide/mqtt-bridge-component.html)\.
+ Deploy the [ IP detector component](https://docs.aws.amazon.com//greengrass/v2/developerguide/ip-detector-component.html) to automatically detect connectivity information, or manually manage the endpoints\.
+ See [ Interact with local AWS IoT devices](https://docs.aws.amazon.com//greengrass/v2/developerguide/interact-with-local-iot-devices.html) for more information\.

For more details, see AWS documentation about running [AWS IoT Greengrass V1 applications on AWS IoT Greengrass V2](https://docs.aws.amazon.com//greengrass/v2/developerguide/migrate-from-v1.html#connect-v1-greengrass-devices)\.