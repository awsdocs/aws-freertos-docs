# FreeRTOS Porting<a name="porting-guide"></a>

## What is FreeRTOS<a name="porting-what-is-afr"></a>

Developed in partnership with the world's leading chip companies over a 20\-year period, and now downloaded every 170 seconds, FreeRTOS is a market\-leading real\-time operating system \(RTOS\) for microcontrollers and small microprocessors\. Distributed freely under the MIT open source license, FreeRTOS includes a kernel and a growing set of libraries suitable for use across all industry sectors\. FreeRTOS is built with an emphasis on reliability and ease of use\. FreeRTOS includes libraries for connectivity, security, and over\-the\-air \(OTA\) updates, and demo applications that demonstrate FreeRTOS features on [qualified boards](https://devices.amazonaws.com/search?page=1&sv=freertos)\.

 For more information, visit [FreeRTOS\.org](https://www.freertos.org/)\.

## Porting FreeRTOS to your IoT board<a name="porting-to-your-device"></a>

You will need to port FreeRTOS software libraries to your microcontroller\-based board based on its features and your application\. 

**To port FreeRTOS to your device**

1. Follow the instructions in [Downloading FreeRTOS for Porting](porting-download.md) to download the latest version of FreeRTOS for porting\.

1. Follow the instructions in [Setting up your workspace and project for porting](porting-set-up-project.md) to configure the files and folders in your FreeRTOS download for porting and testing\.

1. Follow the instructions in [Porting the FreeRTOS libraries](afr-porting.md) to port the FreeRTOS libraries to your device\. Each porting topic includes instructions on testing the ports\.

### Porting FAQs<a name="afr-porting-faqs"></a>

*What is a FreeRTOS port?*  
A FreeRTOS port is a board\-specific implementation of APIs for the required FreeRTOS libraries and the FreeRTOS kernel that your platform supports\. The port enables the APIs to work on the board, and implements the required integration with the device drivers and BSPs that are provided by the platform vendor\. Your port should also include any configuration adjustments \(e\.g\. clock rate, stack size, heap size\) that are required by the board\.

If you have questions about porting that are not answered on this page or in the rest of the FreeRTOS Porting Guide, please [ see the available FreeRTOS support options](https://freertos.org/RTOS-contact-and-support.html)\.