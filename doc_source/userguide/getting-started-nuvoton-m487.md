# Getting started with the Nuvoton NuMaker\-IoT\-M487<a name="getting-started-nuvoton-m487"></a>

This tutorial provides instructions for getting started with the Nuvoton NuMaker\-IoT\-M487 development board\. The series microcontroller, and includes built\-in RJ45 Ethernet and Wi\-Fi modules\. If you don't have the Nuvoton NuMaker\-IoT\-M487, visit the [AWS Partner Device Catalog](https://devices.amazonaws.com/detail/a3G0h000000Tg9cEAC/NuMaker-IoT-M487) to purchase one from our partner\. 

Before you begin, you must configure AWS IoT and your FreeRTOS software to connect your development board to the AWS Cloud\. For instructions, see [First steps](freertos-prereqs.md)\. In this tutorial, the path to the FreeRTOS download directory is referred to as `freertos`\.

## Overview<a name="getting-started-nuvoton-m487-overview"></a>

This tutorial guides you through the following steps:

1. Install software on your host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross\-compile a FreeRTOS demo application to a binary image\.

1. Load the application binary image to your board, and then run the application\.

## Set up your development environment<a name="gsg-nuvoton-m487-setup-env"></a>

The Keil MDK Nuvoton edition is designed for developing and debugging applications for Nuvoton M487 boards\. The Keil MDK v5 Essential, Plus, or Pro version should also work for the Nuvoton M487 \(Cortex\-M4 core\) MCU\. You can download the Keil MDK Nuvoton edition with a price discount for the Nuvoton Cortex\-M4 series MCUs\. The Keil MDK is only supported on Windows\.

**To install the development tool for the NuMaker\-IoT\-M487**

