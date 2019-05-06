# Getting Started with the Cypress CYW943907AEVAL1F Development Kit<a name="getting_started_cypress_43"></a>

This tutorial provides instructions for getting started with the Cypress CYW943907AEVAL1F Development Kit\. If you do not have the Cypress CYW943907AEVAL1F Development Kit, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AAPg0UAH/CYW943907AEVAL1F)\.

**Note**  
This tutorial walks you through setting up and running the MQTT Hello World demo\. The Amazon FreeRTOS port for this board currently does not support the TCP server and client demos\.

Before you begin, you must configure AWS IoT and your Amazon FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the Amazon FreeRTOS download directory is referred to as `BASE_FOLDER`\.

**Important**  
The maximum length of a file path on Microsoft Windows is 260 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the Amazon FreeRTOS download directory is fewer than 43 characters long\.  
As noted in [Downloading Amazon FreeRTOS](freertos-download.md), Amazon FreeRTOS ports for Cypress are currently only available on [GitHub](https://github.com/aws/amazon-freertos)\.

## Overview<a name="w3aab7c19b9c11"></a>

This tutorial contains instructions for the following getting started steps:

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling an Amazon FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

1. Interacting with the application running on your board across a serial connection, for monitoring and debugging purposes\.

## Setting Up Your Development Environment<a name="cypress-setup-env"></a>

### Download and Install the WICED Studio SDK<a name="install-wiced-studio"></a>

In this Getting Started guide, you use the Cypress WICED Studio SDK to program your board with the Amazon FreeRTOS demo\. Visit the [WICED Software](https://www.cypress.com/products/wiced-software) website to download the WICED Studio SDK from Cypress\. You must register for a free Cypress account to download the software\. The WICED Studio SDK is compatible with Windows, macOS, and Linux operating systems\.

**Note**  
Some operating systems require additional installation steps\. Make sure that you read and follow all installation instructions for the operating system and version of WICED Studio that you are installing\.

### Set Environment Variables<a name="cypress-environment"></a>

Before you use WICED Studio to program your board, you must create an environment variable for the WICED Studio SDK installation directory\. If WICED Studio is running while you create your variables, you need to restart the application after you set your variables\.

**Note**  
The WICED Studio installer creates two separate folders named `WICED-Studio-6.2` on your machine\. When you define the `WICED_STUDIO_SDK_PATH` environment variable, be sure to specify the full installation path of the WICED Studio SDK, and not the installation path of the WICED Studio UI\. In Windows and macOS, the `WICED-Studio-6.2` folder for the SDK is created in the `Documents` folder by default\.

**To create the environment variable on Windows**

1. Open **Control Panel**, choose **System**, and then choose **Advanced System Settings**\.

1. On the **Advanced** tab, choose **Environment Variables**\.

1. Under **User variables**, choose **New**\.

1. For **Variable name**, enter **WICED\_STUDIO\_SDK\_PATH**\. For **Variable value**, enter the WICED Studio SDK installation directory\.

**To create the environment variable on Linux or macOS**

1. Open the `/etc/profile` file on your machine, and add the following to the last line of the file:

   ```
   export WICED_STUDIO_SDK_PATH=installation-path/WICED-Studio-6.2
   ```

1. Restart your machine\.

1. Open a terminal and run the following commands:

   ```
   cd <BASE_FOLDER>/lib/third_party/mcu_vendor/cypress/WICED_SDK
   ```

   ```
   perl platform_adjust_make.pl
   ```

   ```
   chmod +x make
   ```

## Establishing a Serial Connection<a name="cypress-serial-connection"></a>

**To establish a serial connection between your host machine and your board**

1. Connect the board to your host computer with a USB Standard\-A to Micro\-B cable\.

1. Identify the USB serial port number for the connection to the board on your host computer\.

1. Start a serial terminal and open a connection with the following settings:
   + Baud rate: 115200
   + Data: 8 bit
   + Parity: None
   + Stop bits: 1
   + Flow control: None

For more information about installing a terminal and setting up a serial connection, see [Installing a Terminal Emulator](uart-term.md)\.

## Build and Run the Amazon FreeRTOS Demo Project<a name="cypress-build-and-run-example"></a>

After you set up a serial connection to your board, you can build the Amazon FreeRTOS demo project, flash the demo to your board, and then run the demo\.

**To build and run the Amazon FreeRTOS demo project in WICED Studio**

1. Launch WICED Studio\.

1. From the **File** menu, choose **Import**\. Expand the **General** folder, choose **Existing Projects into Workspace**, and then choose **Next**\.

1. In **Select root directory**, enter `<BASE_FOLDER>\demos\cypress\CYW943907AEVAL1F\wicedstudio`, and then choose **Open**\.

1. Under **Projects**, the **aws\_demo** project should be selected\. Choose **Finish** to import the project\. The target project **aws\_demo** should appear in the **Make Target** window\.

1. Expand the **WICED Platform** menu, and choose **WICED Filters off**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/wiced-filters.png)

1. In the **Make Target** window, expand **aws\_demo**, right\-click the `demo.aws_demo` file, and then choose **Build Target** to build and download the demo to your board\. The demo should run automatically after it is built and downloaded to your board\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/wiced-build-43.png)

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="cypress-troubleshooting"></a>
+ If you are using Windows, you might receive the following error when you build and run the demo project:

  ```
  : recipe for target 'download_dct' failed
  make.exe[1]: *** [download_dct] Error 1
  ```

  To troubleshoot this error, do the following:

  1. Browse to `WICED-Studio-SDK-PATH\WICED-Studio-6.2\43xxx_Wi-Fi\tools\OpenOCD\Win32` and double\-click on `openocd-all-brcm-libftdi.exe`\.

  1. Browse to `WICED-Studio-SDK-PATH\WICED-Studio-6.2\43xxx_Wi-Fi\toots\drivers\CYW9WCD1EVAL1` and double\-click on `InstallDriver.exe`\.
+ If you are using Linux or macOS, you might receive the following error when you build and run the demo project:

  ```
  make[1]: *** [download_dct] Error 127
  ```

  To troubleshoot this error, use the following command to update the libusb\-dev package:

  ```
  sudo apt-get install libusb-dev
  ```

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.