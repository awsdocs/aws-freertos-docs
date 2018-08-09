# Getting Started with the NXP LPC54018 IoT Module<a name="getting_started_nxp"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not have an NXP LPC54018 IoT Module, you can order one from [NXP](https://www.nxp.com/products/processors-and-microcontrollers/arm-based-processors-and-mcus/lpc-cortex-m-mcus/lpc54000-series-cortex-m4-mcus/lpc54018-iot-module-for-the-lpc540xx-family-of-mcus:OM40007?fsrch=1&sr=1&pageNum=1)\. Use a USB cable to connect your NXP LPC54018 IoT Module to your computer\.

## Setting Up Your Environment<a name="setup-env_nxp"></a>

Amazon FreeRTOS supports two IDEs for the NXP LPC54018 IoT Module: IAR Embedded Workbench and MCUXpresso\. Before you begin, install one of the two IDEs:

**To install IAR Embedded Workbench for Arm**

1. Browse to [Software for NXP Kits](https://www.iar.com/iar-embedded-workbench/partners/nxp/downloads-for-nxp-kits) and choose **Download Software** to install IAR Embedded Workbench for Arm\.
**Note**  
IAR Embedded Workbench for ARM requires Microsoft Windows\.

1. Unzip and run the installer\. Follow the prompts\.

1. In the **License Wizard**, choose **Register with IAR Systems to get an evaluation license**\.

**To install MCUXpresso from NXP**

1. Download and run the MCUXpresso installer from [NXP](https://www.nxp.com/support/developer-resources/software-development-tools/mcuxpresso-software-and-tools/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)\.

1. Browse to [MCUXpresso SDK](https://www.nxp.com/support/developer-resources/software-development-tools/mcuxpresso-software-and-tools/mcuxpresso-software-development-kit-sdk:MCUXpresso-SDK) and choose **Build your SDK\.**

1. Choose **Select Development Board**\.

1. Under **Select Development Board**, type **LPC54018\-IoT\-Module** in the **Search by Name** text box\.

1. Under **Boards** choose **LPC54018\-IoT\-Module**\.

1. On the right\-hand side of the page, verify the hardware details and choose **Build MCUXepresso SDK**\.

1. The SDK for Windows using the MCUXpresso IDE is already built\. Choose **Download SDK**\. If you are using another operating system, under **Host OS**, choose your operating system and then choose **Download SDK**\. 

1. Start the MCUXpresso IDE\. In the bottom of the MCUXpresso IDE screen, choose the **Installed SDKs** tab\.

1. Drag and drop the downloaded sdk archive file into the **Installed SDKs** window\.

**Note**  
If you experience issues during installation, see [NXP Support](https://www.nxp.com/support/support:SUPPORTHOME?tid=sbmenu) or [NXP Developer Resources](https://www.nxp.com/support/developer-resources:DEVELOPER_HOME)\.

## Connecting a JTAG Debugger<a name="get-started-jtag-debugger"></a>

You need a JTAG debugger to launch and debug your code running on the NXP LPC54018 board\. Amazon FreeRTOS was tested using a Segger J\-Link probe\. For more information about supported debuggers, see the [NXP LPC54018 User Guide](https://www.nxp.com/docs/en/user-guide/UM11078.pdf)\.

**Note**  
If you are using a Segger J\-Link debugger, you need a converter cable to connect the 20\-pin connector from the debugger to the 10\-pin connector on the NXP IoT module\. 

## Download and Build Amazon FreeRTOS<a name="nxp-download-and-build"></a>

After your environment is set up, you can download Amazon FreeRTOS and run the sample code\.

### Download Amazon FreeRTOS<a name="nxp-download"></a><a name="nxp-download-free-rtos"></a>

1. Browse to the [Amazon FreeRTOS page](https://console.aws.amazon.com/freertos) in the AWS IoT console\.

1. In the left navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose **Download FreeRTOS Software**\.

1. If you are using IAR Workbench:

   1. In the **Software Configurations**, find **Connect to AWS IoT\- NXP** and choose **Download**\.

1. If you are using MCUXpresso:

   1. In the **Software Configurations**, find **Connect to AWS IoT\- NXP**\. Select the **Connect to AWS IoT\- NXP** text, do not choose **Download**\.

   1. Under **Hardware Platform** choose **Edit**\.

   1. Under **Integrated Development Environment \(IDE\)**, choose **MCUXpresso**

   1. Under **Compiler**, choose **GCC**\.

   1. At the bottom of the page, choose **Create and Download**\.

1. Unzip the downloaded file to a folder and make a note of the folder path\. In this tutorial, this folder is referred to as `BASE_FOLDER`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.

### Import the Amazon FreeRTOS Sample Code into Your IDE<a name="nxp-freertos-import-project"></a><a name="nxp-load-project"></a>

**To import the Amazon FreeRTOS sample code into the IAR Embedded Workbench IDE**

1. Open IAR Embedded Workbench, and from the **File** menu, choose **Open Workspace**\.

1. In the **search\-directory** text box, type `<BASE_FOLDER>\AmazonFreeRTOS\demos\nxp\lpc54018_iot_module\iar`, and choose **aws\_demos\.eww**\.

1. In the **Project** menu, choose **Rebuild All**\.

**To import the Amazon FreeRTOS sample code into the MCUXpresso IDE**

1. Open MCUXpresso and from the **File** menu, choose **Open Projects From File System\.\.\.**

1. In the **Directory** text box, type *<BASE\_FOLDER>*\\AmazonFreeRTOS\\demos\\nxp\\lpc54018\_iot\_module\\mcuxpresso and choose **Finish**

1. In the **Project** menu, choose **Build All**\.

### Configure Your Project<a name="nxp-config-project"></a>

To configure your project, you need to know your AWS IoT endpoint\.

**To find your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the left navigation pane, choose **Settings**\.

1. Copy your AWS IoT endpoint from the **Endpoint** text box\. It should look like `<1234567890123>.iot.<us-east-1>.amazonaws.com`\.

1. Open `aws_demos/application_code/common_demos/include/aws_clientcredential.h` and set `clientcredentialMQTT_BROKER_ENDPOINT` to your AWS IoT endpoint\. Save your changes\.

You also need to know your Wi\-FI network SSID, password, and security type and the name of the AWS IoT thing that represents your device\. Valid security types are:
+ `eWiFiSecurityOpen`: Open, no security\.
+ `eWiFiSecurityWEP`: WEP security\.
+ `eWiFiSecurityWPA`: WPA security\.
+ `eWiFiSecurityWPA2`: WPA2 security\.

In your IDE, open `aws_demos\application_code\common_demos\include\aws_clientcredential.h`\.

Specify values for the following `#define` constants:
+ `clientcredentialMQTT_BROKER_ENDPOINT`: Your AWS IoT endpoint\.
+ `clientcredentialIOT_THING_NAME`: The AWS IoT thing for your board\.
+ `clientcredentialWIFI_SSID`: The SSID for your Wi\-Fi network\.
+ `clientcredentialWIFI_PASSWORD`: The password for your Wi\-Fi network\.
+ `clientcredentialWIFI_SECURITY`: The security type for your Wi\-Fi network\.

Make sure to save your changes\.

#### Configure Your AWS IoT Credentials<a name="nxp-configure-credentials"></a>

The certificate and private key must be hard\-coded into the Amazon FreeRTOS demo code\. This is for demo purposes only\. Production level applications should store these files in a secure location\. Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\.

**To format your certificate and private key**

1. In a browser window, open `<BASE_FOLDER>\demos\common\devmode_key_provisioning\CertificateConfigurationTool\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the stub file in the directory\.

### Run the FreeRTOS Samples<a name="nxp-run-example"></a>

To run the Amazon FreeRTOS demos on the NXP LPC54018 IoT Module board, connect the USB port on the NXP IoT Module to your host computer, open a terminal program, and connect to the port identified as "USB Serial Device\."

1. Rebuild your project\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the left navigation pane, choose **Test** to open the MQTT client\.

1. In the **Subscription topic** text box, type `freertos/demos/echo`, and then choose **Subscribe to topic**\.

1. In your IDE, from the **Project** menu, choose **Build**\.

1. Connect both the NXP IoT Module and the Segger J\-Link Debugger to the USB ports on your computer using mini\-USB to USB cables\.

1. If you are using IAR Embedded Workbench:

   1. From the **Project** menu, choose **Download and Debug**\.

   1. From the **Debug** menu, choose **Start Debugging**\.

   1. When the debugger stops at the breakpoint in `main`, go to the **Debug** menu and choose **Go\.**
**Note**  
If a **J\-Link Device Selection** dialog box opens, choose **OK** to continue\. In the **Target Device Settings** dialog box, choose **Unspecified**, choose **Cortex\-M4**, and then choose **OK**\. You only need to be do this once\.

1. If you are using MCUXpresso:

   1. If this is your first time debugging, select the `aws_demos` project and from the **Debug** toolbar, choose the blue debug button \(Start debugging the project with the selected build configuration\)\.

   1. A window is displayed with any detected debug probes, choose the probe you want to use and click **OK** to start debugging\.
**Note**  
When the debugger stops at the breakpoint in `main()`, press the debug restart button ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/reset.png) once to reset the debugging session\. \(This is due to an ongoing bug with MCUXpresso debugger for NXP54018\-IoT\-Module\)\.

1. When the debugger stops at the breakpoint in `main()`, go to the **Debug** menu, and choose **Go**\.

In the MQTT client in the AWS IoT console, you should see the MQTT messages sent by your device\.

### Troubleshooting<a name="getting_started_nxp_troubleshooting"></a>

If no messages appear in the AWS IoT console, try the following:

1. Open a terminal window to view the logging output of the sample\. This can help you determine what is going wrong\.

1. Check that your network credentials are valid\.