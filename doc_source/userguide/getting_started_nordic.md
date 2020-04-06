# Getting Started with the Nordic nRF52840\-DK<a name="getting_started_nordic"></a>

This tutorial provides instructions for getting started with the Nordic nRF52840\-DK\. If you do not have the Nordic nRF52840\-DK, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtrUAH/nRF52840-Development-Kit)\.

Before you begin, you need to [Set Up AWS IoT and Amazon Cognito for FreeRTOS Bluetooth Low Energy](ble-demo.md#set-up-ble-demo-aws)\.

To run the FreeRTOS Bluetooth Low Energy demo, you also need an iOS or Android mobile device with Bluetooth and Wi\-Fi capabilities\.

**Note**  
If you are using an iOS device, you need Xcode to build the demo mobile application\. If you are using an Android device, you can use Android Studio to build the demo mobile application\.

## Overview<a name="getting_started_nordic-overview"></a>

This tutorial contains instructions for the following getting started steps:

1. Connecting your board to a host machine\.

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling a FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

1. Interacting with the application running on your board across a serial connection, for monitoring and debugging purposes\.

## Set Up the Nordic Hardware<a name="nordic-setup-hardware"></a>

Connect your host computer to the USB port labeled J2, located directly above the coin cell battery holder on your Nordic nRF52840 board\.

For more information about setting up the Nordic nRF52840\-DK, see the [nRF52840 Development Kit User Guide](http://infocenter.nordicsemi.com/pdf/nRF52840_DK_User_Guide_v1.2.pdf)\.

## Set Up Your Development Environment<a name="nordic-setup-env"></a>

### Download and Install Segger Embedded Studio<a name="install-embedded-studio"></a>

FreeRTOS supports Segger Embedded Studio as a development environment for the Nordic nRF52840\-DK\.

To set up your environment, you need to download and install Segger Embedded Studio on your host computer\.

**To download and install Segger Embedded Studio**

1. Go to the [Segger Embedded Studio Downloads](https://www.segger.com/downloads/embedded-studio/) page, and choose the Embedded Studio for ARM option for your operating system\.

1. Run the installer and follow the prompts to completion\.

### Set Up the FreeRTOS Bluetooth Low Energy Mobile SDK Demo Application<a name="install-mobile-sdks"></a>

To run the FreeRTOS demo project across Bluetooth Low Energy, you need to run the FreeRTOS Bluetooth Low Energy Mobile SDK Demo Application on your mobile device\.

**To set up the the FreeRTOS Bluetooth Low Energy Mobile SDK Demo Application**

1. Follow the instructions in [Mobile SDKs for FreeRTOS Bluetooth Devices](freertos-ble-mobile.md) to download and install the SDK for your mobile platform on your host computer\.

1. Follow the instructions in [FreeRTOS Bluetooth Low Energy Mobile SDK Demo Application](ble-demo.md#ble-sdk-app) to set up the demo mobile application on your mobile device\.

## Establish a Serial Connection<a name="nordic-serial-connection"></a>

Segger Embedded Studio includes a terminal emulator that you can use to receive log messages across a serial connection to your board\.

**To establish a serial connection with Segger Embedded Studio**

1. Open Segger Embedded Studio\.

1. From the top menu, choose **Target**, **Connect J\-Link**\.

1. From the top menu, choose **Tools**, **Terminal Emulator**, **Properties**, and set the properties as instructed in [Installing a Terminal Emulator](uart-term.md)\.

1. From the top menu, choose **Tools**, **Terminal Emulator**, **Connect *port* \(115200,N,8,1\)**\.

**Note**  
The Segger embedded studio terminal emulator does not support an input capability\. For this, use a terminal emulator like PuTTy, Tera Term, or GNU Screen\. Configure the terminal to connect to your board by a serial connection as instructed in [Installing a Terminal Emulator](uart-term.md)\.

## Download and Configure FreeRTOS<a name="nordic-download-and-configure"></a>

After you set up your hardware and environment, you can download FreeRTOS\.

### Download FreeRTOS<a name="nordic-download"></a>

To download FreeRTOS for the Nordic nRF52840\-DK, go to the [FreeRTOS GitHub page](https://github.com/aws/amazon-freertos) and clone the repository\. See the [ README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\. 

**Important**  
In this topic, the path to the FreeRTOS download directory is referred to as `freertos`\.
Space characters in the `freertos` path can cause build failures\. When cloning or copying the repository, make sure the path you create does not contain space characters\.
The maximum length of a file path on Microsoft Windows is 260 characters\. Long FreeRTOS download directory paths can cause build failures\.

### Configure Your Project<a name="nordic-freertos-config-project"></a>

To run the demo, you need to configure your project to work with AWS IoT\. To configure your project to work with AWS IoT, your device must be registered as an AWS IoT thing\. You should have registered your device when you [Set Up AWS IoT and Amazon Cognito for FreeRTOS Bluetooth Low Energy](ble-demo.md#set-up-ble-demo-aws)\.

**To configure your AWS IoT endpoint**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint appears in the **Endpoint** text box\. It should look like `1234567890123-ats.iot.us-east-1.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\. Make a note of the AWS IoT thing name for your device\. 

1. With your AWS IoT endpoint and your AWS IoT thing name on hand, open `freertos/demos/include/aws_clientcredential.h` in your IDE, and specify values for the following `#define` constants:
   + `clientcredentialMQTT_BROKER_ENDPOINT` *Your AWS IoT endpoint*
   + `clientcredentialIOT_THING_NAME` *Your board's AWS IoT thing name*

**To enable the demo**

1. Check that the Bluetooth Low Energy GATT Demo is enabled\. Go to `vendors/nordic/boards/nrf52840-dk/aws_demos/config_files/iot_ble_config.h`, and add `#define IOT_BLE_ADD_CUSTOM_SERVICES ( 1 )` to the list of define statements\.

1. Open `vendors/nordic/boards/nrf52840-dk/aws_demos/config_files/aws_demos_config.h`, and define `CONFIG_MQTT_DEMO_ENABLED`\.

1. Since the Nordic chip comes with very little RAM \(250KB\), the BLE configuration might need to be changed to allow for larger GATT table entries compared to the size of each attribute\. In this way you can adjust the amount of memory the application gets\. To do this, override the definitions of the following attributes in the file `freertos/vendors/nordic/boards/nrf52840-dk/aws_demos/config_files/sdk_config.h`: 
   + NRF\_SDH\_BLE\_VS\_UUID\_COUNT

     The number of vendor\-specific UUIDs\.
   + NRF\_SDH\_BLE\_GATTS\_ATTR\_TAB\_SIZE

     Attribute Table size in bytes\. The size must be a multiple of 4\.

   \(For tests, the location of the file is `freertos/vendors/nordic/boards/nrf52840-dk/aws_tests/config_files/sdk_config.h`\.\) 

## Build and Run the FreeRTOS Demo Project<a name="nordic-build-and-run-example"></a>

After you download FreeRTOS and configure your demo project, you are ready to build and run the demo project on your board\.

**Important**  
If this is the first time that you are running the demo on this board, you need to flash a bootloader to the board before the demo can run\.  
To build and flash the bootloader, follow the steps below, but instead of using the `projects/nordic/nrf52840-dk/ses/aws_demos/aws_demos.emProject` project file, use `projects/nordic/nrf52840-dk/ses/aws_demos/bootloader/bootloader.emProject`\.

**To build and run the FreeRTOS Bluetooth Low Energy demo from Segger Embedded Studio**

1. Open Segger Embedded Studio\. From the top menu, choose **File**, choose **Open Solution**, and then navigate to the project file `projects/nordic/nrf52840-dk/ses/aws_demos/aws_demos.emProject`

1. If you are using the Segger Embedded Studio terminal emulator, choose **Tools** from the top menu, and then choose **Terminal Emulator**, **Terminal Emulator** to display information from your serial connection\.

   If you are using another terminal tool, you can monitor that tool for output from your serial connection\.

1. Right\-click the `aws_demos` demo project in the **Project Explorer**, and choose **Build**\.
**Note**  
If this is your first time using Segger Embedded Studio, you might see you a warning "No license for commercial use"\. Segger Embedded Studio can be used free of charge for Nordic Semiconductor devices\. Choose **Activate Your Free License**, and follow the instructions\.

1. Choose **Debug**, and then choose **Go**\.

   After the demo starts, it waits to pair with a mobile device across Bluetooth Low Energy\.

1. Follow the instructions for the [MQTT over Bluetooth Low Energy Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-mqtt) to complete the demo with the FreeRTOS Bluetooth Low Energy Mobile SDK demo application as the mobile MQTT proxy\.

## Troubleshooting<a name="nordic-troubleshooting"></a>

For general troubleshooting information about Getting Started with FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.