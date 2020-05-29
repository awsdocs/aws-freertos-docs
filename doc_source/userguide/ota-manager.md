# OTA Update Manager service<a name="ota-manager"></a>

The over\-the\-air \(OTA\) Update Manager service provides a way to:
+ Create an OTA update and the resources it uses, including an AWS IoT job, an AWS IoT stream, and code signing\.
+ Get information about an OTA update\. 
+ List all OTA updates associated with your AWS account\.
+ Delete an OTA update\.

An OTA update is a data structure maintained by the OTA Update Manager service\. It contains:
+ An OTA update ID\.
+ An optional OTA update description\.
+ A list of devices to update \(*targets*\)\.
+ The type of OTA update: CONTINUOUS or SNAPSHOT\. See the [Jobs](https://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html) section of the *AWS IoT Developer Guide* for a discussion of the type of update that you need\.
+ The protocol used to perform the OTA update: \[MQTT\], \[HTTP\] or \[MQTT, HTTP\]\. When you specify MQTT and HTTP, the device setup determines the protocol used\.
+ A list of files to send to the target devices\.
+ An IAM role that allows access to the AWS IoT Jobs service\.
+ An optional list of user\-defined name\-value pairs\.

OTA updates were designed to update device firmware, but you can use them to send any files that you want to one or more devices registered with AWS IoT\. When you send firmware updates over the air, we recommend that you digitally sign them so that the devices that receive them can verify they haven't been tampered with en route\.

You can send updated firmware images using the HTTP or MQTT protocol, depending on the settings that you choose\. You can sign your firmware updates with [Code Signing for FreeRTOS](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html) or you can use your own code\-signing tools\.

For more control over the process, you can use the [ CreateStream](https://docs.aws.amazon.com/iot/latest/apireference/API_CreateStream.html) API to create a stream when sending updates over MQTT\. In some instances, you can modify the FreeRTOS Agent [code](https://github.com/aws/amazon-freertos/tree/master/libraries/freertos_plus/aws/ota/src) to adjust the size of the blocks that you send and receive\.

When you create an OTA update, the OTA Manager service creates an [AWS IoT job](https://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html) to notify your devices that an update is available\. The FreeRTOS OTA Agent runs on your devices and listens for update messages\. When an update is available, it requests the firmware update image over HTTP or MQTT and stores the files locally\. It checks the digital signature of the downloaded files and, if valid, installs the firmware update\. If you're not using FreeRTOS, you must implement your own OTA Agent to listen for and download updates and perform any installation operations\.