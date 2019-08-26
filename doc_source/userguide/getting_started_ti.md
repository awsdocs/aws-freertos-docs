# Getting Started with the Texas Instruments CC3220SF\-LAUNCHXL<a name="getting_started_ti"></a>

This tutorial provides instructions for getting started with the Texas Instruments CC3220SF\-LAUNCHXL\. If you do not have the Texas Instruments \(TI\) CC3220SF\-LAUNCHXL Development Kit, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtaUAH/SimpleLink-Wi-Fi®-CC3220SF-Wireless-Microcontroller-LaunchPad-Development-Kit)\.

Before you begin, you must configure AWS IoT and your Amazon FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the Amazon FreeRTOS download directory is referred to as `<amazon-freertos>`\.

## Overview<a name="w3aab7c23c35b7"></a>

This tutorial contains instructions for the following getting started steps:

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling an Amazon FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

## Set Up Your Development Environment<a name="ti-setup-env"></a>

Follow the steps below to set up your development environment to get started with Amazon FreeRTOS\.

Note that Amazon FreeRTOS supports two IDEs for the TI CC3220SF\-LAUNCHXL Development Kit: Code Composer Studio and IAR Embedded Workbench\. You can use either IDE to get started\.

### Install Code Composer Studio<a name="install-ccs"></a>

