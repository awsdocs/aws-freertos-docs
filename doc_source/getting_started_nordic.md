# Getting Started with the Nordic nRF52840\-DK<a name="getting_started_nordic"></a>


|  | 
| --- |
| Amazon FreeRTOS support for the Nordic nRF52840\-DK is in public beta release\. BLE demos are subject to change\. | 

Before you begin, see [Prerequisites](freertos-prereqs.md)\.

If you do not have the Nordic nRF52840\-DK, you can purchase one from [Nordic](https://www.nordicsemi.com/eng/Products/nRF52840-DK)\.

## Setting Up the Nordic Hardware<a name="nordic-setup-env"></a>

Connect your host computer to the USB port labaled J2, located directly above the coin cell battery holder on your Nordic nRF52840 board\.

For more information about setting up the Nordic nRF52840\-DK, see the [nRF52840 Development Kit User Guide](http://infocenter.nordicsemi.com/pdf/nRF52840_DK_User_Guide_v1.2.pdf)\.

## Setting Up Your Environment<a name="nordic-setup-env"></a>

### Download and Install Segger Embedded Studio<a name="install-embedded-studio"></a>

Amazon FreeRTOS supports Segger Embedded Studio as a development environment for the Nordic nRF52840\-DK\.

To set up your environment, you need to download and install Segger Embedded Studio\.

1. Go to the [Segger Embedded Studio Downloads](https://www.segger.com/downloads/embedded-studio/) page and choose the Embedded Studio for ARM option for your operating system\.

1. Run the installer and follow the prompts to completion\.

### Establish a Serial Connection<a name="nordic-serial-connection"></a>

After you connect your computer to your Nordic nRF52840 board and install Segger Embedded Studio, open a terminal tool, like PuTTy, Tera Term, or GNU Screen\. Configure the terminal to connect to your board by a serial connection\. Set the COM port to **JLink CDC UART Port** with the following serial port settings:
+ Baud Rate: **115200**
+ Data: **8 bit**
+ Parity: **None**
+ Stop: **1 bit**
+ Flow Control: **None**

**Note**  
Depending on your terminal tool, the serial port settings might vary in name\.

## Download and Configure Amazon FreeRTOS<a name="nordic-download-and-configure"></a>

After you set up your hardware and environment, you can download Amazon FreeRTOS\.

### Download Amazon FreeRTOS<a name="nordic-download"></a>

To download Amazon FreeRTOS for the Nordic nRF52840\-DK, go to the [Amazon FreeRTOS GitHub page](https://github.com/aws/amazon-freertos) and clone the repository\. The Amazon FreeRTOS BLE library is still in public beta, so you need to switch branches to access the code for the Nordic nRF52840\-DK board\. Check out the branch named `feature/ble-beta`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `AmazonFreeRTOS` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\AmazonFreeRTOS` works, but `C:\Users\Username\Documents\Development\Projects\AmazonFreeRTOS` causes build failures\.  
In this tutorial, the path to the `AmazonFreeRTOS` directory is referred to as `BASE_FOLDER`\.

### Configure Your Project<a name="nordic-freertos-config-project"></a>

To run the demo, you must configure your project to work with AWS IoT\. To configure your project to work with AWS IoT, your board must be registered as an AWS IoT thing\. This is a step in the [Prerequisites](freertos-prereqs.md)\.

**To configure your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint appears in the **Endpoint** text box\. It should look like `<1234567890123>-ats.iot.<us-east-1>.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\. Make a note of the AWS IoT thing name for your device\. 

1. With your AWS IoT endpoint and your AWS IoT thing name on hand, open `<BASE_FOLDER>\demos\common\include\aws_clientcredential.h` in your IDE, and specify values for the following `#define` constants:
   + `clientcredentialMQTT_BROKER_ENDPOINT` *Your AWS IoT endpoint*
   + `clientcredentialIOT_THING_NAME` *Your board's AWS IoT thing name*

**To configure your AWS IoT credentials**

To configure your AWS IoT credentials, you need the private key and certificate that you downloaded from the AWS IoT console when you registered your device as an AWS IoT thing\. After you have registered your device as an AWS IoT thing, you can retrieve device certificates from the AWS IoT console, but you cannot retrieve private keys\.

Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\. You need to format the certificate and private key for your device\. 

1. In a browser window, open `<BASE_FOLDER>\tools\certificate_configuration\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` that you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` that you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the existing file in the directory\.
**Note**  
The certificate and private key should be hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

**To enable the demo**

1. Check that the BLE GATT Demo is enabled\. Go to `<BASE_FOLDER>\demos\nordic\nrf52840-dk\common\config_files\aws_ble_config.h`, and make sure that `bleconfigENABLE_GATT_DEMO` is set to `1`\.

1. Open `<BASE_FOLDER>\demos\common\demo_runner\aws_demo_runner.c`, and in the demo declarations, uncomment `extern void vStartMQTTBLEEchoDemo( void );`\. In the `DEMO_RUNNER_RunDemos` definition, uncomment `vStartMQTTBLEEchoDemo();`\. \.

## Build and Run Amazon FreeRTOS Samples<a name="nordic-build-and-run-example"></a>

After you download Amazon FreeRTOS and configure your demo project, you are ready to build and run the demo project on your board\.

Open Segger Embedded Studio\. From the top menu, choose **File**, choose **Open Solution**, and then navigate to the project file `<BASE_FOLDER>\demos\nordic\nrf52840-dk\ses\aws_demos_ble.emProject`

From the top menu, choose **View**, and then choose **Debug Terminal** to display information from your serial connection terminal\.

To build the BLE demo, right\-click the `aws_ble_demos` demo project, and choose **Build**\.

**Note**  
If this is your first time using Segger Embedded Studio, you might see you a warning "No license for commercial use"\. Segger Embedded Studio can be used free of charge for Nordic Semiconductor devices\. Choose **Activate Your Free License**, and follow the instructions\.

To run the BLE demo on your board, from the Segger Embedded Studio menu, choose **Debug**, and then choose **Go**\.

For more information about completing the demo with the Amazon FreeRTOS BLE Mobile SDK demo application as the mobile MQTT proxy, see [MQTT over BLE Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-mqtt)\.