# OTA tutorial<a name="dev-guide-ota-workflow"></a>

This section contains a tutorial for updating firmware on devices running FreeRTOS using OTA updates\. In addition to firmware images, you can use an OTA update to send any type of file to a device connected to AWS IoT\.

You can use the AWS IoT console or the AWS CLI to create an OTA update\. The console is the easiest way to get started with OTA because it does a lot of the work for you\. The AWS CLI is useful when you are automating OTA update jobs, working with a large number of devices, or are using devices that have not been qualified for FreeRTOS\. For more information about qualifying devices for FreeRTOS, see the [FreeRTOS Partners](https://aws.amazon.com/partners/dqp/) website\.<a name="create-update"></a>

**To create an OTA update**

1. Deploy an initial version of your firmware to one or more devices\.

1. Verify that the firmware is running correctly\.

1. When a firmware update is required, make the code changes and build the new image\.

1. If you are manually signing your firmware, sign and then upload the signed firmware image to your Amazon S3 bucket\. If you are using Code Signing for AWS IoT, upload your unsigned firmware image to an Amazon S3 bucket\.

1. Create an OTA update\.

When you create an OTA update, you specify the image delivery protocol \(MQTT or HTTP\) or specify both to allow the device to choose\. The FreeRTOS OTA agent on the device receives the updated firmware image and verifies the digital signature, checksum, and version number of the new image\. If the firmware update is verified, the device is reset and, based on application\-defined logic, commits the update\. If your devices are not running FreeRTOS, you must implement an OTA agent that runs on your devices\. 