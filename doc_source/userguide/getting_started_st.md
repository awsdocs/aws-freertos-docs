# Getting started with the STMicroelectronics STM32L4 Discovery Kit IoT Node<a name="getting_started_st"></a>

This tutorial provides instructions for getting started with the STMicroelectronics STM32L4 Discovery Kit IoT Node\. If you do not already have the STMicroelectronics STM32L4 Discovery Kit IoT Node, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANsWUAX/STM32L4-Discovery-Kit-IoT-Node)\.

Make sure you have installed the latest Wi\-Fi firmware\. To download the latest Wi\-Fi firmware, see [STM32L4 Discovery kit IoT node, low\-power wireless, Bluetooth Low Energy, NFC, SubGHz, Wi\-Fi](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip)\. Under **Binary Resources**, choose **Inventek ISM 43362 Wi\-Fi module firmware update \(read the readme file for instructions\) **\.

Before you begin, you must configure AWS IoT and your FreeRTOS download to connect your device to the AWS Cloud\. See [First steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the FreeRTOS download directory is referred to as `freertos`\.

## Overview<a name="w15aac13c27c43b9"></a>

This tutorial contains instructions for the following getting started steps:

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling a FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

## Set up your development environment<a name="st-setup-env"></a>

### Install System Workbench for STM32<a name="install-system-workbench"></a>

1. Browse to [OpenSTM32\.org](http://www.openstm32.org/HomePage)\.

1. Register on the OpenSTM32 webpage\. You need to sign in to download System Workbench\.

1. Browse to the [System Workbench for STM32 installer](http://www.openstm32.org/System%2BWorkbench%2Bfor%2BSTM32) to download and install System Workbench\.

If you experience issues during installation, see the FAQs on the [System Workbench website](http://www.openstm32.org/HomePage)\.

## Build and run the FreeRTOS demo project<a name="st-build-and-run-example"></a>

### Import the FreeRTOS demo into the STM32 System Workbench<a name="st-freertos-import-project"></a><a name="st-import-project"></a>

1. Open the STM32 System Workbench and enter a name for a new workspace\.

1. From the **File** menu, choose **Import**\. Expand **General**, choose **Existing Projects into Workspace**, and then choose **Next**\.

1. In **Select Root Directory**, enter `projects/st/stm32l475_discovery/ac6/aws_demos`\.

1. The project `aws_demos` should be selected by default\.

1. Choose **Finish** to import the project into STM32 System Workbench\.

1. From the **Project** menu, choose **Build All**\. Confirm the project compiles without any errors\.

### Run the FreeRTOS demo project<a name="st-run-example"></a>

1. Use a USB cable to connect your STMicroelectronics STM32L4 Discovery Kit IoT Node to your computer\. 

1. From **Project Explorer**, right\-click `aws_demos`, choose **Debug As**, and then choose **Ac6 STM32 C/C\+\+ Application**\.

   If a debug error occurs the first time a debug session is launched, follow these steps:

   1. In STM32 System Workbench, from the **Run** menu, choose **Debug Configurations**\.

   1. Choose **aws\_demos Debug**\. \(You might need to expand **Ac6 STM32 Debugging**\.\)

   1. Choose the **Debugger** tab\.

   1. In **Configuration Script**, choose **Show Generator Options**\.

   1. In **Mode Setup**, set **Reset Mode** to **Software System Reset**\. Choose **Apply**, and then choose **Debug**\. 

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

### Using CMake with FreeRTOS<a name="w15aac13c27c43c13b7"></a>

If you prefer not to use an IDE for FreeRTOS development, you can alternatively use CMake to build and run the demo applications or applications that you have developed using third\-party code editors and debugging tools\.

First create a folder to contain the generated build files \(*build\-folder*\)\.

Use the following command to generate build files:

```
cmake -DVENDOR=st -DBOARD=stm32l475_discovery -DCOMPILER=arm-gcc -S freertos -B build-folder
```

If `arm-none-eabi-gcc` is not in your shell path, you also need to set the `AFR_TOOLCHAIN_PATH` CMake variable\. For example:

```
-D AFR_TOOLCHAIN_PATH=/home/user/opt/gcc-arm-none-eabi/bin
```

For more information about using CMake with FreeRTOS, see [Using CMake with FreeRTOS](getting-started-cmake.md)\.

### Monitoring MQTT messages on the cloud<a name="w15aac13c27c43c13b9"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="st-troubleshooting"></a>

If you see the following in the UART output from the demo application, you need to update the Wi\-Fi module’s firmware:

```
[Tmr Svc] WiFi firmware version is: xxxxxxxxxxxxx
[Tmr Svc] [WARN] WiFi firmware needs to be updated.
```

To download the latest Wi\-Fi firmware, see [STM32L4 Discovery kit IoT node, low\-power wireless, Bluetooth Low Energy, NFC, SubGHz, Wi\-Fi](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip)\. In **Binary Resources**, choose the download link for **Inventek ISM 43362 Wi\-Fi module firmware update**\.

For general troubleshooting information about Getting Started with FreeRTOS, see [Troubleshooting getting started](gsg-troubleshooting.md)\.