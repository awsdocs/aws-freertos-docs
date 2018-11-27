# Bluetooth Low Energy Demo Applications \(Beta\)<a name="ble-demo"></a>


|  | 
| --- |
| The Bluetooth Low Energy \(BLE\) Library is in public beta release for Amazon FreeRTOS and is subject to change\. | 

## Overview<a name="ble-demo-overview"></a>

Amazon FreeRTOS BLE includes three demo applications:

### [MQTT over BLE](#ble-demo-mqtt) Demo<a name="w3aac12b9b5b5"></a>

This application demonstrates how to use the MQTT over BLE service\.

### [Wi\-Fi Provisioning](#ble-demo-wifi) Demo<a name="w3aac12b9b5b7"></a>

This application demonstrates how to use the Wi\-Fi Provisioning service\.

### [Generic Attributes Server](#ble-demo-server) Demo<a name="w3aac12b9b5b9"></a>

This application demonstrates how to use the Amazon FreeRTOS BLE middleware APIs to create a simple GATT server\.

## Prerequisites<a name="ble-demo-prereqs"></a>

To follow along with these demos, you need a microcontroller with Bluetooth Low Energy capabilities\.

Before you begin, do the following:

### Set Up AWS IoT<a name="w3aac12b9b7b7"></a>

To set up AWS IoT, you need to do the following:
+ Set up an AWS account\.
+ Register your device as an AWS IoT thing\.
+ Download your AWS IoT credentials\.

