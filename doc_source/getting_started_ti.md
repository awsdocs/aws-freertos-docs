# Getting Started with the Texas Instruments CC3220SF\-LAUNCHXL<a name="getting_started_ti"></a>

Before you begin, see [First Steps](freertos-prereqs.md)\.

If you do not have the Texas Instruments \(TI\) CC3220SF\-LAUNCHXL Development Kit, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtaUAH/SimpleLink-Wi-Fi®-CC3220SF-Wireless-Microcontroller-LaunchPad-Development-Kit)\.

## Setting Up Your Environment<a name="ti-setup-env"></a>

Follow the steps below to set up your development environment to get started with Amazon FreeRTOS\.

Note that Amazon FreeRTOS supports two IDEs for the TI CC3220SF\-LAUNCHXL Development Kit: Code Composer Studio and IAR Embedded Workbench\. You can use either IDE to get started\.

### Install Code Composer Studio<a name="install-ccs"></a>

1. Browse to [TI Code Composer Studio](http://processors.wiki.ti.com/index.php/Download_CCS)\.

1. Download the offline installer for version 7\.3\.0 for the platform of your host machine \(Windows, macOS, or Linux 64\-bit\)\.
**Note**  
Currently, Amazon FreeRTOS demo code is only compatible with version 7\.3\.0 of TI Code Composer Studio\. Newer versions of Code Composer Studio might cause build errors\.

1. Unzip and run the offline installer\. Follow the prompts\.

1. For **Product Families to Install**, choose **SimpleLink Wi\-Fi CC32xx Wireless MCUs**\.

1. On the next page, accept the default settings for debugging probes, and then choose **Finish**\.

If you experience issues when you are installing Code Composer Studio, see [TI Development Tools Support](http://software-dl.ti.com/ccs/esd/documents/ccs_support.html), [Code Composer Studio FAQs](http://processors.wiki.ti.com/index.php/FAQ_-_CCS), and [Troubleshooting CCS](http://processors.wiki.ti.com/index.php/Troubleshooting_CCSv7)\.

### Install IAR Embedded Workbench<a name="install-iar"></a>

1. Browse to [IAR Embedded Workbench for ARM](https://www.iar.com/iar-embedded-workbench/#!?architecture=Arm)\.

1. Download and run the Windows installer\. In **Debug probe drivers**, make sure that **TI XDS** is selected:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/choose-usb.png)

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

1. Choose **Browse**, and then navigate to where you installed the CC3220SF SimpleLink SDK\. The service pack is located at `ti\simplelink_cc32xx_sdk_VERSION\tools\cc32xx_tools\servicepack-cc3x20\sp_VERSION.bin`\.

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

1. In **Select search\-directory**, enter `<BASE_FOLDER>\demos\ti\cc3220_launchpad\ccs`\. The project `aws_demos` should be selected by default\. To import the project into TI Code Composer, choose **Finish**\.

1. In **Project Explorer**, double\-click **aws\_demos** to make the project active\.

1. From **Project**, choose **Build Project** to make sure the project compiles without errors or warnings\.

**To subscribe to an MQTT topic**
**Note**  
Before you run the Amazon FreeRTOS samples, do the following:

1. Make sure the Sense On Power \(SOP\) jumper on your Texas Instruments CC3220SF\-LAUNCHXL is in position 0\. For more information, see [CC3220 SimpleLink User's Guide](http://www.ti.com/lit/ug/swru463b/swru463b.pdf)\.

1. Use a USB cable to connect your Texas Instruments CC3220SF\-LAUNCHXL to your computer\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.<a name="ti-run-project-css"></a>

**To run the Amazon FreeRTOS demo in TI Code Composer**

1. In the project explorer, make sure the `CC3220SF.ccxml` is selected as the active target configuration\. To make it active, right\-click the file and choose **Set as active target configuration**\.

1. In TI Code Composer,from **Run**, choose **Debug**\.

1. When the debugger stops at the breakpoint in `main()`, go to the **Run** menu, and choose **Resume**\.
**Note**  
The Amazon FreeRTOS demo has 8 debug messages configured\.

In the MQTT client in the AWS IoT console, you should see the MQTT messages sent by your device\.

### Build and Run Amazon FreeRTOS Demo Project in IAR Embedded Workbench<a name="ti-build-and-run-iar"></a><a name="ti-load-project-iar"></a>

**To import the Amazon FreeRTOS demo into IAR Embedded Workbench**

1. Open IAR Embedded Workbench, choose **File**, and then choose **Open Workspace**\.

1. Navigate to `<BASE_FOLDER>\demos\ti\cc3220_launchpad\iar`, choose **aws\_demos\.eww**, and then choose **OK**\.

1. Right\-click the project name \(`aws_demos`\), and then choose **Make**\.

**To subscribe to an MQTT topic**

1. Make sure the Sense On Power \(SOP\) jumper on your Texas Instruments CC3220SF\-LAUNCHXL is in position 0\. For more information, see [CC3220 SimpleLink User's Guide](http://www.ti.com/lit/ug/swru463b/swru463b.pdf)\.

1. Use a USB cable to connect your Texas Instruments CC3220SF\-LAUNCHXL to your computer\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

**To run the Amazon FreeRTOS demo in IAR Embedded Workbench**

1. Rebuild your project\.

   To rebuild the project, from the **Project** menu, choose **Make**\.

1. From the **Project** menu, choose **Download and Debug**\. You can ignore "Warning: Failed to initialize EnergyTrace," if it's displayed\. For more information about EnergyTrace, see [MSP EnergyTrace Technology](http://www.ti.com/tool/energytrace?jktype=recommendedresults)\.

1. When the debugger stops at the breakpoint in `main()`, go to the **Debug** menu, and choose **Go**\.
**Note**  
The Amazon FreeRTOS demo has 8 debug messages configured\.

You should see MQTT messages sent by your device in the MQTT client of the AWS IoT console\.

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