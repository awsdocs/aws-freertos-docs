# OTA Tutorial<a name="dev-guide-ota-workflow"></a>

This section contains a tutorial for updating firmware on devices running Amazon FreeRTOS using OTA updates\. You can, however, use OTA updates to send files to any devices connected to AWS IoT\.

You can use the AWS IoT console or the AWS CLI to create an OTA update\. The console is the easiest way to get started with OTA because it does a lot of the work for you\. The AWS CLI is useful when you are automating OTA update jobs, working with a large number of devices, or are using devices that have not been qualified for Amazon FreeRTOS\. For more information about qualifying devices for Amazon FreeRTOS, see the [Amazon FreeRTOS Partners](https://aws.amazon.com/freertos/partners/) website\.<a name="create-update"></a>

**To create a OTA update**

1. Deploy an initial version of your firmware to one or more devices\.

1. Verify that the firmware is running correctly\.

1. When a firmware update is required, make the code changes and build the new image\.

1. If you are manually signing your firmware, sign and then upload the signed firmware image to your Amazon S3 bucket\.

   If you are using Code Signing for Amazon FreeRTOS, upload your unsigned firmware image to an Amazon S3 bucket\.

1. Create an OTA update\.

The Amazon FreeRTOS OTA agent on the device receives the updated firmware image and verifies the digital signature, checksum, and version number of the new image\. If the firmware update is verified, the device is reset and, based on application\-defined logic, commits the update\. If your devices are not running Amazon FreeRTOS, you must implement an OTA agent that runs on your devices\. 