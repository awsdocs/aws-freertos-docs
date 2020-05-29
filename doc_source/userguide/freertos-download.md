# Downloading FreeRTOS<a name="freertos-download"></a>

You can download FreeRTOS from the FreeRTOS console or from the [FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\.

**Note**  
If you're following the **Quick Connect** workflow in the [FreeRTOS console](https://console.aws.amazon.com/freertos), you can ignore the following procedure\.\.<a name="download-free-rtos"></a>

**To download FreeRTOS from the FreeRTOS console**

1. Sign in to the [FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. Under **Predefined configurations**, find **Connect to AWS IoT\- *Platform***, and then choose **Download**\.

1. Unzip the downloaded file to a directory, and copy the directory path\.
**Important**  
In this topic, the path to the FreeRTOS download directory is referred to as `freertos`\.
Space characters in the `freertos` path can cause build failures\. When you clone or copy the repository, make sure the path you that create doesn't contain space characters\.
The maximum length of a file path on Microsoft Windows is 260 characters\. Long FreeRTOS download directory paths can cause build failures\.

**Note**  
If you're getting started with the Cypress CYW954907AEVAL1F or CYW943907AEVAL1F development kits, you must download FreeRTOS from GitHub\. See the [ README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\. Configurations of FreeRTOS for these boards aren't currently available from the FreeRTOS console\.

After you download FreeRTOS, you can continue to [Configuring the FreeRTOS demos](freertos-configure.md)\.