1. Download the [ Keil MDK Nuvoton Edition](https://store.developer.arm.com/store/embedded-iot-software-tools/keil-mdk-nuvoton-edition?edition=1164) from the Keil MDK website\. 

1. Install the Keil MDK on your host machine using your license\. The Keil MDK includes the Keil µVision IDE, a C/C\+\+ compilation toolchain, and the µVision debugger\.

   If you experience issues during installation, contact [Nuvoton](https://www.nuvoton.com/contact-us) for assistance\.

1. Install the **Nu\-Link\_Keil\_Driver\_V3\.00\.6951** \(or latest version\), which is on the [ Nuvoton Development Tool](https://www.nuvoton.com/tool-and-software/software-development-tool/driver/) page\.

## Build and run the FreeRTOS demo project<a name="gsg-nuvoton-m487-build-run"></a>

**To build the FreeRTOS demo project**

1. Open the Keil µVision IDE\.

1. On the **File** menu, choose **Open**\. In the **Open file** dialog box, make sure the file type selector is set to **Project Files**\.

1. Choose either the Wi\-Fi or Ethernet demo project to build\.
   + To open the Wi\-Fi demo project, choose the target project `aws_demos.uvproj` in the `freertos\projects\nuvoton\numaker_iot_m487_wifi\uvision\aws_demos` directory\. 
   + To open the Ethernet demo project, choose the target project `aws_demos_eth.uvproj` in the `freertos\projects\nuvoton\numaker_iot_m487_wifi\uvision\aws_demos_eth` directory\. 

1. To make sure your settings are correct to flash the board, right\-click the `aws_demo` project in the IDE, and then choose **Options**\. \(See [Troubleshooting](#gsg-nuvoton-m487-troubleshoot) for more details\.\)

1. On the **Utilities** tab, verify that **Use Target Driver for Flash Programming** is selected, and that **Nuvoton Nu\-Link Debugger** is set as the target driver\. 

1. On the **Debug** tab, next to **Nuvoton Nu\-Link Debugger**, choose **Settings**\.

1. Verify that the **Chip Type** is set to **M480**\. 

1. In the Keil µVision IDE **Project** navigation pane, choose the `aws_demos` project\. On the **Project** menu, choose **Build Target**\. 

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\. 

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2)\.

1. In the navigation pane, choose **Test** to open the MQTT client\. 

1. In **Subscription topic**, enter ***your\-thing\-name*/example/topic**, and then choose **Subscribe to topic**\. 

**To run the FreeRTOS demo project**

1. Connect your Numaker\-IoT\-M487 board to your host machine \(computer\)\. 

1. Rebuild the project\. 

1. In the Keil µVision IDE, on the **Flash** menu, choose **Download**\. 

1. On the **Debug** menu, choose **Start/Stop Debug Session**\.

1. When the debugger stops at the breakpoint in `main()`, open the **Run** menu, and then choose **Run \(F5\)**\.

   You should see MQTT messages sent by your device in the MQTT client in the AWS IoT console\. 

## Using CMake with FreeRTOS<a name="gsg-nuvoton-m487-cmake"></a>

You can also use CMake to build and run the FreeRTOS demo applications or applications you have developed using third\-party code editors and debugging tools\. 

Make sure you have installed the CMake build system\. Follow the instructions in [Using CMake with FreeRTOS](getting-started-cmake.md), and then follow the steps in this section\.

**Note**  
Be sure the path to the location of the compiler \(Keil\) is in your Path system variable, for example, `C:\Keil_v5\ARM\ARMCC\bin`\. 

You can also use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2)\.

1. In the navigation pane, choose **Test** to open the MQTT client\. 

1. In **Subscription topic**, enter ***your\-thing\-name*/example/topic**, and then choose **Subscribe to topic**\. 

**To generate build files from source files and run the demo project**

1. On your host machine, open the command prompt and navigate to the *freertos* folder\. 

1. Create a folder to contain the generated build file\. We will refer to this folder as the *BUILD\_FOLDER*\. 

1. Generate the build files for either the Wi\-Fi or Ethernet demo\.
   + For Wi\-Fi:

     Navigate to the directory that contains the source files for the FreeRTOS demo project\. Then, generate the build files by running the following command\. 

     ```
     cmake -DVENDOR=nuvoton -DBOARD=numaker_iot_m487_wifi -DCOMPILER=arm-keil -S . -B BUILD_FOLDER -G Ninja 
     ```
   + For Ethernet:

     Navigate to the directory that contains the source files for the FreeRTOS demo project\. Then, generate the build files by running the following command\. 

     ```
     cmake -DVENDOR=nuvoton -DBOARD=numaker_iot_m487_wifi -DCOMPILER=arm-keil -DAFR_ENABLE_ETH=1 -S . -B BUILD_FOLDER -G Ninja 
     ```

1. Generate the binary to flash onto the M487 by running the following command\.

   ```
   cmake --build BUILD_FOLDER 
   ```

   At this point, the binary file `aws_demos.bin` should be in the `BUILD_FOLDER/vendors/Nuvoton/boards/numaker_iot_m487_wifi` folder\. 

1. To configure the board for flashing mode, make sure the MSG switch \(No\.4 of ISW1 on ICE\) is switched ON\. When you plug in the board, a window \(and drive\) will be assigned\. \(See [Troubleshooting](#gsg-nuvoton-m487-troubleshoot)\.\)

1. Open a terminal emulator to view the messages over UART\. Follow the instructions at [Installing a terminal emulator](gsg-troubleshooting.md#uart-term)\.

1. Run the demo project by copying the generated binary onto the device\. 

   If you subscribed to the MQTT topic with the AWS IoT MQTT client, you should see MQTT messages sent by your device in the AWS IoT console\. 

## Troubleshooting<a name="gsg-nuvoton-m487-troubleshoot"></a>
+ If your windows can’t recognize the device `VCOM`, install the NuMaker windows serial port driver from the link [Nu\-Link USB Driver v1\.6](https://www.nuvoton.com/export/resource-files/Nu-Link_USB_Driver_V1.6.zip)\.
+ If you connect your device to the Keil MDK \(IDE\) through Nu\-Link, make sure the MSG switch \(No\.4 of ISW1 on ICE\) is OFF, as shown\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/nuvoton-m487-gsg.png)

If you experience issues setting up your development environment or connecting to your board, contact [Nuvoton](http://www.nuvoton.com/contact-us/)\.

### Debugging FreeRTOS projects in Keil μVision<a name="gsg-nuvoton-m487-troubleshoot-debug-projects"></a>

**To start a debug session in Keil μVision**

1. Open Keil μVision\. 

1. Follow the steps to build the FreeRTOS demo project in [Build and run the FreeRTOS demo project](#gsg-nuvoton-m487-build-run)\.

1. On the **Debug** menu, choose **Start/Stop Debug Session**\. 

   The **Call Stack \+ Locals** window appears when you start a debug session\. μVision flashes the demo to the board, runs the demo, and stops at the beginning of the `main()` function\. 

1. Set breakpoints in your project's source code, and then run the code\. The project should look something like the following\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/numaker-iot-m487-debug.png)

### Troubleshooting μVision debug settings<a name="gsg-nuvoton-m487-troubleshoot-debug"></a>

If you encounter problems while debugging an application, check that your debug settings are set correctly in Keil μVision\.

**To verify that the μVision debug settings are correct**

1. Open Keil μVision\. 

1. Right\-click the `aws_demo` project in the IDE, and then choose **Options**\. 

1. On the **Utilities** tab, verify that **Use Target Driver for Flash Programming** is selected, and that **Nuvoton Nu\-Link Debugger** is set as the target driver\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/nulink-debugger-target.png)

1. On the **Debug** tab, next to **Nuvoton Nu\-Link Debugger**, choose **Settings**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/nulink-driver-setup.png)

1. Verify that the **Chip Type** is set to **M480**\. 