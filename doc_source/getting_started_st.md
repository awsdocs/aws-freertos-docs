# Getting Started with the STMicroelectronics STM32L4 Discovery Kit IoT Node<a name="getting_started_st"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not already have the STMicroelectronics STM32L4 Discovery Kit IoT Node, you can purchase one from [STMicroelectronics](http://www.st.com/en/evaluation-tools/b-l475e-iot01a.html)\.

## Setting Up Your Environment<a name="st-setup-env"></a>

**Install System Workbench for STM32**

1. Browse to [OpenSTM32\.org](http://www.openstm32.org/HomePage)\.

1. Register on the OpenSTM32 webpage\. You need to sign in to download System Workbench\.

1. Browse to the [System Workbench for STM32 installer](http://www.openstm32.org/System%2BWorkbench%2Bfor%2BSTM32) to download and install System Workbench\.

If you experience issues during installation, see the FAQs on the [System Workbench website](http://www.openstm32.org/HomePage)\.

## Download and Build Amazon FreeRTOS<a name="st-download-and-build"></a>

After your environment is set up, you can download Amazon FreeRTOS and run the sample code\.

### Download Amazon FreeRTOS<a name="st-download"></a><a name="st-download-free-rtos"></a>

1. In the AWS IoT console, browse to the [Amazon FreeRTOS page](https://console.aws.amazon.com/freertos)\.

1. In the left navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose **Download FreeRTOS Software**\.

1. Under **Software Configurations**, find **Connect to AWS IoT\- ST**, and then choose **Download**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/download-treadstone-st.png)

1. Unzip the downloaded file to a folder, and make a note of the folder path\. In this tutorial, this folder is referred to as `BASE_FOLDER`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.

### Import the Amazon FreeRTOS Sample Code into the STM32 System Workbench<a name="st-freertos-import-project"></a><a name="st-import-project"></a>

1. Open the STM32 System Workbench and type a name for a new workspace\.

1. From the **File** menu, choose **Import**\. Expand **General**, choose **Existing Projects into Workspace**, and then choose **Next**\.

1. In the **Select Root Directory** text box, type `<BASE_FOLDER>\AmazonFreeRTOS\demos\st\stm32l475_discovery\ac6`\.

1. The project `aws_demos` should be found and selected by default\.

1. Choose **Finish** to import the project into STM32 System Workbench\.

1. From the **Project** menu, choose **Build All** and make sure it compiles without any errors or warnings\.

### Configure Your Project<a name="freertos-config-project"></a>

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

In the **Project Explorer** window, open `aws_demos\application_code\common_demos\include\aws_clientcredential.h`\.

Specify values for the following `#define` constants:
+ `clientcredentialMQTT_BROKER_ENDPOINT`: Your AWS IoT endpoint\.
+ `clientcredentialIOT_THING_NAME`: The AWS IoT thing for your board\.
+ `clientcredentialWIFI_SSID`: The SSID for your Wi\-Fi network\.
+ `clientcredentialWIFI_PASSWORD`: The password for your Wi\-Fi network\.
+ `clientcredentialWIFI_SECURITY`: The security type for your Wi\-Fi network\.

Make sure to save your changes\.

#### Configure Your AWS IoT Credentials<a name="ti-configure-credentials"></a>

The certificate and private key must be hard\-coded into the Amazon FreeRTOS demo code\. This is for demo purposes only\. Production level applications should store these files in a secure location\. Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\.

**To format your certificate and private key**

1. In a browser window, open `<BASE_FOLDER>\demos\common\devmode_key_provisioning\CertificateConfigurationTool\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the stub file in the directory\.

## Run the FreeRTOS Samples<a name="st-run-example"></a>

1. Use a USB cable to connect your STMicroelectronics STM32L4 Discovery Kit IoT Node to your computer\. 

1. Rebuild your project\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the left navigation pane, choose **Test** to open the MQTT client\.

1. In the **Subscription topic** text box, type `freertos/demos/echo`, and then choose **Subscribe to topic**\.

1. From the **Project Explorer**, right\-click `aws_demos`, choose **Debug As**, and then choose **Ac6 STM32 C/C\+\+ Application**\.

   If a debug error occurs the first time a debug session is launched, follow these steps:

   1. In STM32 System Workbench, from the **Run** menu, choose **Debug Configurations**\.

   1. In list box on the left, choose **aws\_demos Debug**\. \(You might need to expand **Ac6 STM32 Debugging**\.\)

   1. Choose the **Debugger** tab\.

   1. In **Configuration Script**, choose **Show Generator Options**\.

   1. In **Mode Setup**, set **Reset Mode** to **Software System Reset**\. Choose **Apply**, and then choose **Debug**\. 

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

In the MQTT client in AWS IoT, you should see the MQTT messages sent by your device\.