1. Browse to [TI Code Composer Studio](http://processors.wiki.ti.com/index.php/Download_CCS)\.

1. Download the offline installer for the platform of your host machine \(Windows, macOS, or Linux 64\-bit\)\.

1. Unzip and run the offline installer\. Follow the prompts\.

1. For **Product Families to Install**, choose **SimpleLink Wi\-Fi CC32xx Wireless MCUs**\.

1. On the next page, accept the default settings for debugging probes, and then choose **Finish**\.

If you experience issues when you are installing Code Composer Studio, see [TI Development Tools Support](http://software-dl.ti.com/ccs/esd/documents/ccs_support.html), [Code Composer Studio FAQs](http://processors.wiki.ti.com/index.php/FAQ_-_CCS), and [Troubleshooting CCS](http://processors.wiki.ti.com/index.php/Troubleshooting_CCSv7)\.

### Install IAR Embedded Workbench<a name="install-iar"></a>

1. Browse to [IAR Embedded Workbench for ARM](https://www.iar.com/iar-embedded-workbench/#!?architecture=Arm)\.

1. Download and run the Windows installer\. In **Debug probe drivers**, make sure that **TI XDS** is selected\.

1. Complete the installation and launch the program\. On the **License Wizard** page, choose **Register with IAR Systems to get an evaluation license**, or use your own IAR license\.

### Install the SimpleLink CC3220 SDK<a name="install-ti-sdk"></a>

Install the [SimpleLink CC3220 SDK](http://www.ti.com/tool/SIMPLELINK-CC3220-SDK)\. The SimpleLink Wi\-Fi CC3220 SDK contains drivers for the CC3220SF programmable MCU, more than 40 sample applications, and documentation required to use the samples\.

### Install Uniflash<a name="install-uniflash"></a>

Install [Uniflash](http://www.ti.com/tool/UNIFLASH)\. CCS Uniflash is a standalone tool used to program on\-chip flash memory on TI MCUs\. Uniflash has a GUI, command line, and scripting interface\.

### Install the Latest Service Pack<a name="ti-servicepack"></a>

1. On your TI CC3220SF\-LAUNCHXL, place the SOP jumper on the middle set of pins \(position = 1\) and reset the board\.

1. Start Uniflash\. If your CC3220SF LaunchPad board appears under **Detected Devices**, choose **Start**\. If your board is not detected, choose **CC3220SF\-LAUNCHXL** from the list of boards under **New Configuration**, and then choose **Start Image Creator**\.

1. Choose **New Project**\.

1. On the **Start new project** page, enter a name for your project\. For **Device Type**, choose **CC3220SF**\. For **Device Mode**, choose **Develop**, and then choose **Create Project**\.

1. On the right side of the Uniflash application window, choose **Connect**\.

1. From the left column, choose **Advanced**, **Files**, and then **Service Pack**\.

1. Choose **Browse**, and then navigate to where you installed the CC3220SF SimpleLink SDK\. The service pack is located at `ti/simplelink_cc32xx_sdk_VERSION/tools/cc32xx_tools/servicepack-cc3x20/sp_VERSION.bin`\.

1. Choose the **Burn** \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/flame.png)\) button, and then choose **Program Image \(Create & Program\)** to install the service pack\. Remember to switch the SOP jumper back to position 0 and reset the board\.

### Configure Wi\-Fi Provisioning<a name="wifi-provision"></a>

To configure the Wi\-Fi settings for your board, do one of the following:
+ Configure the Amazon FreeRTOS demo application described in [Configuring the Amazon FreeRTOS Demos](freertos-configure.md)\.
+ Use [SmartConfig](http://dev.ti.com/tirex/#/?link=Software%2FSimpleLink%20CC32xx%20SDK%2FSimpleLink%20Academy%2FWi-Fi%2FWi-Fi%20Provisioning) from Texas Instruments\.

## Build and Run the Amazon FreeRTOS Demo Project<a name="ti-build-and-run-examples"></a>

### Build and Run the Amazon FreeRTOS Demo Project in TI Code Composer<a name="ti-build-and-run-ccs"></a><a name="ti-load-project"></a>

**To import the Amazon FreeRTOS demo into TI Code Composer**

1. Open TI Code Composer, and choose **OK** to accept the default workspace name\.

1. On the **Getting Started** page, choose **Import Project**\.

1. In **Select search\-directory**, enter `projects/ti/cc3220_launchpad/ccs/aws_demos`\. The project `aws_demos` should be selected by default\. To import the project into TI Code Composer, choose **Finish**\.

1. In **Project Explorer**, double\-click **aws\_demos** to make the project active\.

1. From **Project**, choose **Build Project** to make sure the project compiles without errors or warnings\.<a name="ti-run-project-css"></a>

**To run the Amazon FreeRTOS demo in TI Code Composer**

1. Make sure the Sense On Power \(SOP\) jumper on your Texas Instruments CC3220SF\-LAUNCHXL is in position 0\. For more information, see [CC3220 SimpleLink User's Guide](http://www.ti.com/lit/ug/swru463b/swru463b.pdf)\.

1. Use a USB cable to connect your Texas Instruments CC3220SF\-LAUNCHXL to your computer\.

1. In the project explorer, make sure the `CC3220SF.ccxml` is selected as the active target configuration\. To make it active, right\-click the file and choose **Set as active target configuration**\.

1. In TI Code Composer, from **Run**, choose **Debug**\.

1. When the debugger stops at the breakpoint in `main()`, go to the **Run** menu, and choose **Resume**\.

### Build and Run Amazon FreeRTOS Demo Project in IAR Embedded Workbench<a name="ti-build-and-run-iar"></a><a name="ti-load-project-iar"></a>

**To import the Amazon FreeRTOS demo into IAR Embedded Workbench**

1. Open IAR Embedded Workbench, choose **File**, and then choose **Open Workspace**\.

1. Navigate to `projects/ti/cc3220_launchpad/iar/aws_demos`, choose **aws\_demos\.eww**, and then choose **OK**\.

1. Right\-click the project name \(`aws_demos`\), and then choose **Make**\.

**To run the Amazon FreeRTOS demo in IAR Embedded Workbench**

1. Make sure the Sense On Power \(SOP\) jumper on your Texas Instruments CC3220SF\-LAUNCHXL is in position 0\. For more information, see [CC3220 SimpleLink User's Guide](http://www.ti.com/lit/ug/swru463b/swru463b.pdf)\.

1. Use a USB cable to connect your Texas Instruments CC3220SF\-LAUNCHXL to your computer\.

1. Rebuild your project\.

   To rebuild the project, from the **Project** menu, choose **Make**\.

1. From the **Project** menu, choose **Download and Debug**\. You can ignore "Warning: Failed to initialize EnergyTrace," if it's displayed\. For more information about EnergyTrace, see [MSP EnergyTrace Technology](http://www.ti.com/tool/energytrace?jktype=recommendedresults)\.

1. When the debugger stops at the breakpoint in `main()`, go to the **Debug** menu, and choose **Go**\.

### Using CMake with Amazon FreeRTOS<a name="w3aab7c23c35c11b7"></a>

If you prefer not to use an IDE for Amazon FreeRTOS development, you can alternatively use CMake to build and run the demo applications or applications that you have developed using third\-party code editors and debugging tools\.

**To build the Amazon FreeRTOS demo with CMake**

1. Create a folder to contain the generated build files \(*BUILD\_FOLDER*\)\.

1. If you are using the TI ARM compiler with your TI board, use the following command to generate build files from source code:

   ```
   cmake -DVENDOR=ti -DBOARD=cc3220_launchpad -DCOMPILER=arm-ti -S <amazon-freertos> -B BUILD_FOLDER
   ```

   If you are using the IAR compiler, use the following command:

   ```
   cmake -DVENDOR=ti -DBOARD=cc3220_launchpad -DCOMPILER=arm-iar -S <amazon-freertos> -B BUILD_FOLDER
   ```

For more information, see [Using CMake with Amazon FreeRTOS](getting-started-cmake.md)\.

### Monitoring MQTT Messages on the Cloud<a name="w3aab7c23c35c11b9"></a>

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **iotdemo/\#**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="getting_started_ti_troubleshooting"></a>

If you don’t see messages in the MQTT client of the AWS IoT console, you might need to configure debug settings for the board\.

**To configure debug settings for TI boards**

1. In Code Composer, on **Project Explorer**, choose **aws\_demos**\.

1. From the **Run** menu, choose **Debug Configurations**\.

1. In the navigation pane, choose **aws\_demos**\.

1. On the **Target** tab, under **Connection Options**, choose **Reset the target on a connect**\.

1. Choose **Apply**, and then choose **Close**\.

If these steps don’t work, look at the program's output in the serial terminal\. You should see some text that indicates the source of the problem\.

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.