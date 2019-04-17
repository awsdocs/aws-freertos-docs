# Getting Started with the STMicroelectronics STM32L4 Discovery Kit IoT Node<a name="getting_started_st"></a>

If you do not already have the STMicroelectronics STM32L4 Discovery Kit IoT Node, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANsWUAX/STM32L4-Discovery-Kit-IoT-Node)\.

Make sure you have installed the latest Wi\-Fi firmware\. To download the latest Wi\-Fi firmware, see [STM32L4 Discovery kit IoT node, low\-power wireless, BLE, NFC, SubGHz, Wi\-Fi](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip)\. Under **Binary Resources**, choose **Inventek ISM 43362 Wi\-Fi module firmware update \(read the readme file for instructions\) **\.

Before you begin, you must configure AWS IoT and your Amazon FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the Amazon FreeRTOS download directory is referred to as `BASE_FOLDER`\.

## Setting Up Your Environment<a name="st-setup-env"></a>

### Install System Workbench for STM32<a name="install-system-workbench"></a>

1. Browse to [OpenSTM32\.org](http://www.openstm32.org/HomePage)\.

1. Register on the OpenSTM32 webpage\. You need to sign in to download System Workbench\.

1. Browse to the [System Workbench for STM32 installer](http://www.openstm32.org/System%2BWorkbench%2Bfor%2BSTM32) to download and install System Workbench\.

If you experience issues during installation, see the FAQs on the [System Workbench website](http://www.openstm32.org/HomePage)\.

## Build and Run the Amazon FreeRTOS Demo Project<a name="st-build-and-run-example"></a>

### Import the Amazon FreeRTOS Demo into the STM32 System Workbench<a name="st-freertos-import-project"></a><a name="st-import-project"></a>

1. Open the STM32 System Workbench and enter a name for a new workspace\.

1. From the **File** menu, choose **Import**\. Expand **General**, choose **Existing Projects into Workspace**, and then choose **Next**\.

1. In **Select Root Directory**, enter `<BASE_FOLDER>\demos\st\stm32l475_discovery\ac6`\.

1. The project `aws_demos` should be selected by default\.

1. Choose **Finish** to import the project into STM32 System Workbench\.

1. From the **Project** menu, choose **Build All**\. Confirm the project compiles without any errors or warnings\.

### Run the Amazon FreeRTOS Demo Project<a name="st-run-example"></a>

1. Use a USB cable to connect your STMicroelectronics STM32L4 Discovery Kit IoT Node to your computer\. 

1. Rebuild your project\.

1. From **Project Explorer**, right\-click `aws_demos`, choose **Debug As**, and then choose **Ac6 STM32 C/C\+\+ Application**\.

   If a debug error occurs the first time a debug session is launched, follow these steps:

   1. In STM32 System Workbench, from the **Run** menu, choose **Debug Configurations**\.

   1. Choose **aws\_demos Debug**\. \(You might need to expand **Ac6 STM32 Debugging**\.\)

   1. Choose the **Debugger** tab\.

   1. In **Configuration Script**, choose **Show Generator Options**\.

   1. In **Mode Setup**, set **Reset Mode** to **Software System Reset**\. Choose **Apply**, and then choose **Debug**\. 

1. When the debugger stops at the breakpoint in `main()`, from the **Run** menu, choose **Resume**\.

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

### Run the Bluetooth Low\-Energy Demo<a name="st-run-ble"></a>


|  | 
| --- |
| Amazon FreeRTOS support for Bluetooth Low Energy is in public beta release\. BLE demos are subject to change\. | 

**Note**  
To run the BLE demo, you need the SPBTLE\-1S BLE module for the STM32L475 Discovery Kit\.

Amazon FreeRTOS supports [Bluetooth Low Energy \(BLE\)](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ble-library.html) connectivity\. You can download Amazon FreeRTOS with BLE from [GitHub](https://github.com/aws/amazon-freertos/tree/feature/ble-beta)\. The Amazon FreeRTOS BLE library is still in public beta, so you need to switch branches to access the code for your board\. Check out the branch named `feature/ble-beta`\.

To run the Amazon FreeRTOS demo project across BLE, you need to run the Amazon FreeRTOS BLE Mobile SDK Demo Application on an iOS or Android mobile device\.

**To set up the the Amazon FreeRTOS BLE Mobile SDK Demo Application**

1. Follow the instructions in [Mobile SDKs for Amazon FreeRTOS Bluetooth Devices](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ble-mobile.html) to download and install the SDK for your mobile platform on your host computer\.

1. Follow the instructions in [Amazon FreeRTOS BLE Mobile SDK Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-sdk-app) to set up the demo mobile application on your mobile device\.

 For instructions about how to run the MQTT over BLE demo on your board, see the [MQTT over BLE demo application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-mqtt)\.

## Troubleshooting<a name="st-troubleshooting"></a>

If you see the following in the UART output from the demo application, you need to update the Wi\-Fi module’s firmware:

```
[Tmr Svc] WiFi firmware version is: xxxxxxxxxxxxx
[Tmr Svc] [WARN] WiFi firmware needs to be updated.
```

To download the latest Wi\-Fi firmware, see [STM32L4 Discovery kit IoT node, low\-power wireless, BLE, NFC, SubGHz, Wi\-Fi](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip)\. In **Binary Resources**, choose the download link for **Inventek ISM 43362 Wi\-Fi module firmware update**\.

For general troubleshooting information about Getting Started with Amazon FreeRTOS, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.