For more information about setting up AWS IoT, see the [AWS IoT Developer Guide](https://docs.aws.amazon.com/iot/latest/developerguide/)\.

### Set Up Amazon Cognito<a name="w3aac12b9b7b9"></a>

To set up Amazon Cognito, you need to do the following:
+ Set up an AWS account\.
+ Create an Amazon Cognito user pool\.
+ Create an Amazon Cognito identity pool\.
+ Attach an IAM policy to the authenticated identity\.

For more information about setting up Amazon Cognito, see the [Amazon Cognito Developer Guide](https://docs.aws.amazon.com/cognito/latest/developerguide/)\.

### Set Up Your Environment<a name="w3aac12b9b7c11"></a>

To set up your enviroment, do the following:
+ Set up your microcontroller's environment with Amazon FreeRTOS and the Amazon FreeRTOS BLE library\. You can download Amazon FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos)\.

  For information about getting started with Amazon FreeRTOS on an Amazon FreeRTOS\-qualified microcontroller, see information for your board in [Getting Started with Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-getting-started.html)\.
**Note**  
You can run the demos on any BLE\-enabled microcontroller with Amazon FreeRTOS and ported Amazon FreeRTOS BLE libraries\. Currently, the Amazon FreeRTOS [MQTT over BLE](#ble-demo-mqtt) demo project is fully ported to the following BLE\-enabled devices:  
[STMicroelectronics STM32L4 Discovery Kit IoT Node](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_st.html), with the STBTLE\-1S BLE module
[Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html)
[Nordic nRF52840\-DK](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_nordic.html)
+ Install the [Amazon FreeRTOS BLE Mobile SDK Demo Application](#ble-sdk-app) on your Android or iOS device\. The demo application is a common component of the demos\.

  For information about installing the demo app, see the GitHub README files for the [Amazon FreeRTOS BLE Mobile SDK for Android](https://github.com/aws/amazon-freertos-ble-android-sdk/) or the [Amazon FreeRTOS BLE Mobile SDK for iOS](https://github.com/aws/amazon-freertos-ble-ios-sdk/)\.

## Common Components<a name="ble-demo-common"></a>

The Amazon FreeRTOS demo applications have two common components:
+ Network Manager
+ BLE Mobile SDK demo application

### Network Manager<a name="ble-demo-network-manager"></a>

Network Manager manages your microcontroller's network connection\. It is located in your Amazon FreeRTOS directory at `\demos\common\network_manager\aws_iot_network_manager.c`\. If the network manager is enabled for both Wi\-Fi and BLE, the demos start with BLE by default\. If the BLE connection is disrupted, and your board is Wi\-Fi\-enabled, the Network Manager switches to an available Wi\-Fi connection to prevent you from disconnecting from the network\.

To enable a network connection type with the Network Manager, add the network connection type to the `configENABLED_NETWORKS` parameter in `demos/vendor/board/common/config_files/aws_iot_network_config.h`\. For example, if you have both BLE and Wi\-Fi enabled, the line that starts with `#define configENABLED_NETWORKS` in `aws_iot_network_config.h` reads as follows:

```
#define  configENABLED_NETWORKS  ( AWSIOT_NETWORK_TYPE_BLE | AWSIOT_NETWORK_TYPE_WIFI )
```

To get a list of currently supported network connection types, see `lib\include\aws_iot_network_manager.h`\.

### Amazon FreeRTOS BLE Mobile SDK Demo Application<a name="ble-sdk-app"></a>

Each demo uses the Amazon FreeRTOS BLE Mobile SDK demo application, which can be found in the [BLE Android SDK](https://github.com/aws/amazon-freertos-ble-android-sdk/tree/master/FreeRTOSDemo/Examples) or the [BLE iOS SDK](https://github.com/aws/amazon-freertos-ble-ios-sdk/tree/master/FreeRTOSDemo/Examples) under `FreeRTOSDemo/Examples`\. In this example, we use the iOS version of the demo mobile application\.

To discover and establish secure connections with your microcontroller across BLE with the demo mobile application, for each demo, do the following:

1. Run the [MQTT over BLE](#ble-demo-mqtt), [Wi\-Fi Provisioning](#ble-demo-wifi), or [Generic Attributes Server](#ble-demo-server) demo on your microcontroller\.

1. Start the BLE mobile SDK demo application on your mobile device\.

1. Confirm that your microcontroller appears under **Devices** on the BLE mobile SDK demo app\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list1.png)
**Note**  
Only devices with Amazon FreeRTOS and the device information service \(`\lib\bluetooth_low_energy\services\device_information`\) appear in the list\.

1. Choose your microcontroller from the list of devices\. The application establishes a connection with the board, and a green line appears next to the connected device\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list2.png)

   You can disconnect from your microntroller by dragging the line to the left\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list3.png)

1. You might be prompted to pair your microcontroller and mobile device\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/pairing-board.png)  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/pairing-mobile.png)

   If the code for numeric comparison is the same on both devices, pair the devices\.

**Note**  
The BLE Mobile SDK demo application uses Amazon Cognito for user authentication\. Make sure that you have set up a Amazon Cognito user and identity pools, and that you have attached IAM policies to authenticated identities\.

## MQTT over BLE<a name="ble-demo-mqtt"></a>

In the MQTT over BLE demo, your microcontroller publishes messages to the AWS IoT cloud through an MQTT proxy\.

**To subscribe to a demo MQTT topic**

1. Sign in to the AWS IoT console\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-hw-test-console.png)

You can run the MQTT demo across a BLE or Wi\-Fi connection\. The configuration of the [Network Manager](#ble-demo-network-manager) determines which connection type is used\. 

If you use BLE to pair the microcontroller with your mobile device, the MQTT messages are routed through the BLE mobile SDK demo application on your mobile device\.

If you use Wi\-Fi, the demo is the same as the MQTT Hello World demo project located in [https://github.com/aws/amazon-freertos/tree/master/demos/vendor/board/ide](https://github.com/aws/amazon-freertos/tree/master/demos/vendor/board/ide)\. That demo is used in most of the [Getting Started with Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-getting-started.html) demo projects\.

**To enable the demo**

If you have already enabled the demo by following the instructions in the getting started guide for your device, you can skip these instructions\.

1. Confirm that the MQTT over BLE and Wi\-Fi Provisioning services are enabled in `lib\utils\aws_ble_services_init.c`\. The services are enabled by default\.

1. Open `demos\common\demo_runner\aws_demo_runner.c`, and in the demo declarations, uncomment `extern void vStartMQTTBLEEchoDemo( void );`\. In the `DEMO_RUNNER_RunDemos` definition, uncomment `vStartMQTTBLEEchoDemo();`\.

**To run the demo**

If the Network Manager is configured for Wi\-Fi only, simply build and run the demo project on your board\.

If the Network Manager is configured for BLE, do the following:

1. Build and run the demo project on your microcontroller\.

1. Make sure that you have paired your board and your mobile device using the [Amazon FreeRTOS BLE Mobile SDK Demo Application](#ble-sdk-app)\.

1. From the **Devices**list in the demo mobile app, choose your microcontroller, and then choose **MQTT Proxy** to open the MQTT proxy settings\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list4.png)

1. Touch **Enable MQTT proxy** to enable the MQTT proxy\. The slider should turn green\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-proxy.png)

   After you enable the MQTT proxy, MQTT messages appear on the `freertos/demos/echo` topic, and data is printed to the UART terminal\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-terminal-output.png)

## Wi\-Fi Provisioning<a name="ble-demo-wifi"></a>

Wi\-Fi Provisioning is an Amazon FreeRTOS BLE service that allows you to securely send Wi\-Fi network credentials from a mobile device to a microcontroller over BLE\. The source code for the Wi\-Fi Provisioning service can be found at `lib/bluetooth_low_energy/services/wifi_provisioning`\.

**Note**  
The Wi\-Fi provisioning demo is currently supported on the Espressif ESP32\-DevKitC\.  
The Android version of the demo mobile application does not currently support Wi\-Fi Provisioning\.

**To enable the demo**

1. Confirm that the Wi\-Fi Provisioning service is enabled in the `lib\utils\aws_ble_services_init.c` file\. The service is enabled by default\.

1. Configure the [Network Manager](#ble-demo-network-manager) to enable both BLE and Wi\-Fi\.

**To run the demo**

1. Build and run the demo project on your microcontroller\.

1. Make sure that you have paired your microntroller and your mobile device using the [Amazon FreeRTOS BLE Mobile SDK Demo Application](#ble-sdk-app)\.

1. From the **Devices** list in the demo mobile app, choose your microcontoller, and then choose **Network Config** to open the network configuration settings\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list4.png)

1. After you choose **Network Config** for your board, the microcontroller sends a list of the networks in the vicinity to the mobile device\. Available Wi\-Fi networks appear in a list under **Scanned Networks**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-network-config1.png)

   From the **Scanned Networks** list, choose your network, and then enter the SSID and password, if required\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-wifi-password.png)

   The micrcontroller connects to and saves the network\. The network appears under the **Saved Networks**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-network-config2.png)

