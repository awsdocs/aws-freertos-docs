# First Steps<a name="freertos-prereqs"></a>

To get started with FreeRTOS, you need an AWS account, an IAM user with permission to access AWS IoT and FreeRTOS cloud services, and you need one of the supported hardware platforms\. You also need to download FreeRTOS and configure your board's FreeRTOS demo project to work with AWS IoT\. The following sections walk you through these requirements\.

**Note**  
If you're using the Espressif ESP32\-DevKitC, ESP\-WROVER\-KIT, or the ESP32\-WROOM\-32SE, skip these steps and go to [Getting Started with the Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT](getting_started_espressif.md)\.  
If you're using the Nordic nRF52840\-DK, skip these steps and go to [Getting Started with the Nordic nRF52840\-DK](getting_started_nordic.md)\.

1. [Setting Up Your AWS Account and Permissions](freertos-account-and-permissions.md) 

   After you complete the instructions in [Setting Up Your AWS Account and Permissions](freertos-account-and-permissions.md), you can follow the **Quick Connect** workflow in the [FreeRTOS console](https://console.aws.amazon.com/freertos) to quickly connect your board to the AWS Cloud\. If you follow the **Quick Connect** workflow, you do not need to complete the remaining steps in this list\. Note that configurations of FreeRTOS are currently not available on the FreeRTOS console for the following boards:
   + Cypress CYW943907AEVAL1F Development Kit
   + Cypress CYW954907AEVAL1F Development Kit

1. [Registering Your MCU Board with AWS IoT](get-started-freertos-thing.md)

1. [Downloading FreeRTOS](freertos-download.md)

1. [Configuring the FreeRTOS Demos](freertos-configure.md)