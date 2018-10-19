# Getting Started with the Texas Instruments CC3220SF\-LAUNCHXL<a name="getting_started_ti"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not have the Texas Instruments \(TI\) CC3220SF\-LAUNCHXL Development Kit, you can purchase one from [Texas Instruments](http://www.ti.com/tool/CC3220SF-LAUNCHXL)\.

## Setting Up Your Environment<a name="ti-setup-env"></a>

Amazon FreeRTOS supports two IDEs for the TI CC3220SF\-LAUNCHXL development kit: Code Composer Studio and IAR Embedded Workbench\.

For information about installing Code Composer Studio, see [Install Code Composer Studio](#install-ccs)\.

For information about installing IAR Embedded Workbench, see [Install IAR Embedded Workbench](#install-iar)\.

You also need to [Install the SimpleLink CC3220 SDK](#install-ti-sdk), [Install Uniflash](#install-uniflash), [Configure Wi\-Fi Provisioning](#wifi-provision), and [Install the Latest Service Pack](#ti-servicepack)\.

### Install Code Composer Studio<a name="install-ccs"></a>

1. Browse to [TI Code Composer Studio](http://processors.wiki.ti.com/index.php/Download_CCS)\.

1. Download the offline installer for version 7\.3\.0 for the platform of your host machine \(Windows, macOS, or Linux 64\-bit\)\.

1. Unzip and run the offline installer\. Follow the prompts\.

1. For **Product Families to Install**, choose **SimpleLink Wi\-Fi CC32xx Wireless MCUs**\.

1. On the next page, accept the default settings for debugging probes, and then choose **Finish**\.

If you experience issues when you are installing Code Composer Studio, see [TI Development Tools Support](http://software-dl.ti.com/ccs/esd/documents/ccs_support.html), [Code Composer Studio FAQs](http://processors.wiki.ti.com/index.php/FAQ_-_CCS), and [Troubleshooting Code Composer Studio](http://processors.wiki.ti.com/index.php/Troubleshooting_CCS)\.

### Install IAR Embedded Workbench<a name="install-iar"></a>

1. Browse to [IAR Embedded Workbench for ARM](https://www.iar.com/iar-embedded-workbench/#!?architecture=Arm)\.

1. Download and run the Windows installer\. In **Debug probe drivers**, make sure that **TI XDS** is selected:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/choose-usb.png)

1. Complete the installation and launch the program\. On the **License Wizard** page, choose **Register with IAR Systems to get an evaluation license**, or use your own IAR license\.

### Install the SimpleLink CC3220 SDK<a name="install-ti-sdk"></a>

Install the [SimpleLink CC3200 SDK](http://www.ti.com/tool/CC3200SDK)\. The SimpleLink Wi\-Fi CC3200 SDK contains drivers for the CC3200 programmable MCU, more than 40 sample applications, and documentation required to use the samples\.

### Install Uniflash<a name="install-uniflash"></a>

Install [Uniflash](http://www.ti.com/tool/UNIFLASH)\. CCS Uniflash is a standalone tool used to program on\-chip flash memory on TI MCUs\. Uniflash has a GUI, command line, and scripting interface\.

### Configure Wi\-Fi Provisioning<a name="wifi-provision"></a>

To configure the Wi\-Fi settings for your board, do one of the following:
+ Complete the Amazon FreeRTOS demo application described in [Configure Your Project](#ti-freertos-config-project)\.
+ Use [SmartConfig](http://processors.wiki.ti.com/index.php/CC3200_SmartConfig_Provisioning) from Texas Instruments\.

### Install the Latest Service Pack<a name="ti-servicepack"></a>

1. On your TI CC3220SF\-LAUNCHXL, place the SOP jumper on the middle set of pins \(position = 1\) and reset the board\.

1. Start Uniflash, and from the list of configurations, choose **CC3200SF\-LAUNCHXL**\. Choose **Start Image Creator**\.

1. Choose **New Project**\.

1. On the **Start new project** page, enter a name for your project\. For **Device Type**, choose **CC3220SF**\. For **Device Mode**, choose **Develop**, and then choose **Create Project**\.

1. Disconnect your serial terminal \(if previously connected\) and on the right side of the Uniflash application window, choose **Connect**\.

1. From the left column, choose **Service Pack**\.

1. Choose **Browse**, and then navigate to where you installed the CC3220SF SimpleLink SDK\. The service pack is located at `ti\simplelink_cc32xx_sdk_VERSION\tools\cc32xx_tools\servicepack-cc3x20\sp_VERSION.bin`\.

1. Choose the ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/flame.png) button and then choose **Program Image \(Create & Program\)** to install the service pack\. Remember to switch the SOP jumper back to position 0 and reset the board\.

## Download and Configure Amazon FreeRTOS<a name="ti-download-and-configure"></a>

After your environment is set up, you can download Amazon FreeRTOS\.

### Download Amazon FreeRTOS<a name="ti-download"></a><a name="ti-download-free-rtos"></a>

1. Browse to the AWS IoT console\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Under **Software Configurations**, find **Connect to AWS IoT\- TI**, and then:

   If you are using Code Composer Studio, choose **Download**\.

   If you are using IAR Embedded Workbench, choose **Connect to AWS IoT\-TI**\. Under **Hardware platform**, choose **Edit**\. Under **Integrated Development Environment \(IDE\)**, choose **IAR Embedded Workbench**\. Make sure the compiler is set to IAR, and then choose **Create and Download**\.

1. Unzip the downloaded file to your hard drive\. When unzipped, you have a directory named `AmazonFreeRTOS`\. You can place this directory anywhere you want, but be aware of path length limitations on Windows\. In this tutorial, the path to the `AmazonFreeRTOS` directory is referred to as `BASE_FOLDER`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.

### Configure Your Project<a name="ti-freertos-config-project"></a>

To run the demo, you must configure your project to work with AWS IoT and a Wi\-Fi network\. 

**Configure your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

1. Copy your AWS IoT endpoint from the **Endpoint** text box\. It should look like `<1234567890123>.iot.<us-east-1>.amazonaws.com`\.

1. Open `aws_demos\application_code\common_demos\include\aws_clientcredential.h` in your IDE\.

1. Set **clientcredentialMQTT\_BROKER\_ENDPOINT** to your AWS IoT endpoint\.

**Configure your Wi\-Fi**

1. Open the same `aws_clientcredential.h` file\.

1. Specify values for the following `#define` constants:  
`clientcredentialMQTT_BROKER_ENDPOINT`  
*Your AWS IoT endpoint*  
`clientcredentialIOT_THING_NAME`  
*The AWS IoT thing for your board*  
`clientcredentialWIFI_SSID`  
*The SSID for your Wi\-Fi network*  
`clientcredentialWIFI_PASSWORD`  
*The password for your Wi\-Fi network*  
`clientcredentialWIFI_SECURITY`  
*The security type of your Wi\-Fi network\.*   
Valid security types are:  
   + `eWiFiSecurityOpen` \(Open, no security\)
   + `eWiFiSecurityWEP` \(WEP security\)
   + `eWiFiSecurityWPA` \(WPA security\)
   + `eWiFiSecurityWPA2` \(WPA2 security\)

**Configure your AWS IoT credentials**

Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\. You need to format the certificate and private key for your device\. 

1. In a browser window, open `tools\certificate_configuration\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` that you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` that you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `demos\common\include`\. This overwrites the existing file in the directory\.
**Note**  
The certificate and private key should be hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

## Build and Run Amazon FreeRTOS Samples<a name="ti-build-and-run-examples"></a>

### Build and Run Amazon FreeRTOS Samples in TI Code Composer<a name="ti-build-and-run-ccs"></a><a name="ti-load-project"></a>

**Import the Amazon FreeRTOS Sample Code into TI Code Composer**

1. Open TI Code Composer, and choose **OK** to accept the default workspace name\.

1. On the **Getting Started** page, choose **Import Project**\.

1. In **Select search\-directory**, enter `<BASE_FOLDER>\AmazonFreeRTOS\demos\ti\cc3220_launchpad\ccs`\. The project `aws_demos` should be selected by default\. To import the project into TI Code Composer, choose **Finish**\.

1. In **Project Explorer**, double\-click **aws\_demos** to make the project active\.

1. From **Project**, choose **Build Project** to make sure the project compiles without errors or warnings\.

**Subscribe to MQTT topic**
**Note**  
Before you run the Amazon FreeRTOS samples, do the following:

1. Make sure the Sense On Power \(SOP\) jumper on your Texas Instruments CC3220SF\-LAUNCHXL is in position 0\. For more information, see [CC3200 SimpleLink User's Guide](http://www.ti.com/lit/ug/swru372b/swru372b.pdf)\.

1. Use a USB cable to connect your Texas Instruments CC3220SF\-LAUNCHXL to your computer\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.<a name="ti-run-project-css"></a>

**Run the Amazon FreeRTOS samples in TI Code Composer**

1. Rebuild your project\.

1. In TI Code Composer,from **Run**, choose **Debug**\.

1. When the debugger stops at the breakpoint in `main()`, go to the **Run** menu, and choose **Resume**\.

In the MQTT client in the AWS IoT console, you should see the MQTT messages sent by your device\.

### Build and Run Amazon FreeRTOS Samples in IAR Embedded Workbench<a name="ti-build-and-run-iar"></a><a name="ti-load-project-iar"></a>

**Import the Amazon FreeRTOS Sample Code into IAR Embedded Workbench**

1. Open IAR Embedded Workbench, choose **File**, and then choose **Open Workspace**\.

1. Navigate to `<BASE_FOLDER>\AmazonFreeRTOS\demos\ti\cc3220_launchpad\iar`, choose **aws\_demos\.eww**, and then choose **OK**\.

1. Right\-click the project name \(`aws_demos`\), and then choose **Make**\.

**Subscribe to MQTT topic**

1. Make sure the Sense On Power \(SOP\) jumper on your Texas Instruments CC3220SF\-LAUNCHXL is in position 0\. For more information, see [CC3200 SimpleLink User's Guide](http://www.ti.com/lit/ug/swru372b/swru372b.pdf)\.

1. Use a USB cable to connect your Texas Instruments CC3220SF\-LAUNCHXL to your computer\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

**Run the Amazon FreeRTOS samples in IAR Embedded Workbench**

1. Rebuild your project\.

   To rebuild the project, from the **Project** menu, choose **Make**\.

1. From the **Project** menu, choose **Download and Debug**\. You can ignore "Warning: Failed to initialize EnergyTrace," if it's displayed\. For more information about EnergyTrace, see [MSP EnergyTrace Technology](http://www.ti.com/tool/energytrace?jktype=recommendedresults)\.

1. When the debugger stops at the breakpoint in `main()`, go to the **Debug** menu, and choose **Go**\.

You should see MQTT messages sent by your device in the MQTT client of the AWS IoT console\.

## Troubleshooting<a name="getting_started_ti_troubleshooting"></a>

If you don’t see messages in the MQTT client of the AWS IoT console, you might need to configure debug settings for the board\. 

1. In Code Composer, on **Project Explorer**, choose **aws\_demos**\.

1. From the **Run** menu, choose **Debug Configurations**\.

1. In the navigation pane, choose **aws\_demos**\.

1. On the **Target** tab, under **Connection Options**, choose **Reset the target on a connect**\.

1. Choose **Apply**, and then choose **Close**\.

If these steps don’t work, look at the program's output in the serial terminal\. You should see some text that indicates the source of the problem\.