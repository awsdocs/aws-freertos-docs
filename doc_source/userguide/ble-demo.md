# Bluetooth Low Energy demo applications<a name="ble-demo"></a>

## Overview<a name="ble-demo-overview"></a>

FreeRTOS Bluetooth Low Energy includes three demo applications:
+ [MQTT over Bluetooth Low Energy](#ble-demo-mqtt) demo 

  This application demonstrates how to use the MQTT over Bluetooth Low Energy service\.
+ [Wi\-Fi provisioning](#ble-demo-wifi) demo

  This application demonstrates how to use the Bluetooth Low Energy Wi\-Fi Provisioning service\.
+ [Generic Attributes Server](#ble-demo-server) demo

  This application demonstrates how to use the FreeRTOS Bluetooth Low Energy middleware APIs to create a simple GATT server\.

**Note**  
To set up and run the FreeRTOS demos, follow the steps in [Getting Started with FreeRTOS](freertos-getting-started.md)\.

## Prerequisites<a name="ble-demo-prereqs"></a>

To follow along with these demos, you need a microcontroller with Bluetooth Low Energy capabilities\. You also need the [iOS SDK for FreeRTOS Bluetooth devices](freertos-ble-mobile.md#freertos-ble-ios) or the [Android SDK for FreeRTOS Bluetooth devices](freertos-ble-mobile.md#freertos-ble-android)\.

### Set up AWS IoT and Amazon Cognito for FreeRTOS Bluetooth Low Energy<a name="set-up-ble-demo-aws"></a>

To connect your devices to AWS IoT across MQTT, you need to set up AWS IoT and Amazon Cognito\.

**To set up AWS IoT**

1. Set up an AWS account on [https://aws\.amazon\.com/](https://aws.amazon.com/)\.

1. Open the [AWS IoT console](https://console.aws.amazon.com/iot/), and from the navigation pane, choose **Manage**, and then choose **Things**\.

1. Choose **Create**, and then choose **Create a single thing**\.

1. Enter a name for your device, and then choose **Next**\.

1. If you are connecting your microcontroller to the cloud through a mobile device, choose **Create thing without certificate**\. Because the Mobile SDKs use Amazon Cognito for device authentication, you do not need to create a device certificate for demos that use Bluetooth Low Energy\.

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

### Set up your FreeRTOS environment for Bluetooth Low Energy<a name="ble-demo-set-up"></a>

To set up your environment, you need to download FreeRTOS with the [Bluetooth Low Energy library](freertos-ble-library.md) on your microcontroller, and download and configure the Mobile SDK for FreeRTOS Bluetooth Devices on your mobile device\.

**To set up your microcontroller's environment with FreeRTOS Bluetooth Low Energy**

1. Download or clone FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos)\. See the [README\.md](https://github.com/aws/amazon-freertos/blob/main/README.md) file for instructions\.

1. Set up FreeRTOS on your microcontroller\.

   For information about getting started with FreeRTOS on a FreeRTOS\-qualified microcontroller, see the guide for your board in [Getting Started with FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-getting-started.html)\.
**Note**  
You can run the demos on any Bluetooth Low Energy\-enabled microcontroller with FreeRTOS and ported FreeRTOS Bluetooth Low Energy libraries\. Currently, the FreeRTOS [MQTT over Bluetooth Low Energy](#ble-demo-mqtt) demo project is fully ported to the following Bluetooth Low Energy\-enabled devices:  
[Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html)
[Nordic nRF52840\-DK](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_nordic.html)

## Common components<a name="ble-demo-common"></a>

The FreeRTOS demo applications have two common components:
+ Network Manager
+ Bluetooth Low Energy Mobile SDK demo application

### Network Manager<a name="ble-demo-network-manager"></a>

Network Manager manages your microcontroller's network connection\. It is located in your FreeRTOS directory at `demos/network_manager/aws_iot_network_manager.c`\. If the Network Manager is enabled for both Wi\-Fi and Bluetooth Low Energy, the demos start with Bluetooth Low Energy by default\. If the Bluetooth Low Energy connection is disrupted, and your board is Wi\-Fi\-enabled, the Network Manager switches to an available Wi\-Fi connection to prevent you from disconnecting from the network\.

To enable a network connection type with the Network Manager, add the network connection type to the `configENABLED_NETWORKS` parameter in `vendors/vendor/boards/board/aws_demos/config_files/aws_iot_network_config.h` \(where the *vendor* is the name of the vendor and the *board* is the name of the board that you are using to run the demos\)\.

For example, if you have both Bluetooth Low Energy and Wi\-Fi enabled, the line that starts with `#define configENABLED_NETWORKS` in `aws_iot_network_config.h` reads as follows:

```
#define  configENABLED_NETWORKS  ( AWSIOT_NETWORK_TYPE_BLE | AWSIOT_NETWORK_TYPE_WIFI )
```

To get a list of currently supported network connection types, see the lines that begin with `#define AWSIOT_NETWORK_TYPE` in `aws_iot_network.h`\.

### FreeRTOS Bluetooth Low Energy Mobile SDK demo application<a name="ble-sdk-app"></a>

The FreeRTOS Bluetooth Low Energy Mobile SDK demo application is located on GitHub at [Android SDK for FreeRTOS Bluetooth Devices](https://github.com/aws/amazon-freertos-ble-android-sdk) under `amazon-freertos-ble-android-sdk/app` and the [iOS SDK for FreeRTOS Bluetooth Devices](https://github.com/aws/amazon-freertos-ble-ios-sdk) under `amazon-freertos-ble-ios-sdk/Example/AmazonFreeRTOSDemo`\. In this example, we use screenshots of the iOS version of the demo mobile application\.

**Note**  
If you are using an iOS device, you need Xcode to build the demo mobile application\. If you are using an Android device, you can use Android Studio to build the demo mobile application\.

**To configure the iOS SDK demo application**

When you define configuration variables, use the format of the placeholder values provided in the configuration files\.

1. Confirm that the [iOS SDK for FreeRTOS Bluetooth devices](freertos-ble-mobile.md#freertos-ble-ios) is installed\.

1. Issue the following command from `amazon-freertos-ble-ios-sdk/Example/AmazonFreeRTOSDemo/`:

   ```
   $ pod install
   ```

1. Open the `amazon-freertos-ble-ios-sdk/Example/AmazonFreeRTOSDemo/AmazonFreeRTOSDemo.xcworkspace` project with Xcode, and change the signing developer account to your account\.

1. Create an AWS IoT policy in your region \(if you haven't already\)\.
**Note**  
This policy is different from the IAM policy created for the Amazon Cognito authenticated identity\.

   1. Open the [AWS IoT console](https://console.aws.amazon.com/iot/)\.

   1. In the navigation pane, choose **Secure**, choose **Policies**, and then choose **Create**\. Enter a name to identify your policy\. In the **Add statements** section, choose **Advanced mode**\. Copy and paste the following JSON into the policy editor window\. Replace *aws\-region* and *aws\-account* with your AWS Region and account ID\.

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": "iot:Connect",
                  "Resource":"arn:aws:iot:region:account-id:*"
              }, 
              {
                  "Effect": "Allow",
                  "Action": "iot:Publish",
                  "Resource": "arn:aws:iot:region:account-id:*"
              },
              {
                   "Effect": "Allow",
                   "Action": "iot:Subscribe",
                   "Resource": "arn:aws:iot:region:account-id:*"
              },
              {
                   "Effect": "Allow",
                   "Action": "iot:Receive",
                   "Resource": "arn:aws:iot:region:account-id:*"
              }
          ]
      }
      ```

   1. Choose **Create**\.

1. Open `amazon-freertos-ble-ios-sdk/Example/AmazonFreeRTOSDemo/AmazonFreeRTOSDemo/Amazon/AmazonConstants.swift`, and redefine the following variables:
   + `region`: Your AWS Region\.
   + `iotPolicyName`: Your AWS IoT policy name\.
   + `mqttCustomTopic`: The MQTT topic that you want to publish to\.

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

1. Confirm that the [Android SDK for FreeRTOS Bluetooth devices](freertos-ble-mobile.md#freertos-ble-android) is installed\.

1. Create an AWS IoT policy in your region \(if you haven't already\)\.
**Note**  
This policy is different from the IAM policy created for the Amazon Cognito authenticated identity\.

   1. Open the [AWS IoT console](https://console.aws.amazon.com/iot/)\.

   1. In the navigation pane, choose **Secure**, choose **Policies**, and then choose **Create**\. Enter a name to identify your policy\. In the **Add statements** section, choose **Advanced mode**\. Copy and paste the following JSON into the policy editor window\. Replace *aws\-region* and *aws\-account* with your AWS Region and account ID\.

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": "iot:Connect",
                  "Resource":"arn:aws:iot:region:account-id:*"
              }, 
              {
                  "Effect": "Allow",
                  "Action": "iot:Publish",
                  "Resource": "arn:aws:iot:region:account-id:*"
              },
              {
                   "Effect": "Allow",
                   "Action": "iot:Subscribe",
                   "Resource": "arn:aws:iot:region:account-id:*"
              },
              {
                   "Effect": "Allow",
                   "Action": "iot:Receive",
                   "Resource": "arn:aws:iot:region:account-id:*"
              }
          ]
      }
      ```

   1. Choose **Create**\.

1. Open [ https://github\.com/aws/amazon\-freertos\-ble\-android\-sdk/blob/master/app/src/main/java/software/amazon/freertos/demo/DemoConstants\.java](https://github.com/aws/amazon-freertos-ble-android-sdk/blob/master/app/src/main/java/software/amazon/freertos/demo/DemoConstants.java)  and redefine the following variables:
   + `AWS_IOT_POLICY_NAME`: Your AWS IoT policy name\.
   + `AWS_IOT_REGION`: Your AWS Region\.

1. Open  [ https://github\.com/aws/amazon\-freertos\-ble\-android\-sdk/blob/master/app/src/main/res/raw/awsconfiguration\.json](https://github.com/aws/amazon-freertos-ble-android-sdk/blob/master/app/src/main/res/raw/awsconfiguration.json)\. 

   Under `CognitoIdentity`, redefine the following variables:
   + `PoolId`: Your Amazon Cognito identity pool ID\.
   + `Region`: Your AWS Region\.

   Under `CognitoUserPool`, redefine the following variables:
   + `PoolId`: Your Amazon Cognito user pool ID\.
   + `AppClientId`: Your app client ID\.
   + `AppClientSecret`: Your app client secret\.
   + `Region`: Your AWS Region\.

**To discover and establish secure connections with your microcontroller over Bluetooth Low Energy**

1. In order to pair your microcontroller and mobile device securely \(step 6\), you need a serial terminal emulator with both input and output capabilities \(such as TeraTerm\)\. Configure the terminal to connect to your board by a serial connection as instructed in [Installing a terminal emulator](gsg-troubleshooting.md#uart-term)\.

1. Run the Bluetooth Low Energy demo project on your microcontroller\.

1. Run the Bluetooth Low Energy Mobile SDK demo application on your mobile device\.

   To start the demo application in the Android SDK from the command line, run the following command:

   ```
   $ ./gradlew installDebug
   ```

1. Confirm that your microcontroller appears under **Devices** on the Bluetooth Low Energy Mobile SDK demo app\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list1.png)
**Note**  
All devices with FreeRTOS and the device information service \(`freertos/.../device_information`\) that are in range appear in the list\.

1. Choose your microcontroller from the list of devices\. The application establishes a connection with the board, and a green line appears next to the connected device\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list2.png)

   You can disconnect from your microcontroller by dragging the line to the left\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list3.png)

1. If prompted, pair your microcontroller and mobile device\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/pairing-board.png)  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/pairing-mobile.png)

   If the code for numeric comparison is the same on both devices, pair the devices\.

**Note**  
The Bluetooth Low Energy Mobile SDK demo application uses Amazon Cognito for user authentication\. Make sure that you have set up a Amazon Cognito user and identity pools, and that you have attached IAM policies to authenticated identities\.

## MQTT over Bluetooth Low Energy<a name="ble-demo-mqtt"></a>

In the MQTT over Bluetooth Low Energy demo, your microcontroller publishes messages to the AWS Cloud through an MQTT proxy\.

**To subscribe to a demo MQTT topic**

1. Sign in to the AWS IoT console\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter ***thing\-name*/example/topic1**, and then choose **Subscribe to topic**\.

If you use Bluetooth Low Energy to pair the microcontroller with your mobile device, the MQTT messages are routed through the Bluetooth Low Energy Mobile SDK demo application on your mobile device\.

**To enable the demo over Bluetooth Low Energy**

1. Open `vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h`, and define `CONFIG_MQTT_BLE_TRANSPORT_DEMO_ENABLED`\.

1. Open `demos/include/aws_clientcredential.h`, and configure `clientcredentialMQTT_BROKER_ENDPOINT` with the AWS IoT broker endpoint\. Configure `clientcredentialIOT_THING_NAME` with the thing name for the BLE micro controller device\. The AWS IoT broker endpoint can be obtained from the AWS IoT console by choosing **Settings** in the left navigation pane, or through the CLI by running the command: `aws iot describe-endpoint --endpoint-type=iot:Data-ATS`\.
**Note**  
The AWS IoT broker endpoint and thing name must both be in the same region where the cognito identity and user pool are configured\.

**To run the demo**

1. Build and run the demo project on your microcontroller\.

1. Make sure that you have paired your board and your mobile device using the [FreeRTOS Bluetooth Low Energy Mobile SDK demo application](#ble-sdk-app)\.

1. From the **Devices** list in the demo mobile app, choose your microcontroller, and then choose **MQTT Proxy** to open the MQTT proxy settings\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list4.png)

1. After you enable the MQTT proxy, MQTT messages appear on the `thing-name/example/topic1` topic, and data is printed to the UART terminal\.

## Wi\-Fi provisioning<a name="ble-demo-wifi"></a>

Wi\-Fi Provisioning is a FreeRTOS Bluetooth Low Energy service that allows you to securely send Wi\-Fi network credentials from a mobile device to a microcontroller over Bluetooth Low Energy\. The source code for the Wi\-Fi Provisioning service can be found at `freertos/.../wifi_provisioning`\.

**Note**  
The Wi\-Fi Provisioning demo is currently supported on the Espressif ESP32\-DevKitC\.

**To enable the demo**

1. Enable the Wi\-Fi Provisioning service\. Open `vendors/vendor/boards/board/aws_demos/config_files/iot_ble_config.h`, and set `#define IOT_BLE_ENABLE_WIFI_PROVISIONING` to `1` \(where the *vendor* is the name of the vendor and the *board* is the name of the board that you are using to run the demos\)\.
**Note**  
The Wi\-Fi Provisioning service is disabled by default\.

1. Configure the [Network Manager](#ble-demo-network-manager) to enable both Bluetooth Low Energy and Wi\-Fi\.

**To run the demo**

1. Build and run the demo project on your microcontroller\.

1. Make sure that you have paired your microcontroller and your mobile device using the [FreeRTOS Bluetooth Low Energy Mobile SDK demo application](#ble-sdk-app)\.

1. From the **Devices** list in the demo mobile app, choose your microcontroller, and then choose **Network Config** to open the network configuration settings\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list4.png)

1. After you choose **Network Config** for your board, the microcontroller sends a list of the networks in the vicinity to the mobile device\. Available Wi\-Fi networks appear in a list under **Scanned Networks**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-network-config1.png)

   From the **Scanned Networks** list, choose your network, and then enter the SSID and password, if required\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-wifi-password.png)

   The microcontroller connects to and saves the network\. The network appears under **Saved Networks**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-network-config2.png)

You can save several networks in the demo mobile app\. When you restart the application and demo, the microcontroller connects to the first available saved network, starting from the top of the **Saved Networks** list\. 

To change the network priority order or delete networks, on the **Network Configuration** page, choose **Editing Mode**\. To change the network priority order, choose the right side of the network that you want to reprioritize, and drag the network up or down\. To delete a network, choose the red button on the left side of the network that you want to delete\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-network-editing.png)

## Generic Attributes Server<a name="ble-demo-server"></a>

In this example, a demo Generic Attributes \(GATT\) Server application on your microcontroller sends a simple counter value to the [FreeRTOS Bluetooth Low Energy Mobile SDK demo application](#ble-sdk-app)\.

Using the Bluetooth Low Energy Mobile SDKs, you can create your own GATT client for a mobile device that connects to the GATT server on your microcontroller and runs in parallel with the demo mobile application\.

**To enable the demo**

1. Enable the Bluetooth Low Energy GATT demo\. In `vendors/vendor/boards/board/aws_demos/config_files/iot_ble_config.h` \(where the *vendor* is the name of the vendor and the *board* is the name of the board that you are using to run the demos\), add `#define IOT_BLE_ADD_CUSTOM_SERVICES ( 1 )` to the list of define statements\.
**Note**  
The Bluetooth Low Energy GATT demo is disabled by default\.

1. Open `freertos/vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_CORE_MQTT_MUTUAL_AUTH_DEMO_ENABLED`, and define  `CONFIG_BLE_GATT_SERVER_DEMO_ENABLED`\.

**To run the demo**

1. Build and run the demo project on your microcontroller\.

1. Make sure that you have paired your board and your mobile device using the [FreeRTOS Bluetooth Low Energy Mobile SDK demo application](#ble-sdk-app)\.

1. From the **Devices** list in the app, choose your board, and then choose **MQTT Proxy** to open the MQTT proxy options\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-device-list4.png)

1. Return to the **Devices** list, choose your board, and then choose **Custom GATT MQTT** to open the custom GATT service options\.

1. Choose **Start Counter** to start publishing data to the ***your\-thing\-name*/example/topic** MQTT topic\.

   After you enable the MQTT proxy, Hello World and incrementing counter messages appear on the `your-thing-name/example/topic` topic\.