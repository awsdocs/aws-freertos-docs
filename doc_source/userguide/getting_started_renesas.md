# Getting Started with the Renesas Starter Kit\+ for RX65N\-2MB<a name="getting_started_renesas"></a>

This tutorial provides instructions for getting started with the Renesas Starter Kit\+ for RX65N\-2MB\. If you do not have the Renesas RSK\+ for RX65N\-2MB, visit the AWS Partner Device Catalog, and purchase one from our [partners](https://devices.amazonaws.com/detail/a3G0L00000AAOkeUAH/Renesas-Starter-Kit+-for-RX65N-2MB)\.

Before you begin, you must configure AWS IoT and your FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the FreeRTOS download directory is referred to as `<freertos>`\.

## Overview<a name="gsg-renesas-overview"></a>

This tutorial contains instructions for the following getting started steps:

1. Connecting your board to a host machine\.

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling a FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

## Set Up the Renesas Hardware<a name="renesas-setup-hardware"></a>

**To set up the RSK\+ for RX65N\-2MB**

1. Connect the positive \+5V power adapter to the PWR connector on the RSK\+ for RX65N\-2MB\.

1. Connect your computer to the USB2\.0 FS port on the RSK\+ for RX65N\-2MB\.

1. Connect your computer to the USB\-to\-serial port on the RSK\+ for RX65N\-2MB\.

1. Connect a router or internet\-connected Ethernet port to the Ethernet port on the RSK\+ for RX65N\-2MB\.

**To set up the E2 Lite Debugger module**

1. Use the 14\-pin ribbon cable to connect the E2 Lite Debugger module to the ‘E1/E2 Lite’ port on the RSK\+ for RX65N\-2MB\.

1. Use a USB cable to connect the E2 Lite debugger module to your host machine\. When the E2 Lite debugger is connected to both the board and your computer, a green ‘ACT’ LED on the debugger flashes\.

1. After the debugger is connected to your host machine and RSK\+ for RX65N\-2MB, the E2 Lite debugger drivers begin installing\.

   Note that administrator privileges are required to install the drivers\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/renesas-board3.png)

## Set Up Your Development Environment<a name="renesas-setup-env"></a>

To set up FreeRTOS configurations for the RSK\+ for RX65N\-2MB, use the Renesas e2studio IDE and CC\-RX compiler\. 

**Note**  
The Renesas e2studio IDE and CC\-RX compiler are only supported on Windows 7, 8, and 10 operating systems\.

**To download and install e2studio**

1. Go to the [Renesas e2studio installer](https://www.renesas.com/us/en/software/D4000894.html) download page, and download the offline installer\.

1. You are directed to a Renesas Login page\.

   If you have an account with Renesas, enter your user name and password and then choose **Login**\.

   If you do not have an account, choose **Register now**, and follow the first registration steps\. You should receive an email with a link to activate your Renesas account\. Follow this link to complete your registration with Renesas, and then log in to Renesas\.

1. After you log in, download the e2studio installer to your computer\.

1. Open the installer and follow the steps to completion\.

For more information, see the [e2studio](https://www.renesas.com/us/en/products/software-tools/tools/ide/e2studio.html#productInfo) on the Renesas website\.

**To download and install the RX Family C/C\+\+ Compiler Package**

1. Go to the [RX Family C/C\+\+ Compiler Package](https://www.renesas.com/us/en/software/D4000890.html) download page, and download the V3\.00\.00 package\.

1. Open the executable and install the compiler\.

For more information, see the [C/C\+\+ Compiler Package for RX Family](https://www.renesas.com/us/en/products/software-tools/tools/compiler-assembler/compiler-package-for-rx-family.html#productInfo) on the Renesas website\.

**Note**  
The compiler is available free for evaluation version only and valid for 60 days\. On the 61st day, you need to get a License Key\. For more information, see [Evaluation Software Tools](https://www.renesas.com/us/en/products/software-tools/evaluation-software-tools.html)\.

## Build and Run FreeRTOS Samples<a name="renesas-build-and-run-example"></a>

Now that you have configured the demo project, you are ready to build and run the project on your board\.

### Build the FreeRTOS Demo in e2studio<a name="renesas-freertos-import-project"></a>

**To import and build the demo in e2studio**

1. Launch e2studio from the Start menu\. 

1. On the **Select a directory as a workspace** window, browse to the folder that you want to work in, and choose **Launch**\.

1. The first time you open e2studio, the **Toolchain Registry** window opens\. Choose **Renesas Toolchains**, and confirm that **CC\-RX v3\.00\.00** is selected\. Choose **Register**, and then choose **OK**\.

1. If you are opening e2studio for the first time, the **Code Generator Registration** window appears\. Choose **OK**\.

1. The **Code Generator COM component register** window appears\. Under **Please restart e2studio to use Code Generator**, choose **OK**\.

1. The **Restart e2studio** window appears\. Choose **OK**\.

1. e2studio restarts\. On the **Select a directory as a workspace** window, choose **Launch**\.

1. On the e2studio welcome screen, choose the **Go to the e2studio workbench** arrow icon\.

1. Right\-click the **Project Explorer** window, and choose **Import**\.

1. In the import wizard, choose **General**, **Existing Projects into Workspace**, and then choose **Next**\.

1. Choose **Browse**, locate the directory `projects/renesas/rx65n-rsk/e2studio/aws_demos`, and then choose **Finish**\. 

1. From **Project** menu, choose **Project**, **Build All**\.

   The build console issues a warning message that the License Manager is not installed\. You can ignore this message unless you have a license key for the CC\-RX compiler\. To install the License Manager, see the [License Manager](https://www.renesas.com/us/en/software/D4000398.html) download page\.

### Run the FreeRTOS Project<a name="renesas-run"></a>

**To run the project in e2studio**

1. Confirm that you have connected the E2 Lite Debugger module to your RSK\+ for RX65N\-2MB

1. From the top menu, choose **Run**, **Debug Configuration**\.

1. Expand **Renesas GDB Hardware Debugging**, and choose **aws\_demos HardwareDebug**\.

1. Choose the **Debugger** tab, and then choose the **Connection Settings** tab\. Confirm that your connection settings are correct\.

1. Choose **Debug** to download the code to your board and begin debugging\.

   You might be prompted by a firewall warning for `e2-server-gdb.exe`\. Check **Private networks, such as my home or work network**, and then choose **Allow access**\.

1. e2studio might ask to change to **Renesas Debug Perspective**\. Choose **Yes**\.

   The green 'ACT' LED on the E2 Lite Debugger illuminates\.

1. After the code is downloaded to the board, choose **Resume** to run the code up to the first line of the main function\. Choose **Resume** again to run the rest of the code\.

### Monitoring MQTT Messages on the Cloud<a name="gsg-renesas-monitor-mqtt"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.

For the latest projects released by Renesas, see the `renesas-rx` fork of the `amazon-freertos` repository on [GitHub](https://github.com/renesas-rx/amazon-freertos)\.

## Troubleshooting<a name="renesas-troubleshooting"></a>

For general troubleshooting information about Getting Started with FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.