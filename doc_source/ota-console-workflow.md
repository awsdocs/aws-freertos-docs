# Creating an OTA Update \(AWS IoT Console\)<a name="ota-console-workflow"></a>

1. In the navigation pane of the AWS IoT console, choose **Manage**, and then choose **Jobs**\.

1. Choose **Create**\.

1. Under **Create an Amazon FreeRTOS Over\-the\-Air \(OTA\) update job**, choose **Create OTA update job**\.

1. You can deploy an OTA update to a single device or a group of devices\. Under **Select devices to update**, choose **Select**\. To update a single device, choose the **Things** tab\. To update a group of devices, choose the **Thing Groups** tab\.

1. If you are updating a single device, select the check box next to the IoT thing associated with your device\. If you are updating a group of devices, select the check box next to the thing group associated with your devices\. Choose **Next**\.

1. Under **Select and sign your firmware image**, choose **Sign a new firmware image for me**\.

1. Under **Code signing profile**, choose **Create**\.

1. In **Create a code signing profile**, enter a name for your code\-signing profile\.

   1. Under **Device hardware platform**, choose your hardware platform\.
**Note**  
Only hardware platforms that have been qualified for Amazon FreeRTOS are displayed in this list\. If you are using a non\-qualified platform, you must use the CLI to create the OTA update\. For more information, see [Creating an OTA Update with the AWS CLI](ota-cli-workflow.md)\.

   1. Under **Code signing certificate**, choose **Select** to select a previously imported certificate or **Import** to import a new certificate\.

   1. Under **Pathname of code signing certificate on device**, enter the fully\-qualified path name to the code\-signing certificate on your device\. This likely varies by platform\.
**Note**  
When running on the Microchip Curiosity PIC32MZEF, the code\-signing certificate is first searched for in the certificate store by name\. If not found, a built\-in certificate is used\.
**Important**  
On the Texas Instruments CC3220SF\-LAUNCHXL, do not include a leading slash character \(**/**\) in front of the file name if your code signing certificate exists in the root of the file system on this platform\. Otherwise, the OTA update fails during authentication with a `file not found` error\.

1. Under **Select your firmware image in S3 or upload it**, choose **Select**\. A list of your Amazon S3 buckets is displayed\. Choose the bucket that contains your firmware update, and then choose your firmware update in the bucket\.
**Note**  
The Microchip Curiosity PIC32MZEF demo projects produce two binary images with default names of `mplab.production.bin` and `mplab.production.ota.bin`\. Use the second file when you upload an image for OTA updating\.

1. Under **Pathname of firmware image on device**, enter the fully\-qualified path name to the location where the firmware image will be copied onto your device\. This location also varies by platform\.
**Important**  
On the Texas Instruments CC3220SF\-LAUNCHXL, due to security restrictions, the firmware image path name must be `/sys/mcuflashimg.bin`\.

1. Under **IAM role for OTA update job**, choose a role that allows access to your S3 bucket and has the following policies:
   + `AWSIoTThingsRegistration`
   + `AmazonFreeRTOSOTAUpdate`

1. Choose **Next**\.

1. Under **Job type**, choose **Your job will complete after deploying to the selected devices/groups \(snapshot\)**\.

1. Enter an ID and a description for your OTA update job and then choose **Create**\.<a name="previously-signed"></a>

**To use a previously signed firmware image**

1. Under **Select and sign your firmware image**, choose **Select a previously signed firmware image**\.

1. Under **Pathname of firmware image on device**, enter the fully\-qualified path name to the location where the firmware image will be copied onto your device\. This might be different on different platforms\.

1. Under **Previous code signing job**, choose **Select**, and then choose the previous code\-signing job used to sign the firmware image you are using for the OTA update\.<a name="custom-signed"></a>

**Using a custom signed firmware image**

1. Under **Select and sign your firmware image**, choose **Use my custom signed firmware image**\.

1. Under **Pathname of code signing certificate on device**, enter the fully\-qualified path name to the code\-signing certificate on your device\. This might be different for different platforms\.

1. Under **Pathname of firmware image on device**, enter the fully\-qualified path name to the location where the firmware image will be copied onto your device\. This might be different on different platforms\.

1. Under **Signature**, paste your PEM format signature\.

1. Under **Original hash algorithm**, choose the hash algorithm that was used when creating your file signature\.

1. Under **Original encryption algorithm**, choose the algorithm that was used when creating your file signature\.

1. Under **Select your firmware image in Amazon S3**, choose the Amazon S3 bucket and the signed firmware image in the Amazon S3 bucket\.

After you have specified the code\-signing information, specify the OTA update job type, service role, and an ID for your update\.

**Note**  
Do not use any personally identifiable information in the job ID for your OTA update\. Examples of personally identifiable information include:  
Your name\.
Your IP address\.
Your email address\.
Your location\.
Bank details\.
Medical information\.

1. Under **Job type**, choose **Your job will complete after deploying to the selected devices/groups \(snapshot\)**\.

1. Under **IAM role for OTA update job**, choose your OTA service role\.

1. Enter an alphanumeric ID for your job and then choose **Create**\.

The job appears in the AWS IoT console with a status of **IN PROGRESS**\.

**Note**  
The AWS IoT console does not update the state of jobs automatically\. Refresh your browser to see updates\.

Connect your serial UART terminal to your device\. You should see output that indicates the device is downloading the updated firmware\.

After the device downloads the updated firmware, it restarts and then installs the firmware\. You can see what's happening in the UART terminal\.

For a complete walkthrough of how to use the console to create an OTA update, see [OTA Demo Application](ota-demo.md)\.