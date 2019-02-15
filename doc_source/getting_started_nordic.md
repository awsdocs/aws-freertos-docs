# Getting Started with the Nordic nRF52840\-DK<a name="getting_started_nordic"></a>


|  | 
| --- |
| Amazon FreeRTOS support for the Nordic nRF52840\-DK is in public beta release\. BLE demos are subject to change\. | 

Before you begin, you need to [Set Up AWS IoT and Amazon Cognito for Amazon FreeRTOS BLE](ble-demo.md#set-up-ble-demo-aws)\.

If you do not have the Nordic nRF52840\-DK, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtrUAH/nRF52840-Development-Kit)\.

To run the Amazon FreeRTOS BLE demo, you also need an iOS or Android mobile device with Bluetooth and Wi\-Fi capabilities\.

## Setting Up the Nordic Hardware<a name="nordic-setup-env"></a>

Connect your host computer to the USB port labaled J2, located directly above the coin cell battery holder on your Nordic nRF52840 board\.

For more information about setting up the Nordic nRF52840\-DK, see the [nRF52840 Development Kit User Guide](http://infocenter.nordicsemi.com/pdf/nRF52840_DK_User_Guide_v1.2.pdf)\.

## Setting Up Your Environment<a name="nordic-setup-env"></a>

### Download and Install Segger Embedded Studio<a name="install-embedded-studio"></a>

Amazon FreeRTOS supports Segger Embedded Studio as a development environment for the Nordic nRF52840\-DK\.

To set up your environment, you need to download and install Segger Embedded Studio on your host computer\.

**To download and install Segger Embedded Studio**

1. Go to the [Segger Embedded Studio Downloads](https://www.segger.com/downloads/embedded-studio/) page, and choose the Embedded Studio for ARM option for your operating system\.

1. Run the installer and follow the prompts to completion\.

### Set Up the Amazon FreeRTOS BLE Mobile SDK Demo Application<a name="install-mobile-sdks"></a>

To run the Amazon FreeRTOS demo project across BLE, you need to run the Amazon FreeRTOS BLE Mobile SDK Demo Application on your mobile device\.

**To set up the the Amazon FreeRTOS BLE Mobile SDK Demo Application**

1. Follow the instructions in [Mobile SDKs for Amazon FreeRTOS Bluetooth Devices](freertos-ble-mobile.md) to download and install the SDK for your mobile platform on your host computer\.

1. Follow the instructions in [Amazon FreeRTOS BLE Mobile SDK Demo Application](ble-demo.md#ble-sdk-app) to set up the demo mobile application on your mobile device\.

### Establish a Serial Connection<a name="nordic-serial-connection"></a>

Segger Embedded Studio includes a terminal emulator that you can use to receive log messages across a serial connection to your board\.

**To establish a serial connection with Segger Embedded Studio**

1. Open Segger Embedded Studio\.

1. From the top menu, choose **Target**, **Connect J\-Link**\.

1. From the top menu, choose **Tools**, **Terminal Emulator**, **Properties**, and set the properties as instructed in [Installing a Terminal Emulator](uart-term.md)\.

1. From the top menu, choose **Tools**, **Terminal Emulator**, **Connect *port* \(115200,N,8,1\)**\.

**Note**  
You can also establish a serial connection with a terminal tool of your choice, like PuTTy, Tera Term, or GNU Screen\. Configure the terminal to connect to your board by a serial connection as instructed in [Installing a Terminal Emulator](uart-term.md)\.

## Download and Configure Amazon FreeRTOS<a name="nordic-download-and-configure"></a>

After you set up your hardware and environment, you can download Amazon FreeRTOS\.

### Download Amazon FreeRTOS<a name="nordic-download"></a>

To download Amazon FreeRTOS for the Nordic nRF52840\-DK, go to the [Amazon FreeRTOS GitHub page](https://github.com/aws/amazon-freertos) and clone the repository\. The Amazon FreeRTOS BLE library is still in public beta, so you need to switch branches to access the code for the Nordic nRF52840\-DK board\. Check out the branch named `feature/ble-beta`\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. The longest path in the Amazon FreeRTOS download is 122 characters\. To accommodate the files in the Amazon FreeRTOS projects, make sure that the path to the `amazon-freertos` directory is fewer than 98 characters long\. For example, `C:\Users\Username\Dev\amazon-freertos` works, but `C:\Users\Username\Documents\Development\Projects\amazon-freertos` causes build failures\.  
In this tutorial, the path to the `amazon-freertos` directory is referred to as `BASE_FOLDER`\.

### Configure Your Project<a name="nordic-freertos-config-project"></a>

To run the demo, you need to configure your project to work with AWS IoT\. To configure your project to work with AWS IoT, your device must be registered as an AWS IoT thing\. You should have registered your device when you [Set Up AWS IoT and Amazon Cognito for Amazon FreeRTOS BLE](ble-demo.md#set-up-ble-demo-aws)\.

**To configure your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint appears in the **Endpoint** text box\. It should look like `<1234567890123>-ats.iot.<us-east-1>.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\. Make a note of the AWS IoT thing name for your device\. 

1. With your AWS IoT endpoint and your AWS IoT thing name on hand, open `<BASE_FOLDER>\demos\common\include\aws_clientcredential.h` in your IDE, and specify values for the following `#define` constants:
   + `clientcredentialMQTT_BROKER_ENDPOINT` *Your AWS IoT endpoint*
   + `clientcredentialIOT_THING_NAME` *Your board's AWS IoT thing name*

**To enable the demo**

1. Check that the BLE GATT Demo is enabled\. Go to `<BASE_FOLDER>\demos\nordic\nrf52840-dk\common\config_files\aws_ble_config.h`, and make sure that `bleconfigENABLE_GATT_DEMO` is set to `1`\.

1. Open `<BASE_FOLDER>\demos\common\demo_runner\aws_demo_runner.c`, and in the demo declarations, uncomment `extern void vStartMQTTBLEEchoDemo( void );`\. In the `DEMO_RUNNER_RunDemos` definition, uncomment `vStartMQTTBLEEchoDemo();`\.

## Build and Run the Amazon FreeRTOS Demo Project<a name="nordic-build-and-run-example"></a>

After you download Amazon FreeRTOS and configure your demo project, you are ready to build and run the demo project on your board\.

**To build and run the Amazon FreeRTOS BLE demo from Segger Embedded Studio**

1. Open Segger Embedded Studio\. From the top menu, choose **File**, choose **Open Solution**, and then navigate to the project file `<BASE_FOLDER>\demos\nordic\nrf52840-dk\ses\aws_demos_ble.emProject`

1. If you are using the Segger Embedded Studio terminal emulator, choose **Tools** from the top menu, and then choose **Terminal Emulator**, **Terminal Emulator** to display information from your serial connection\.

   If you are using another terminal tool, you can monitor that tool for output from your serial connection\.

1. Right\-click the `aws_ble_demos` demo project in the **Project Explorer**, and choose **Build**\.
**Note**  
If this is your first time using Segger Embedded Studio, you might see you a warning "No license for commercial use"\. Segger Embedded Studio can be used free of charge for Nordic Semiconductor devices\. Choose **Activate Your Free License**, and follow the instructions\.

1. Choose **Debug**, and then choose **Go**\.

1. Follow the instructions for the [MQTT over BLE Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-mqtt) to complete the demo with the Amazon FreeRTOS BLE Mobile SDK demo application as the mobile MQTT proxy\.

## Troubleshooting<a name="nordic-troubleshooting"></a>

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.