# Getting Started with the STMicroelectronics STM32L4 Discovery Kit IoT Node<a name="getting_started_st"></a>

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not already have the STMicroelectronics STM32L4 Discovery Kit IoT Node, you can purchase one from [STMicroelectronics](http://www.st.com/en/evaluation-tools/b-l475e-iot01a.html)\.

Make sure you have installed the latest Wi\-Fi firmware\. To download the latest Wi\-Fi firmware, see [STM32L4 Discovery kit IoT node, low\-power wireless, BLE, NFC, SubGHz, Wi\-Fi](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip)\. Under **Binary Resources**, choose **Inventek ISM 43362 Wi\-Fi module firmware update \(read the readme file for instructions\) **\.

## Setting Up Your Environment<a name="st-setup-env"></a>

### Install System Workbench for STM32<a name="install-system-workbench"></a>

1. Browse to [OpenSTM32\.org](http://www.openstm32.org/HomePage)\.

1. Register on the OpenSTM32 webpage\. You need to sign in to download System Workbench\.

1. Browse to the [System Workbench for STM32 installer](http://www.openstm32.org/System%2BWorkbench%2Bfor%2BSTM32) to download and install System Workbench\.

If you experience issues during installation, see the FAQs on the [System Workbench website](http://www.openstm32.org/HomePage)\.

## Download and Configure Amazon FreeRTOS<a name="st-download-and-configure"></a>

After your environment is set up, you can download Amazon FreeRTOS\.

### Download Amazon FreeRTOS<a name="st-download"></a><a name="st-download-free-rtos"></a>

1. In the AWS IoT console, browse to the [Amazon FreeRTOS page](https://console.aws.amazon.com/freertos)\.

1. In the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. Choose **Download FreeRTOS Software**\.

1. Under **Software Configurations**, find **Connect to AWS IoT\- ST**, and then choose **Download**\.

1. Unzip the downloaded file to a folder, and make a note of the folder path\. In this tutorial, this folder is referred to as `BASE_FOLDER`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.

### Configure Your Project<a name="st-freertos-config-project"></a>

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

## Build and Run Amazon FreeRTOS Samples<a name="st-build-and-run-example"></a>

### Import the Amazon FreeRTOS Sample Code into the STM32 System Workbench<a name="st-freertos-import-project"></a><a name="st-import-project"></a>

1. Open the STM32 System Workbench and enter a name for a new workspace\.

1. From the **File** menu, choose **Import**\. Expand **General**, choose **Existing Projects into Workspace**, and then choose **Next**\.

1. In **Select Root Directory**, enter `<BASE_FOLDER>\AmazonFreeRTOS\demos\st\stm32l475_discovery\ac6`\.

1. The project `aws_demos` should be selected by default\.

1. Choose **Finish** to import the project into STM32 System Workbench\.

1. From the **Project** menu, choose **Build All**\. Confirm the project compiles without any errors or warnings\.

### Run the Amazon FreeRTOS Samples<a name="st-run-example"></a>

1. Use a USB cable to connect your STMicroelectronics STM32L4 Discovery Kit IoT Node to your computer\. 

1. Rebuild your project\.

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter `freertos/demos/echo`, and then choose **Subscribe to topic**\.

1. From **Project Explorer**, right\-click `aws_demos`, choose **Debug As**, and then choose **Ac6 STM32 C/C\+\+ Application**\.

   If a debug error occurs the first time a debug session is launched, follow these steps:

   1. In STM32 System Workbench, from the **Run** menu, choose **Debug Configurations**\.

   1. Choose **aws\_demos Debug**\. \(You might need to expand **Ac6 STM32 Debugging**\.\)

   1. Choose the **Debugger** tab\.

   1. In **Configuration Script**, choose **Show Generator Options**\.

   1. In **Mode Setup**, set **Reset Mode** to **Software System Reset**\. Choose **Apply**, and then choose **Debug**\. 

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

You should see MQTT messages sent by your device in the MQTT client in the AWS IoT console\.

## Troubleshooting<a name="st-troubleshooting"></a>

If you see the following in the UART output from the demo application, you need to update the Wi\-Fi module’s firmware:

```
[Tmr Svc] WiFi firmware version is: xxxxxxxxxxxxx
[Tmr Svc] [WARN] WiFi firmware needs to be updated.
```

To download the latest Wi\-Fi firmware, see [STM32L4 Discovery kit IoT node, low\-power wireless, BLE, NFC, SubGHz, Wi\-Fi](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip)\. In **Binary Resources**, choose the download link for **Inventek ISM 43362 Wi\-Fi module firmware update**\.