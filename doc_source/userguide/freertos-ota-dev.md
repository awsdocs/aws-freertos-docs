# Amazon FreeRTOS Over\-the\-Air Updates<a name="freertos-ota-dev"></a>

Over\-the\-air \(OTA\) updates allow you to deploy firmware updates to one or more devices in your fleet\. Although OTA updates were designed to update device firmware, you can use them to send any files to one or more devices registered with AWS IoT\. When you send updates over the air, we recommend that you digitally sign them so that the devices that receive the files can verify they haven't been tampered with en route\.

You can use [Code Signing for AWS IoT](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html) to sign your files, or you can sign your files with your own code\-signing tools\.

When you create an OTA update, the [OTA Update Manager Service](ota-manager.md) creates an [AWS IoT job](https://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html) to notify your devices that an update is available\. The OTA demo application runs on your device and creates an Amazon FreeRTOS task that subscribes to notification topics for AWS IoT jobs and listens for update messages\. When an update is available, the OTA agent publishes requests to AWS IoT  and receives updates  using the HTTP or MQTT protocol, depending on the settings you chose\. It  checks the digital signature of the downloaded files and, if the files are valid, installs the firmware update\. If you don't use the Amazon FreeRTOS OTA Update demo application, you must integrate the [Amazon FreeRTOSOTA Agent Library](ota-agent-library.md) into your own application to get the firmware update capability\. 

Amazon FreeRTOS over\-the\-air updates make it possible for you to:
+ Digitally sign firmware before deployment\.
+ Deploy new firmware images to a single device, a group of devices, or your entire fleet\.
+ Deploy firmware to devices as they are added to groups, reset, or reprovisioned\.
+ Verify the authenticity and integrity of new firmware after it's deployed to devices\.
+ Monitor the progress of a deployment\.
+ Debug a failed deployment\.

## Tagging OTA Resources<a name="ota-tagging"></a>

To help you manage your OTA resources, you can optionally assign your own metadata to updates and streams in the form of tags\. Tags make it possible for you to categorize your AWS IoT resources in different ways \(for example, by purpose, owner, or environment\)\. This is useful when you have many resources of the same type\. You can quickly identify a resource based on the tags you've assigned to it\.

For more information, see [Tagging Your AWS IoT Resources](https://docs.aws.amazon.com/iot/latest/developerguide/tagging-iot.html)\.