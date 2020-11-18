# Creating an OTA update \(AWS IoT console\)<a name="ota-console-workflow"></a>

1. In the navigation pane of the AWS IoT console, choose **Manage**, and then choose **Jobs**\.

1. Choose **Create**\.

1. Under **Create a FreeRTOS Over\-the\-Air \(OTA\) update job**, choose **Create OTA update job**\.

1. You can deploy an OTA update to a single device or a group of devices\. Under **Select devices to update**, choose **Select**\. To update a single device, choose the **Things** tab\. To update a group of devices, choose the **Thing Groups** tab\.

1. If you are updating a single device, select the check box next to the IoT thing associated with your device\. If you are updating a group of devices, select the check box next to the thing group associated with your devices\. Choose **Next**\.

1. Under **Select the protocol for firmware image transfer**, choose **HTTP**, **MQTT**, or choose both to allow each device to determine the protocol to use\.

1. Under **Select and sign your firmware image**, choose **Sign a new firmware image for me**\.

1. Under **Code signing profile**, choose **Create**\.

1. In **Create a code signing profile**, enter a name for your code\-signing profile\.

   1. Under **Device hardware platform**, choose your hardware platform\.
**Note**  
Only hardware platforms that have been qualified for FreeRTOS are displayed in this list\. If you are testing a non\-qualified platform, and you are using the ECDSA P\-256 SHA\-256 ciphersuite for signing, you can pick the Windows Simulator code signing profile to produce a compatible signature\. If you are using a non\-qualified platform, and you are using a ciphersuite other than ECDSA P\-256 SHA\-256 for signing, you can use Code Signing for AWS IoT, or you can sign your firmware update yourself\. For more information, see [Digitally signing your firmware update](ota-cli-workflow.md#ota-sign-cli)\. 

   1. Under **Code signing certificate**, choose **Select** to select a previously imported certificate or **Import** to import a new certificate\.

   1. Under **Pathname of code signing certificate on device**, enter the fully qualified path name to the code signing certificate on your device\. The certificate's location varies by platform\. It should be the location where you put the code\-signing certificate when you followed the instructions in [Installing the initial firmware](dg-ota-initial-firmware.md)\.
**Important**  
On the Texas Instruments CC3220SF\-LAUNCHXL, do not include a leading forward slash \(**/**\) in front of the file name if your code signing certificate exists in the root of the file system\. Otherwise, the OTA update fails during authentication with a `file not found` error\.

1. Under **Select your firmware image in S3 or upload it**, choose **Select**\. A list of your Amazon S3 buckets is displayed\. Choose the bucket that contains your firmware update, and then choose your firmware update in the bucket\.
**Note**  
The Microchip Curiosity PIC32MZEF demo projects produce two binary images with default names of `mplab.production.bin` and `mplab.production.ota.bin`\. Use the second file when you upload an image for OTA updating\.

1. Under **Pathname of firmware image on device**, enter the fully qualified path name to the location on your device where the OTA job will copy the firmware image\. This location varies by platform\.
**Important**  
On the Texas Instruments CC3220SF\-LAUNCHXL, due to security restrictions, the firmware image path name must be `/sys/mcuflashimg.bin`\.

1. Under **IAM role for OTA update job**, choose a role according to the instructions in [Create an OTA Update service role](create-service-role.md)\. 

1. Choose **Next**\.

1. Enter an ID and description for your OTA update job\.

1. Under **Job type**, choose **Your job will complete after deploying to the selected devices/groups \(snapshot\)**\.

1. Choose any appropriate optional configurations for your job \(**Job executions rollout**, **Job abort**, **Job executions timeout**, and **Tags**\)\.

1. Choose **Create**\.<a name="previously-signed"></a>

**To use a previously signed firmware image**

1. Under **Select and sign your firmware image**, choose **Select a previously signed firmware image**\.

1. Under **Pathname of firmware image on device**, enter the fully qualified path name to the location on your device where the OTA job will copy the firmware image\. This location varies by platform\.

1. Under **Previous code signing job**, choose **Select**, and then choose the previous code\-signing job used to sign the firmware image you are using for the OTA update\.<a name="custom-signed"></a>

**Using a custom signed firmware image**

1. Under **Select and sign your firmware image**, choose **Use my custom signed firmware image**\.

1. Under **Pathname of code signing certificate on device**, enter the fully qualified path name to the code signing certificate on your device\. This path name varies by platform\.

1. Under **Pathname of firmware image on device**, enter the fully qualified path name to the location on your device where the OTA job will copy the firmware image\. This location varies by platform\.

1. Under **Signature**, paste your PEM format signature\.

1. Under **Original hash algorithm**, choose the hash algorithm that was used when you created your file signature\.

1. Under **Original encryption algorithm**, choose the algorithm that was used when you created your file signature\.

1. Under **Select your firmware image in Amazon S3**, choose the Amazon S3 bucket and the signed firmware image in the Amazon S3 bucket\.

After you have specified the code\-signing information, specify the OTA update job type, service role, and an ID for your update\.

**Note**  
Do not use any personally identifiable information in the job ID for your OTA update\. Examples of personally identifiable information include:  
Names\.
IP addresses\.
Email addresses\.
Locations\.
Bank details\.
Medical information\.

1. Under **Job type**, choose **Your job will complete after deploying to the selected devices/groups \(snapshot\)**\.

1. Under **IAM role for OTA update job**, choose your OTA service role\.

1. Enter an alphanumeric ID for your job, and then choose **Create**\.

The job appears in the AWS IoT console with a status of **IN PROGRESS**\.

**Note**  
The AWS IoT console does not update the state of jobs automatically\. Refresh your browser to see updates\.
If you see `"Error: You have exceeded the limit for the number of streams in your AWS account."`, then see [Stream limit exceeded for your AWS account](ota-troubleshooting-stream-limit.md)\.

Connect your serial UART terminal to your device\. You should see output that indicates the device is downloading the updated firmware\.

After the device downloads the updated firmware, it restarts and then installs the firmware\. You can see what's happening in the UART terminal\.

For a tutorial that shows you how to use the console to create an OTA update, see [Over\-the\-air updates demo application](ota-demo.md)\.