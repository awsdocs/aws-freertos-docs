# Getting Started with the NXP LPC54018 IoT Module<a name="getting_started_nxp"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not have an NXP LPC54018 IoT Module, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtAUAX/LPC54018-IoT-Solution)\. Use a USB cable to connect your NXP LPC54018 IoT Module to your computer\.

## Setting Up Your Environment<a name="setup-env_nxp"></a>

Amazon FreeRTOS supports two IDEs for the NXP LPC54018 IoT Module: IAR Embedded Workbench and MCUXpresso\.

Before you begin, install one of these IDEs\.

**To install IAR Embedded Workbench for ARM**

1. Browse to [Software for NXP Kits](https://www.iar.com/iar-embedded-workbench/partners/nxp/downloads-for-nxp-kits) and choose **Download Software**\.
**Note**  
IAR Embedded Workbench for ARM requires Microsoft Windows\.

1. Unzip and run the installer\. Follow the prompts\.

1. In the **License Wizard**, choose **Register with IAR Systems to get an evaluation license**\.

**To install MCUXpresso from NXP**

1. Download and run the MCUXpresso installer from [NXP](https://www.nxp.com/support/developer-resources/software-development-tools/mcuxpresso-software-and-tools/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)\.

1. Browse to [MCUXpresso SDK](https://www.nxp.com/support/developer-resources/software-development-tools/mcuxpresso-software-and-tools/mcuxpresso-software-development-kit-sdk:MCUXpresso-SDK) and choose **Build your SDK\.**

1. Choose **Select Development Board**\.

1. Under **Select Development Board**, in **Search by Name**, enter **LPC54018\-IoT\-Module**\.

1. Under **Boards**, choose **LPC54018\-IoT\-Module**\.

1. Verify the hardware details, and then choose **Build MCUXepresso SDK**\.

1. The SDK for Windows using the MCUXpresso IDE is already built\. Choose **Download SDK**\. If you are using another operating system, under **Host OS**, choose your operating system, and then choose **Download SDK**\. 

1. Start the MCUXpresso IDE, and choose the **Installed SDKs** tab\.

1. Drag and drop the downloaded SDK archive file into the **Installed SDKs** window\.

If you experience issues during installation, see [NXP Support](https://www.nxp.com/support/support:SUPPORTHOME?tid=sbmenu) or [NXP Developer Resources](https://www.nxp.com/support/developer-resources:DEVELOPER_HOME)\.

### Connecting a JTAG Debugger<a name="get-started-jtag-debugger"></a>

You need a JTAG debugger to launch and debug your code running on the NXP LPC54018 board\. Amazon FreeRTOS was tested using a Segger J\-Link probe\. For more information about supported debuggers, see the [NXP LPC54018 User Guide](https://www.nxp.com/docs/en/user-guide/UM11078.pdf)\.

**Note**  
If you are using a Segger J\-Link debugger, you need a converter cable to connect the 20\-pin connector from the debugger to the 10\-pin connector on the NXP IoT module\. 

## Download and Configure Amazon FreeRTOS<a name="nxp-download-and-configure"></a>

After your environment is set up, you can download Amazon FreeRTOS\.

### Download Amazon FreeRTOS<a name="nxp-download"></a><a name="nxp-download-free-rtos"></a>

1. Browse to the [Amazon FreeRTOS page](https://console.aws.amazon.com/freertos) in the AWS IoT console\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose **Download FreeRTOS Software**\.

1. Under **Software Configurations**, find **Connect to AWS IoT\- NXP**, and then:

   If you are using IAR Workbench, choose **Download**\.

   If you are using MCUXpresso:

   1. In **Software Configurations**, find **Connect to AWS IoT\- NXP**\. Select **Connect to AWS IoT\- NXP**, but do not choose **Download**\.

   1. Under **Hardware Platform**, choose **Edit**\.

   1. Under **Integrated Development Environment \(IDE\)**, choose **MCUXpresso**\.

   1. Under **Compiler**, choose **GCC**\.

   1. At the bottom of the page, choose **Create and Download**\.

1. Unzip the downloaded file to the AmazonFreeRTOS folder and make a note of the folder's path\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.  
In this tutorial, the path to the `AmazonFreeRTOS` directory is referred to as `BASE_FOLDER`\.

### Configure Your Project<a name="nxp-freertos-config-project"></a>

To run the demo, you must configure your project to work with AWS IoT, which requires that you register your board as an AWS IoT thing\. [Registering Your MCU Board with AWS IoT](freertos-prereqs.md#get-started-freertos-thing) is a step in the [Prerequisites](freertos-prereqs.md)\.

**To configure your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint is displayed in **Endpoint**\. It should look like `<1234567890123>-ats.iot.<us-east-1>.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\.

   Your device should have an AWS IoT thing name\. Make a note of this name\.

1. In your IDE, open `<BASE_FOLDER>\demos\common\include\aws_clientcredential.h` and specify values for the following `#define` constants:
   + `clientcredentialMQTT_BROKER_ENDPOINT` *Your AWS IoT endpoint*
   + `clientcredentialIOT_THING_NAME` *The AWS IoT thing name of your board*

**To configure your Wi\-Fi settings**

1. Open the `aws_clientcredential.h` file\.

1. Specify values for the following `#define` constants:
   + `clientcredentialWIFI_SSID` *The SSID for your Wi\-Fi network*
   + `clientcredentialWIFI_PASSWORD` *The password for your Wi\-Fi network*
   + `clientcredentialWIFI_SECURITY` *The security type of your Wi\-Fi network*

     Valid security types are:
     + `eWiFiSecurityOpen` \(Open, no security\)
     + `eWiFiSecurityWEP` \(WEP security\)
     + `eWiFiSecurityWPA` \(WPA security\)
     + `eWiFiSecurityWPA2` \(WPA2 security\)

**To configure your AWS IoT credentials**
**Note**  
To configure your AWS IoT credentials, you need the private key and certificate that you downloaded from the AWS IoT console when you registered your device\. After you have registered your device as an AWS IoT thing, you can retrieve device certificates from the AWS IoT console, but you cannot retrieve private keys\.

Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\. You must format the certificate and private key for your device\.

1. In a browser window, open `<BASE_FOLDER>\tools\certificate_configuration\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` that you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` that you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the existing file in the directory\.
**Note**  
The certificate and private key are hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

## Build and Run the Amazon FreeRTOS Demo Project<a name="nxp-build-and-run"></a>

### Import the Amazon FreeRTOS Demo into Your IDE<a name="nxp-freertos-import-project"></a><a name="nxp-load-project"></a>

**To import the Amazon FreeRTOS sample code into the IAR Embedded Workbench IDE**

1. Open IAR Embedded Workbench, and from the **File** menu, choose **Open Workspace**\.

1. In the **search\-directory** text box, enter `<BASE_FOLDER>\demos\nxp\lpc54018_iot_module\iar`, and choose **aws\_demos\.eww**\.

1. From the **Project** menu, choose **Rebuild All**\.

**To import the Amazon FreeRTOS sample code into the MCUXpresso IDE**

1. Open MCUXpresso, and from the **File** menu, choose **Open Projects From File System**\.

1. In the **Directory** text box, enter `<BASE_FOLDER>\demos\nxp\lpc54018_iot_module\mcuxpresso`, and choose **Finish**

1. From the **Project** menu, choose **Build All**\.

### Run the Amazon FreeRTOS Demo Project<a name="nxp-run-example"></a>

To run the Amazon FreeRTOS demos on the NXP LPC54018 IoT Module board, connect the USB port on the NXP IoT Module to your host computer, open a terminal program, and connect to the port identified as USB Serial Device\.

1. Rebuild your project\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

1. In your IDE, from the **Project** menu, choose **Build**\.

1. Connect the NXP IoT Module and the Segger J\-Link Debugger to the USB ports on your computer using mini\-USB to USB cables\.

1. If you are using IAR Embedded Workbench:

   1. From the **Project** menu, choose **Download and Debug**\.

   1. From the **Debug** menu, choose **Start Debugging**\.

   1. When the debugger stops at the breakpoint in `main`, from the **Debug** menu, choose **Go\.**
**Note**  
If a **J\-Link Device Selection** dialog box opens, choose **OK** to continue\. In the **Target Device Settings** dialog box, choose **Unspecified**, choose **Cortex\-M4**, and then choose **OK**\. You only need to be do this once\.

1. If you are using MCUXpresso:

   1. If this is your first time debugging, choose the `aws_demos` project and from the **Debug** toolbar, choose the blue debug button\.

   1. Any detected debug probes are displayed\. Choose the probe you want to use, and then choose **OK** to start debugging\.
**Note**  
When the debugger stops at the breakpoint in `main()`, press the debug restart button ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/reset.png) once to reset the debugging session\. \(This is required due to a bug with MCUXpresso debugger for NXP54018\-IoT\-Module\)\.

1. When the debugger stops at the breakpoint in `main()`, from the **Debug** menu, choose **Go**\.

You should see MQTT messages sent by your device in the MQTT client of the AWS IoT console\.

## Troubleshooting<a name="getting_started_nxp_troubleshooting"></a>

If no messages appear in the AWS IoT console, try the following:

1. Open a terminal window to view the logging output of the sample\. This can help you determine what is going wrong\.

1. Check that your network credentials are valid\.