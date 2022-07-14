# Creating an OTA update \(AWS IoT console\)<a name="ota-console-workflow"></a>

1. In the navigation pane of the AWS IoT console, under **Manage** select **Remote actions**, and then choose **Jobs**\.

1. Choose **Create job**\.

1. Under **Job type** select **Create FreeRTOS OTA update job**, then choose **Next**\.

1. In **Job properties**, enter a **Job name** and \(optionally\) enter a **Description** of the job, then choose **Next**\.

1. You can deploy an OTA update to a single device or a group of devices\. Under **Devices to update**, choose one or more things or thing groups from the dropdown\.

1. Under **Select the protocol for the file transfer**, select either **HTTP** or **MQTT**, or select both to allow each device to determine the protocol to use\.

1. Under **Sign and choose your file**, select **Sign a new file for me**\.

1. Under **Code signing profile**, choose **Create new profile**\.

1. In **Create a code signing profile**, enter a name for your code\-signing profile\.

   1. Under **Device hardware platform**, choose your hardware platform\.
**Note**  
Only hardware platforms that have been qualified for FreeRTOS are displayed in this list\. If you are testing a non\-qualified platform, and you are using the ECDSA P\-256 SHA\-256 ciphersuite for signing, you can pick the Windows Simulator code signing profile to produce a compatible signature\. If you are using a non\-qualified platform, and you are using a ciphersuite other than ECDSA P\-256 SHA\-256 for signing, you can use Code Signing for AWS IoT, or you can sign your firmware update yourself\. For more information, see [Digitally signing your firmware update](ota-cli-workflow.md#ota-sign-cli)\.

   1. Under **Code signing certificate**, choose **Select an existing certificate** and then select a previously imported certificate, or choose **Import a new code signing certificate**, choose your files and select **Import** to import a new certificate\.

   1. Under **Pathname of code signing certificate on device**, enter the fully qualified path name to the code signing certificate on your device\. For most devices you can leave this field blank\. For the Windows simulator and for devices that do place the certificate in a specific file location, enter the pathname here\.
**Important**  
On the Texas Instruments CC3220SF\-LAUNCHXL, do not include a leading forward slash \(**/**\) in front of the file name if your code signing certificate exists in the root of the file system\. Otherwise, the OTA update fails during authentication with a `file not found` error\.

   1. Select **Create**\.

1. Under **File** select **Select an existing file** then choose **Browse S3**\. A list of your Amazon S3 buckets is displayed\. Choose the bucket that contains your firmware update, and then choose your firmware update in the bucket\.
**Note**  
The Microchip Curiosity PIC32MZEF demo projects produce two binary images with default names of `mplab.production.bin` and `mplab.production.ota.bin`\. Use the second file when you upload an image for OTA updating\.

1. Under **Pathname of file on device**, enter the fully qualified path name to the location on your device where the OTA job will copy the firmware image\. This location varies by platform\.
**Important**  
On the Texas Instruments CC3220SF\-LAUNCHXL, due to security restrictions, the firmware image path name must be `/sys/mcuflashimg.bin`\.

1. Open **File Type** and enter an integer value in the range 0\-255\. The file type you enter will be added to the Job document that is delivered to the MCU\. The MCU firmware/software developer has full ownership on what to do with this value\. Possible scenarios include an MCU that has a secondary processor whose firmware can be updated independently from the primary processor\. When the device receives an OTA update job, it can use the File Type to identify which processor the update is for\.

1. Under **IAM role**, choose a role according to the instructions in [Create an OTA Update service role](create-service-role.md)\. 

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

1. Under **Pathname of code signing certificate on device**, enter the fully qualified path name to the code signing certificate on your device\. For most devices you can leave this field blank\. For the Windows simulator and for devices that do place the certificate in a specific file location, enter the pathname here\.

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

Connect your serial UART terminal to your device\. You should see output that indicates the device is downloading the updated firmware\.

After the device downloads the updated firmware, it restarts and then installs the firmware\. You can see what's happening in the UART terminal\.

For a tutorial that shows you how to use the console to create an OTA update, see [Over\-the\-air updates demo application](ota-demo.md)\.