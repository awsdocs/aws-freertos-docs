# Bluetooth Low Energy Demo Applications<a name="ble-demo"></a>


|  | 
| --- |
| The Bluetooth Low Energy \(BLE\) Library is in public beta release for Amazon FreeRTOS and is subject to change\. | 

## Overview<a name="ble-demo-overview"></a>

Amazon FreeRTOS BLE includes three demo applications:

### [MQTT over BLE](#ble-demo-mqtt) Demo<a name="w3aac11c17b5b5"></a>

This application demonstrates how to use the MQTT over BLE service\.

### [Wi\-Fi Provisioning](#ble-demo-wifi) Demo<a name="w3aac11c17b5b7"></a>

This application demonstrates how to use the Wi\-Fi Provisioning service\.

### [Generic Attributes Server](#ble-demo-server) Demo<a name="w3aac11c17b5b9"></a>

This application demonstrates how to use the Amazon FreeRTOS BLE middleware APIs to create a simple GATT server\.

## Prerequisites<a name="ble-demo-prereqs"></a>

To follow along with these demos, you need a microcontroller with Bluetooth Low Energy capabilities\. You also need the [iOS SDK for Amazon FreeRTOS Bluetooth Devices](freertos-ble-mobile.md#freertos-ble-ios) or the [Android SDK for Amazon FreeRTOS Bluetooth Devices](freertos-ble-mobile.md#freertos-ble-android)\.

### Set Up AWS IoT and Amazon Cognito for Amazon FreeRTOS BLE<a name="set-up-ble-demo-aws"></a>

To connect your devices to AWS IoT across MQTT, you need to set up AWS IoT and Amazon Cognito\.

**To set up AWS IoT**

1. Set up an AWS account on [https://aws\.amazon\.com](https://aws.amazon.com)\.

1. Open the [AWS IoT console](https://console.aws.amazon.com/iot/), and from the navigation pane, choose **Manage**, and then choose **Things**\.

1. Choose **Create**, and then choose **Create a single thing**\.

1. Enter a name for your device, and then choose **Next**\.

1. If you are connecting your microcontroller to the cloud through a mobile device, choose **Create thing without certificate**\. Because the Mobile SDKs use Amazon Cognito for device authentication, you do not need to create a device certificate for demos that use BLE\.

   If you are connecting your microcontroller to the cloud directly over Wi\-Fi, choose **Create certificate**, choose **Activate**, and then download the thing's certificate, public key, and private key\.

1. Choose the thing that you just created from the list of registered things, and then choose **Interact** from your thing's page\. Make a note of the AWS IoT REST API endpoint\.

For more information about setting up, see the [Getting Started with AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html)\.

**To create an Amazon Cognito user pool**

1. Open the Amazon Cognito console, and choose **Manage User Pools**\.

1. Choose **Create a user pool**\.

1. Give the user pool a name, and then choose **Review defaults**\.

1. From the navigation pane, choose **App clients**, and then choose **Add an app client**\.

1. Enter a name for the app client, and then choose **Create app client**\.

1. From the navigation pane, choose **Review**, and then choose **Create pool**\.

   Make a note of the pool ID that appears on the **General Settings** page of your user pool\.

1. From the navigation pane, choose **App clients**, and then choose **Show details**\. Make a note of the app client ID and app client secret\.

**To create an Amazon Cognito identity pool**

1. Open the Amazon Cognito console, and choose **Manage Identity Pools**\.

1. Enter a name for your identity pool\.

1. Expand **Authentication providers**, choose the **Cognito** tab, and then enter your user pool ID and app client ID\.

1. Choose **Create Pool**\.

1. Expand **View Details**, and make a note of the two IAM role names\. Choose **Allow** to create the IAM roles for authenticated and unauthenticated identities to access Amazon Cognito\.

1. Choose **Edit identity pool**\. Make a note of the identity pool ID\. It should be of the form `us-west-2:12345678-1234-1234-1234-123456789012`\.

For more information about setting up Amazon Cognito, see the [Getting Started with Amazon Cognito](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-getting-started.html)\.

**To create and attach an IAM policy to the authenticated identity**

1. Open the IAM console, and from the navigation pane, choose **Roles**\.

1. Find and choose your authenticated identity's role, choose **Attach policies**, and then choose **Add inline policy**\.

1. Choose the **JSON** tab, and paste the following JSON:

   ```
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Effect":"Allow",
            "Action":[
               "iot:AttachPolicy",
               "iot:AttachPrincipalPolicy",
               "iot:Connect",
               "iot:Publish",
               "iot:Subscribe",
               "iot:Receive",
               "iot:GetThingShadow",
               "iot:UpdateThingShadow",
               "iot:DeleteThingShadow"
            ],
            "Resource":[
               "*"
            ]
         }
      ]
   }
   ```

1. Choose **Review policy**, enter a name for the policy, and then choose **Create policy**\.

Keep your AWS IoT and Amazon Cognito information on hand\. You need the endpoint and IDs to authenticate your mobile application with the AWS Cloud\.

### Set Up Your Amazon FreeRTOS Environment for BLE<a name="ble-demo-set-up"></a>

To set up your enviroment, you need to download Amazon FreeRTOS with the [Amazon FreeRTOS Bluetooth Low Energy Library \(Beta\)](freertos-ble-library.md) on your microcontroller, and download and configure the Mobile SDK for Amazon FreeRTOS Bluetooth Devices on your mobile device\.

**To set up your microcontroller's environment with Amazon FreeRTOS BLE**

1. Download the `feature/ble-beta` branch of the Amazon FreeRTOS [GitHub](https://github.com/aws/amazon-freertos/tree/feature/ble-beta) repository\.

1. Set up Amazon FreeRTOS on your microcontroller\.

   For information about getting started with Amazon FreeRTOS on an Amazon FreeRTOS\-qualified microcontroller, see the guide for your board in [Getting Started with Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-getting-started.html)\.
**Note**  
You can run the demos on any BLE\-enabled microcontroller with Amazon FreeRTOS and ported Amazon FreeRTOS BLE libraries\. Currently, the Amazon FreeRTOS [MQTT over BLE](#ble-demo-mqtt) demo project is fully ported to the following BLE\-enabled devices:  
[STMicroelectronics STM32L4 Discovery Kit IoT Node](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_st.html), with the STBTLE\-1S BLE module
[Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html)
[Nordic nRF52840\-DK](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_nordic.html)

## Common Components<a name="ble-demo-common"></a>

The Amazon FreeRTOS demo applications have two common components:
+ Network Manager
+ BLE Mobile SDK demo application

### Network Manager<a name="ble-demo-network-manager"></a>

Network Manager manages your microcontroller's network connection\. It is located in your Amazon FreeRTOS directory at `/demos/common/network_manager/aws_iot_network_manager.c`\. If the Network Manager is enabled for both Wi\-Fi and BLE, the demos start with BLE by default\. If the BLE connection is disrupted, and your board is Wi\-Fi\-enabled, the Network Manager switches to an available Wi\-Fi connection to prevent you from disconnecting from the network\.

To enable a network connection type with the Network Manager, add the network connection type to the `configENABLED_NETWORKS` parameter in `demos/vendor/board/common/config_files/aws_iot_network_config.h` \(where the *vendor* is the name of the vendor and the *board* is the name of the board that you are using to run the demos\)\.

For example, if you have both BLE and Wi\-Fi enabled, the line that starts with `#define configENABLED_NETWORKS` in `aws_iot_network_config.h` reads as follows:

```
#define  configENABLED_NETWORKS  ( AWSIOT_NETWORK_TYPE_BLE | AWSIOT_NETWORK_TYPE_WIFI )
```

To get a list of currently supported network connection types, see the lines that begin with `#define AWSIOT_NETWORK_TYPE` in `lib/include/aws_iot_network.h`\.

### Amazon FreeRTOS BLE Mobile SDK Demo Application<a name="ble-sdk-app"></a>

The Amazon FreeRTOS BLE Mobile SDK demo application is located in the [Android SDK for Amazon FreeRTOS Bluetooth Devices](https://github.com/aws/amazon-freertos-ble-android-sdk/tree/beta/app) under `amazon-freertos-ble-android-sdk/app` and the [iOS SDK for Amazon FreeRTOS Bluetooth Devices](https://github.com/aws/amazon-freertos-ble-ios-sdk/tree/beta/Example/AmazonFreeRTOSDemo) under `amazon-freertos-ble-ios-sdk/Example/AmazonFreeRTOSDemo`\. In this example, we use screenshots of the iOS version of the demo mobile application\.

**Note**  
If you are using an iOS device, you need Xcode to build the demo mobile application\. If you are using an Android device, you can use Android Studio to build the demo mobile application\.

**To configure the iOS SDK demo application**

When you define configuration variables, use the format of the placeholder values provided in the configuration files\.

1. Confirm that you the [iOS SDK for Amazon FreeRTOS Bluetooth Devices](freertos-ble-mobile.md#freertos-ble-ios) is installed\.

1. Issue the following command from `amazon-freertos-ble-ios-sdk/Example/AmazonFreeRTOSDemo/`:

   ```
   $ pod install
   ```

1. Open the `amazon-freertos-ble-ios-sdk/Example/AmazonFreeRTOSDemo/AmazonFreeRTOSDemo.xcodeproj` project with Xcode, and change the signing developer account to your account\.

1. Open `amazon-freertos-ble-ios-sdk/Example/AmazonFreeRTOSDemo/AmazonFreeRTOSDemo/Amazon/AmazonConstants.swift`, and redefine the following variables:
   + `region`: Your AWS Region\.
   + `iotPolicyName`: Your AWS IoT policy name\.
   + `mqttCustomTopic`: The MQTT topic that you want to publish to\.
**Note**  
You need to have set up AWS and AWS IoT to have an AWS region and an AWS IoT policy\.

1. Open `amazon-freertos-ble-ios-sdk/Example/AmazonFreeRTOSDemo/AmazonFreeRTOSDemo/Support/awsconfiguration.json`\.

   Under `CognitoIdentity`, redefine the following variables:
   + `PoolId`: Your Amazon Cognito identity pool ID\.
   + `Region`: Your AWS Region\.

   Under `CognitoUserPool`, redefine the following variables:
   + `PoolId`: Your Amazon Cognito user pool ID\.
   + `AppClientId`: Your app client ID\.
   + `AppClientSecret`: Your app client secret\.
   + `Region`: Your AWS Region\.

**To configure the Android SDK demo application**

When you define configuration variables, use the format of the placeholder values provided in the configuration files\.

1. Confirm that the [Android SDK for Amazon FreeRTOS Bluetooth Devices](freertos-ble-mobile.md#freertos-ble-android) is installed\.

1. Open `amazon-freertos-ble-android-sdk/app/src/main/java/com/amazon/aws/freertosandroid/AuthenticatorActivity.java`, and redefine the following variables:
   + `AWS_IOT_POLICY_NAME`: Your AWS IoT policy name\.
   + `AWS_IOT_REGION`: Your AWS Region\.
   + `COGNITO_POOL_ID`: Your Amazon Cognito identity pool ID\.
   + `COGNITO_REGION`: Your AWS Region\.

1. Open `amazon-freertos-ble-android-sdk/app/src/main/java/com/amazon/aws/freertosandroid/MainActivity.java`, and redefine the following variables:
   + `BLE_DEVICE_MAC_ADDR`: The MAC address of your device\.
   + `BLE_DEVICE_NAME`: Your device name\.
   + `MTU`: The desired MTU between your microcontroller and mobile device\.

1. Open `amazon-freertos-ble-android-sdk/app/src/main/res/raw/awsconfiguration.json`\.

   Under `CognitoIdentity`, redefine the following variables:
   + `PoolId`: Your Amazon Cognito identity pool ID\.
   + `Region`: Your AWS Region\.

   Under `CognitoUserPool`, redefine the following variables:
   + `PoolId`: Your Amazon Cognito user pool ID\.
   + `AppClientId`: Your app client ID\.
   + `AppClientSecret`: Your app client secret\.
   + `Region`: Your AWS Region\.

**To discover and establish secure connections with your microcontroller over BLE**

1. Run the BLE demo project on your microcontroller\.

1. Run the BLE Mobile SDK demo application on your mobile device\.

   To start the demo application in the Android SDK from the command line, run the following command:

   ```
   $ ./gradlew installDebug
   ```

1. Confirm that your microcontroller appears under **Devices** on the BLE Mobile SDK demo app\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list1.png)
**Note**  
All devices with Amazon FreeRTOS and the device information service \(`/lib/bluetooth_low_energy/services/device_information`\) that are in range appear in the list\.

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

In the MQTT over BLE demo, your microcontroller publishes messages to the AWS Cloud through an MQTT proxy\.

**To subscribe to a demo MQTT topic**

1. Sign in to the AWS IoT console\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-hw-test-console.png)

You can run the MQTT demo over a BLE or Wi\-Fi connection\. The configuration of the [Network Manager](#ble-demo-network-manager) determines which connection type is used\. 

If you use BLE to pair the microcontroller with your mobile device, the MQTT messages are routed through the BLE Mobile SDK demo application on your mobile device\.

If you use Wi\-Fi, the demo is the same as the MQTT Hello World demo project located at `demos/common/mqtt/aws_hello_world.c`\. That demo is used in most of the [Getting Started with Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-getting-started.html) demo projects\.

**To enable the demo**

If you have already enabled the BLE demo by following the instructions in the Getting Started guide for your device, you can skip these instructions\.

1. Enable the Wi\-Fi Provisioning service\. Open `demos/vendor/board/common/config_files/iot_ble_config.h`, and set `#define bleconfigENABLE_WIFI_PROVISIONING` to `1` \(where the *vendor* is the name of the vendor and the *board* is the name of the board that you are using to run the demos\)\.
**Note**  
The Wi\-Fi Provisioning service is disabled by default\.

1. Open `demos/common/include/iot_demo_runner.h`, comment out `#define democonfigMQTTDemo_ENABLED`, and uncomment `#define democonfigBLE_MQTT_ECHO_DEMO_ENABLED`\.

**To run the demo**

If the Network Manager is configured for Wi\-Fi only, simply build and run the demo project on your board\.

If the Network Manager is configured for BLE, do the following:

1. Build and run the demo project on your microcontroller\.

1. Make sure that you have paired your board and your mobile device using the [Amazon FreeRTOS BLE Mobile SDK Demo Application](#ble-sdk-app)\.

1. From the **Devices** list in the demo mobile app, choose your microcontroller, and then choose **MQTT Proxy** to open the MQTT proxy settings\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list4.png)

1. Touch **Enable MQTT proxy** to enable the MQTT proxy\. The slider should turn green\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-proxy.png)

   After you enable the MQTT proxy, MQTT messages appear on the `freertos/demos/echo` topic, and data is printed to the UART terminal\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-terminal-output.png)

## Wi\-Fi Provisioning<a name="ble-demo-wifi"></a>

Wi\-Fi Provisioning is an Amazon FreeRTOS BLE service that allows you to securely send Wi\-Fi network credentials from a mobile device to a microcontroller over BLE\. The source code for the Wi\-Fi Provisioning service can be found at `lib/bluetooth_low_energy/services/wifi_provisioning`\.

**Note**  
The Wi\-Fi Provisioning demo is currently supported on the Espressif ESP32\-DevKitC\.  
The Android version of the demo mobile application does not currently support Wi\-Fi Provisioning\.

**To enable the demo**

1. Enable the Wi\-Fi Provisioning service\. Open `demos/vendor/board/common/config_files/iot_ble_config.h`, and set `#define bleconfigENABLE_WIFI_PROVISIONING` to `1` \(where the *vendor* is the name of the vendor and the *board* is the name of the board that you are using to run the demos\)\.
**Note**  
The Wi\-Fi Provisioning service is disabled by default\.

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

   The micrcontroller connects to and saves the network\. The network appears under **Saved Networks**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-network-config2.png)

You can save several networks in the demo mobile app\. When you restart the application and demo, the microcontroller connects to the first available saved network, starting from the top of the **Saved Networks** list\. 

To change the network priority order or delete networks, on the **Network Configuration** page, choose **Editing Mode**\. To change the network priority order, choose the right side of the network that you want to reprioritize, and drag the network up or down\. To delete a network, choose the red button on the left side of the network that you want to delete\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-network-editing.png)

## Generic Attributes Server<a name="ble-demo-server"></a>

In this example, a demo Generic Attributes \(GATT\) server application on your microcontroller sends a simple counter value to the [Amazon FreeRTOS BLE Mobile SDK Demo Application](#ble-sdk-app)\.

Using the BLE Mobile SDKs, you can create your own GATT client for a mobile device that connects to the GATT server on your microcontroller and runs in parallel with the demo mobile application\.

**To enable the demo**

1. Enable the BLE GATT demo\. In `demos/vendor/board/common/config_files/iot_ble_config.h` \(where the *vendor* is the name of the vendor and the *board* is the name of the board that you are using to run the demos\), add `#define bleconfigENABLE_GATT_DEMO ( 1 )` to the list of define statements\.
**Note**  
The BLE GATT demo is disabled by default\.

1. Open `demos/common/include/iot_demo_runner.h`, comment out `#define democonfigMQTTDemo_ENABLED`, and uncomment `#define democonfigBLE_MQTT_ECHO_DEMO_ENABLED`\.

**To run the demo**

1. Build and run the demo project on your microcontroller\.

1. Make sure that you have paired your board and your mobile device using the [Amazon FreeRTOS BLE Mobile SDK Demo Application](#ble-sdk-app)\.

1. From the **Devices** list in the app, choose your board, and then choose **MQTT Proxy** to open the MQTT proxy options\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list4.png)

1. Touch **Enable MQTT proxy** to enable the MQTT proxy\. The slider should turn green\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-proxy.png)

1. Return to the **Devices** list, choose your board, and then choose **Custom GATT MQTT** to open the custom GATT service options\.

1. Choose **Start Counter** to start publishing data to the `freertos/demos/echo` MQTT topic\.

   After you enable the MQTT proxy, Hello World and incrementing counter messages appear on the `freertos/demos/echo` topic\.