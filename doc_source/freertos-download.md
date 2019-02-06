# Downloading Amazon FreeRTOS<a name="freertos-download"></a>

You can download Amazon FreeRTOS from the Amazon FreeRTOS console or from GitHub\.

**Important**  
If you are using an Espressif or Nordic board, you must download Amazon FreeRTOS from GitHub\.

If you are following the **Quick Connect** workflow in the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), follow the download instructions in the workflow on the console, and ignore the following procedures\.<a name="download-free-rtos"></a>

**To download Amazon FreeRTOS from the Amazon FreeRTOS console**

1. Go to the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. Under **Predefined configurations**, find **Connect to AWS IoT\- *Platform***, and then choose **Download**\.

1. Unzip the downloaded file to the `AmazonFreeRTOS` folder, and make a note of the folder path\.<a name="download-free-rtos-git"></a>

**To download Amazon FreeRTOS from GitHub**

1. Browse to the [Amazon FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\.

1. Choose **Clone or download**\.

1. From the command line on your computer, clone the repository to a directory on your host machine\.

   ```
   git clone https://github.com/aws/amazon-freertos.git
   ```

1. From the `amazon-freertos` directory, check out the branch that you want to use\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the Amazon FreeRTOS download directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.  
In the Getting Started documentation, the path to the Amazon FreeRTOS download directory is referred to as `<BASE_FOLDER>`\.

After you download Amazon FreeRTOS, you can continue to [Configuring the Amazon FreeRTOS Demos](freertos-configure.md)\.