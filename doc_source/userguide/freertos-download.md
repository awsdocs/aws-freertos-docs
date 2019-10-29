# Downloading Amazon FreeRTOS<a name="freertos-download"></a>

You can download Amazon FreeRTOS from the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos) or from the [Amazon FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\. See the [ README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\. 

If you are following the **Quick Connect** workflow in the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), follow the download instructions in the workflow on the console, and ignore the following procedures\.<a name="download-free-rtos"></a>

**To download Amazon FreeRTOS from the Amazon FreeRTOS console**

1. Go to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. Under **Predefined configurations**, find **Connect to AWS IoT\- *Platform***, and then choose **Download**\.

1. Unzip the downloaded file to a folder, and make a note of the folder path\. In the tutorials, this folder is referred to as *<amazon\-freertos>*\. 
**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `<amazon-freertos>` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\. 

**Note**  
If you are getting started with the Cypress CYW954907AEVAL1F or CYW943907AEVAL1F development kits, you must download Amazon FreeRTOS from GitHub\. See the [ README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\. Configurations of Amazon FreeRTOS for these boards are currently not available from the Amazon FreeRTOS console\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. Lengthy Amazon FreeRTOS download directory paths can cause build failures\.  
In the Getting Started documentation, the path to the Amazon FreeRTOS download directory is referred to as `<amazon-freertos>`\.

After you download Amazon FreeRTOS, you can continue to [Configuring the Amazon FreeRTOS Demos](freertos-configure.md)\.