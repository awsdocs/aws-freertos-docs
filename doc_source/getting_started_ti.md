# Getting Started with the Texas Instruments CC3220SF\-LAUNCHXL<a name="getting_started_ti"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not have the Texas Instruments \(TI\) CC3220SF\-LAUNCHXL Development Kit, you can purchase one from [Texas Instruments](http://www.ti.com/tool/CC3220SF-LAUNCHXL)\.

## Setting Up Your Environment<a name="ti-setup-env"></a>

Amazon FreeRTOS supports two IDEs for the TI CC3220SF\-LAUNCHXL development kit: Code Composer Studio and IAR Embedded Workbench\. Before you begin, install one of the two IDEs:

**Option 1: Install Texas Instruments Code Composer Studio**

1. Browse to [TI Code Composer Studio](http://processors.wiki.ti.com/index.php/Download_CCS)\.

1. Download the offline installer for version 7\.3\.0 for the platform of your host machine \(Windows, macOS, or Linux 64\-bit\)\.

1. Unzip and run the offline installer\. Follow the prompts\.

1. For **Product Families to Install**, choose **SimpleLink Wi\-Fi CC32xx Wireless MCUs**\.

1. On the next page, accept the default settings for debugging probes, and then choose **Finish**\.

If you experience issues when you are installing Code Composer Studio, see [TI Development Tools Support](http://software-dl.ti.com/ccs/esd/documents/ccs_support.html), [Code Composer Studio FAQs](http://processors.wiki.ti.com/index.php/FAQ_-_CCS), and [Troubleshooting Code Composer Studio](http://processors.wiki.ti.com/index.php/Troubleshooting_CCS)\.

**Option 2: Install IAR Embedded Workbench for ARM**

1. Browse to [IAR Embedded Workbench for ARM](https://www.iar.com/iar-embedded-workbench/#!?architecture=Arm)\.

1. Download and run the Windows installer\. Make sure that **TI XDS** is selected as one of the USB Debug probe drivers:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/choose-usb.png)

1. Complete the installation and launch the program\. In the **License Wizard** panel, choose **Register with IAR Systems to get an evaluation license**, or use your own IAR license\.

## Download and Build Amazon FreeRTOS<a name="ti-download-and-build"></a>

After your environment is set up, you can download Amazon FreeRTOS and run the sample code\.

### Download Amazon FreeRTOS<a name="ti-download"></a><a name="ti-download-free-rtos"></a>

1. Browse to the AWS IoT console\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Under **Software Configurations**, find **Connect to AWS IoT\- TI**, and then:

   Option 1 \(if you are using CCS\): choose **Download**\.

   Option 2 \(if you are using IAR\): choose **Connect to AWS IoT\-TI**\. Under **Hardware platform**, choose **Edit**\. Under **Integrated Development Environment \(IDE\)**, choose **IAR Embedded Workbench** from the drop\-down list\. Make sure the compiler is set to IAR\. Then choose **Create and Download**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/download-treadstone-ti.png)

1. Unzip the downloaded file to a folder, and make a note of the folder path\. In this tutorial, this folder is referred to as `BASE_FOLDER`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.

### Import the Amazon FreeRTOS Sample Code into TI Code Composer<a name="ti-freertos-import-project"></a>

\(Follow these steps if you are using TI Code Composer\.\)<a name="ti-load-project"></a>

1. Open TI Code Composer and choose **OK** to accept the default workspace name\.

1. On the **Getting Started** page, choose **Import Project**\.

1. In the **Select search\-directory** text box, type `<BASE_FOLDER>\AmazonFreeRTOS\demos\ti\cc3220_launchpad\ccs`\. The project `aws_demos` should be selected by default\. To import the project into TI Code Composer, choose **Finish**\.

1. From the **Project** menu, choose **Build Project** to make sure it compiles without errors or warnings\.

### Import the Amazon FreeRTOS Sample Code into IAR Embedded Workbench<a name="ti-freertos-import-project-iar"></a>

\(Follow these steps if you are using IAR Embedded Workbench\.\)<a name="ti-load-project-iar"></a>

1. Open IAR Embedded Workbench and choose **File > Open Workspace**\.

1. Navigate to `<BASE_FOLDER>\AmazonFreeRTOS\demos\ti\cc3220_launchpad\iar` and choose **aws\_demos\.eww** then choose **OK**\.

1. Right\-click on the project name \(`aws_demos`\) then choose **Make**\.

### Configure Your Project<a name="get-started-freertos-config-project"></a>

Open `<BASE_FOLDER>\demos\common\include\aws_clientcredential.h` in a text editor\.

To configure your project you need to know your AWS IoT endpoint\.

**To find your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the left navigation pane, choose **Settings**\.

1. Copy your AWS IoT endpoint from the **Endpoint** text box\. It should look like `<1234567890123>.iot.<us-east-1>.amazonaws.com`\.

1. Open `aws_demos/application_code/common_demos/include/aws_clientcredential.h` and set `clientcredentialMQTT_BROKER_ENDPOINT` to your AWS IoT endpoint\. Save your changes\.

You also need to know your Wi\-FI network SSID, password, and security type, and the name of the AWS IoT thing that represents your device\. Valid security types are:
+ `eWiFiSecurityOpen`: Open, no security\.
+ `eWiFiSecurityWEP`: WEP security\.
+ `eWiFiSecurityWPA`: WPA security\.
+ `eWiFiSecurityWPA2`: WPA2 security\.

In the **Project Explorer** window, open `aws_demos\application_code\common_demos\include\aws_clientcredential.h`\.

Specify values for the following `#define` constants:
+ `clientcredentialMQTT_BROKER_ENDPOINT`: Your AWS IoT endpoint\.
+ `clientcredentialIOT_THING_NAME`: The AWS IoT thing for your board\.
+ `clientcredentialWIFI_SSID`: The SSID for your Wi\-Fi network\.
+ `clientcredentialWIFI_PASSWORD`: The password for your Wi\-Fi network\.
+ `clientcredentialWIFI_SECURITY`: The security type for your Wi\-Fi network\.

Make sure to save your changes\.

#### Configure Your AWS IoT Credentials<a name="get-started-ti-configure-credentials"></a>

The certificate and private key must be hard\-coded into the Amazon FreeRTOS demo code\. This is for demo purposes only\. Production level applications should store these files in a secure location\. Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\.

**To format your certificate and private key**

1. In a browser window, open `<BASE_FOLDER>\demos\common\devmode_key_provisioning\CertificateConfigurationTool\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the stub file in the directory\.

## Run the FreeRTOS Samples<a name="ti-run-example"></a>

1. Make sure the SOP jumper on your Texas Instruments CC3220SF\-LAUNCHXL is in position 0\.

1. Use a USB cable to connect your Texas Instruments CC3220SF\-LAUNCHXL to your computer\.

1. Rebuild your project\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the left navigation pane, choose **Test** to open the MQTT client\.

1. In the **Subscription topic** text box, type **freertos/demos/echo**, and then choose **Subscribe to topic**\.

1. From the **Run** menu in TI Code Composer, choose **Debug** to start debugging\.

1. When the debugger stops at the breakpoint in `main()`, go to the **Run** menu, and then choose **Resume**\.

In the MQTT client in the AWS IoT console, you should see the MQTT messages sent by your device\.

## Troubleshooting<a name="getting_started_ti_troubleshooting"></a>

If you don’t see messages received in the MQTT client of the AWS IoT console, you might need to configure debug settings for the board\. 

1. In Code Composer, on **Project Explorer**, choose **aws\_demos**\.

1. From the **Run** menu, choose **Debug Configurations**\.

1. In the left navigation pane, choose **aws\_demos**\.

1. Choose the **Target** tab in the main window\.

1. Scroll down to the **Connection Options** section and select the **Reset the target on a connect** check box\.

1. Choose **Apply**, and then choose **Close** to close the **Debug Configurations** dialog box\.

If your device hangs after calling `sl_Start()`, you might need to update the service pack running on the network processor on the TI chip\.

**To update the service pack**

1. On your TI CC3220SF\-LAUNCHXL, place the SOP jumper on the middle set of pins \(position = 1\) and reset the board\.

1. Start Uniflash and from the list of configurations, choose **CC3200SF\-LAUNCHXL**, and then choose **Start Image Creator**\.

1. Choose **New Project**\.

1. On the **Start new project** page, type a name for your project\. Set **Device Type** to **CC3220SF**\. Set **Device Mode** to **Develop**, and then choose **Create Project**\.

1. Disconnect your serial terminal \(if previously connected\) and on the right side of the Uniflash application window, choose **Connect**\.

1. From the left column, choose **Service Pack**\.

1. Choose **Browse**, and then navigate to where you installed the CC3220SF SimpleLink SDK\. The service pack is located at `ti\simplelink_cc32xx_sdk_VERSION\tools\cc32xx_tools\servicepack-cc3x20\sp_VERSION.bin`\.

1. Choose the ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/flame.png) button and then choose **Program Image \(Create & Program\)** to install the service pack\. Remember to switch the SOP jumper back to position 0 and reset the board\.

If these steps don’t work, look at the program's output in the serial terminal\. You should see some text that indicates the source of the problem\.