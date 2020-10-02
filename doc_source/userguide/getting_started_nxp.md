# Getting started with the NXP LPC54018 IoT Module<a name="getting_started_nxp"></a>

This tutorial provides instructions for getting started with the NXP LPC54018 IoT Module\. If you do not have an NXP LPC54018 IoT Module, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtAUAX/LPC54018-IoT-Solution)\. Use a USB cable to connect your NXP LPC54018 IoT Module to your computer\.

Before you begin, you must configure AWS IoT and your FreeRTOS download to connect your device to the AWS Cloud\. See [First steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the FreeRTOS download directory is referred to as `freertos`\.

## Overview<a name="w15aac13c27c39b7"></a>

This tutorial contains instructions for the following getting started steps:

1. Connecting your board to a host machine\.

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling a FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

## Set up the NXP hardware<a name="nxp-setup-hardware"></a>

**To set up the NXP LPC54018**
+ Connect your computer to the USB port on the NXP LPC54018\.

**To set up the JTAG debugger**

You need a JTAG debugger to launch and debug your code running on the NXP LPC54018 board\. FreeRTOS was tested using an OM40006 IoT Module\. For more information about supported debuggers, see the *User Manual for NXP LPC54018 IoT Module* that is available from the [ OM40007 LPC54018 IoT Module](https://www.nxp.com/products/processors-and-microcontrollers/arm-microcontrollers/general-purpose-mcus/lpc54018-iot-module-for-the-lpc540xx-family-of-mcus:OM40007) product page\. 

1. If you're using an OM40006 IoT Module debugger, use a converter cable to connect the 20\-pin connector from the debugger to the 10\-pin connector on the NXP IoT module\. 

1. Connect the NXP LPC54018 and the OM40006 IoT Module Debugger to the USB ports on your computer using mini\-USB to USB cables\.

## Set up your development environment<a name="setup-env_nxp"></a>

FreeRTOS supports two IDEs for the NXP LPC54018 IoT Module: IAR Embedded Workbench and MCUXpresso\.

Before you begin, install one of these IDEs\.

**To install IAR Embedded Workbench for ARM**

1. Browse to [ IAR Embedded Workbench for ARM](https://www.iar.com/iar-embedded-workbench/#!?architecture=Arm) and download the software\.
**Note**  
IAR Embedded Workbench for ARM requires Microsoft Windows\.

1. Run the installer and follow the prompts\.

1. In the **License Wizard**, choose **Register with IAR Systems to get an evaluation license**\.

1. Put the bootloader on the device before attempting to run any demos\.

**To install MCUXpresso from NXP**

1. Download and run the MCUXpresso installer from [NXP](https://www.nxp.com/support/developer-resources/software-development-tools/mcuxpresso-software-and-tools/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)\.
**Note**  
Versions 10\.3\.x and later are supported\.

1. Browse to [MCUXpresso SDK](https://www.nxp.com/support/developer-resources/software-development-tools/mcuxpresso-software-and-tools/mcuxpresso-software-development-kit-sdk:MCUXpresso-SDK) and choose **Build your SDK\.**
**Note**  
Versions 2\.5 and later are supported\.

1. Choose **Select Development Board**\.

1. Under **Select Development Board**, in **Search by Name**, enter **LPC54018\-IoT\-Module**\.

1. Under **Boards**, choose **LPC54018\-IoT\-Module**\.

1. Verify the hardware details, and then choose **Build MCUXepresso SDK**\.

1. The SDK for Windows using the MCUXpresso IDE is already built\. Choose **Download SDK**\. If you are using another operating system, under **Host OS**, choose your operating system, and then choose **Download SDK**\. 

1. Start the MCUXpresso IDE, and choose the **Installed SDKs** tab\.

1. Drag and drop the downloaded SDK archive file into the **Installed SDKs** window\.

If you experience issues during installation, see [NXP Support](https://www.nxp.com/support/support:SUPPORTHOME?tid=sbmenu) or [NXP Developer Resources](https://www.nxp.com/support/developer-resources:DEVELOPER_HOME)\.

## Build and run the FreeRTOS Demo project<a name="nxp-build-and-run"></a>

### Import the FreeRTOS demo into your IDE<a name="nxp-freertos-import-project"></a><a name="nxp-load-project"></a>

**To import the FreeRTOS sample code into the IAR Embedded Workbench IDE**

1. Open IAR Embedded Workbench, and from the **File** menu, choose **Open Workspace**\.

1. In the **search\-directory** text box, enter `projects/nxp/lpc54018iotmodule/iar/aws_demos`, and choose **aws\_demos\.eww**\.

1. From the **Project** menu, choose **Rebuild All**\.

**To import the FreeRTOS sample code into the MCUXpresso IDE**

1. Open MCUXpresso, and from the **File** menu, choose **Open Projects From File System**\.

1. In the **Directory** text box, enter `projects/nxp/lpc54018iotmodule/mcuxpresso/aws_demos`, and choose **Finish**

1. From the **Project** menu, choose **Build All**\.

### Run the FreeRTOS demo project<a name="nxp-run-example"></a>

**To run the FreeRTOS demo project with the IAR Embedded Workbench IDE**

1. In your IDE, from the **Project** menu, choose **Make**\.

1. From the **Project** menu, choose **Download and Debug**\.

1. From the **Debug** menu, choose **Start Debugging**\.

1. When the debugger stops at the breakpoint in `main`, from the **Debug** menu, choose **Go\.**
**Note**  
If a **J\-Link Device Selection** dialog box opens, choose **OK** to continue\. In the **Target Device Settings** dialog box, choose **Unspecified**, choose **Cortex\-M4**, and then choose **OK**\. You only need to be do this once\.

**To run the FreeRTOS demo project with the MCUxpresso IDE**

1. In your IDE, from the **Project** menu, choose **Build**\.

1. If this is your first time debugging, choose the `aws_demos` project and from the **Debug** toolbar, choose the blue debug button\.

1. Any detected debug probes are displayed\. Choose the probe you want to use, and then choose **OK** to start debugging\.
**Note**  
When the debugger stops at the breakpoint in `main()`, press the debug restart button ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/reset.png) once to reset the debugging session\. \(This is required due to a bug with MCUXpresso debugger for NXP54018\-IoT\-Module\)\.

1. When the debugger stops at the breakpoint in `main()`, from the **Debug** menu, choose **Go**\.

### Monitoring MQTT messages on the cloud<a name="w15aac13c27c39c13b7"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="getting_started_nxp_troubleshooting"></a>

For general troubleshooting information about Getting Started with FreeRTOS, see [Troubleshooting getting started](gsg-troubleshooting.md)\.