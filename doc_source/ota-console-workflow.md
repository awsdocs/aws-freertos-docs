# Creating an OTA Update with the AWS IoT Console<a name="ota-console-workflow"></a>

1. In the navigation pane of the AWS IoT console, choose **Manage**, and then choose **Jobs**\.

1. Choose **Create**\.

1. Under **Create an Amazon FreeRTOS Over\-the\-Air \(OTA\) update job**, choose **Create OTA update job**\.

1. You can deploy an OTA update to a single device or a group of devices\. Under **Select devices to update**, choose **Select**, and then choose the **Things** tab to update a single device or the **Thing Groups** tab to update a group of devices\.

1. If you are updating a single device, select the check box next to the IoT thing associated with your device\. If you are updating a group of devices, select the check box next to the thing group associated with your devices\.

When creating an OTA update it is best practice to digitally sign your firmware image\. You can use Code Signing for Amazon FreeRTOS to sign a new firmware image, use a previously signed firmware image, or manually sign your firmware image\. The information you specify in the console depends on which method you use to sign your firmware\. <a name="use-code-signing-for-freertos"></a>

**Using Code Signing for Amazon FreeRTOS**

1. Under **Select and sign your firmware image**, select **Sign a new firmware image for me**\.

1. Under **Device hardware platform** select your hardware platform\.
**Note**  
Only hardware platforms that have been qualified for Amazon FreeRTOS will be displayed in this drop down box\. If you are using a non\-qualified platform, you will need to use the CLI to create the OTA update\. For more information, see [Creating an OTA Update with the AWS CLI](ota-cli-workflow.md)\.

1. Under **Pathname of code signing certificate on device**, type the fully qualified path name to the code signing certificate on your device\. This may different for different platforms\.

1. Under **Pathname of firmware image on device**, type the fully qualified path name to the location where the firmware image will be copied onto your device\. The OTA demo application expects the code signing certificate to be in the root directory when running on the Texas Instruments CC3200SF\-LAUNCHXL, so type the name of your code signing certificate into this field\. When running on the Microchip Curiosity PIC32MZEF the code signing certificate is hard coded into the application so this field is ignored but you must type in some arbitrary text into this field\.

1. Under **Select your firmware image in S3**, choose **Select**, and then choose the Amazon S3 bucket that contains your firmware image, and then choose your firmware image\.

1. Under **Code signing certificate**, choose **Select**, and then choose your code\-signing certificate\. Code\-signing certificates are listed by ARN\. The ARN for your certificate was displayed when you registered it with ACM\.<a name="previously-signed"></a>

**Using a previously signed firmware image**

1. Under **Select and sign your firmware image**, select **Select a previously signed firmware image**\.

1. Under **Pathname of firmware image on device**, type the fully qualified path name to the location where the firmware image will be copied onto your device\. This may be different on different platforms\.

1. Under **Previous code signing job**, choose **Select**, and then choose the previous code signing job used to sign the firmware image you are using for the OTA update\.<a name="custom-signed"></a>

**Using a custom signed firmware image**

1. Under **Pathname of code signing certificate on device**, type the fully qualified path name to the code signing certificate on your device\. This may different for different platforms\.

1. Under **Pathname of firmware image on device**, type the fully qualified path name to the location where the firmware image will be copied onto your device\. This may be different on different platforms\.

1. Under **Signature**, paste your PEM format signature\.

1. Under **Original hash algorithm**, select the hash algorithm used when creating your file signature\.

1. Under **Original encryption algorithm**, select the algorithm used when creating your file signature\.

1. Under **Select your firmware image in Amazon S3, select the Amazon S3 bucket and the signed firmware image in the Amazon S3 bucket\.**

After specifying code signing information, specify the OTA update job type, service role, and an ID for your update\.

**Note**  
Do not use any personally identifiable information in your OTA update job ID\. Examples of personally identifiable information include:  
Your name
Your IP address
Your email address
Your location
Bank details
Medical information

1. Under **Job type**, choose **Your job will complete after deploying to the selected devices/groups \(snapshot\)**\.

1. Under **IAM role for OTA update job**, choose your OTA service role\.

1. Type an alphanumeric ID for your job\.

1. Choose **Create**\.

The job appears with a status of **IN PROGRESS** in the AWS IoT console window\.

**Note**  
The AWS IoT console does not update the state of jobs automatically\. Refresh your browser to see updates\.

Connect your serial UART terminal to your device\. You should see output that indicates the device is downloading the updated firmware\.

After the device downloads the updated firmware, it restarts and then installs the firmware\. You can see what's happening in the UART terminal\.

For a complete walk through of using the console to create an OTA update, see [OTA Demo Application](ota-demo.md)