You can save several networks in the demo mobile app\. When you restart the application and demo, the microcontroller connects to the first available saved network, starting from the top of the **Saved Networks** list\. 

To change the network priority order or delete networks, on the **Network Configuration** page, choose **Editing Mode**\. To change the network priority order, choose the right side of the network that you want to reprioritize, and drag the network up or down\. To delete a network, choose the red button on the left side of the network that you want to delete\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-network-editing.png)

## Generic Attributes Server<a name="ble-demo-server"></a>

In this example, a demo Generic Attributes \(GATT\) server application on your microcontroller sends a simple counter value to the [Amazon FreeRTOS BLE Mobile SDK Demo Application](#ble-sdk-app) that is used for [MQTT over BLE](#ble-demo-mqtt) and [Wi\-Fi Provisioning](#ble-demo-wifi)\.

Using the BLE Mobile SDKs, you can create your own GATT client for a mobile device that connects to the GATT server on your microcontroller and runs in parallel with the demo mobile application\.

**To run the demo**

1. Build and run the demo project on your microcontroller\.

1. Make sure that you have paired your board and your mobile device using the [Amazon FreeRTOS BLE Mobile SDK Demo Application](#ble-sdk-app)\.

1. From the **Devices** list in the mobile SDK app, choose your board, and then choose **Custom GATT MQTT** to open the custom GATT service options\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list4.png)

1. Touch **Enable MQTT proxy** to enable the MQTT proxy\. The slider should turn green\.

1. Choose **Start Counter** to start publishing data to the `freertos/demos/echo` MQTT topic\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-proxy.png)

   After you enable the MQTT proxy, Hello World and incrementing counter messages appear on the `freertos/demos/echo` topic\.