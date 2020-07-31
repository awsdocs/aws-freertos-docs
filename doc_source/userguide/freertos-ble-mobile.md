# Mobile SDKs for FreeRTOS Bluetooth devices<a name="freertos-ble-mobile"></a>

You can use the Mobile SDKs for FreeRTOS Bluetooth Devices to create mobile applications that interact with your microcontroller over Bluetooth Low Energy\. The Mobile SDKs can also communicate with AWS services, using Amazon Cognito for user authentication\.

## Android SDK for FreeRTOS Bluetooth devices<a name="freertos-ble-android"></a>

Use the Android SDK for FreeRTOS Bluetooth Devices to build Android mobile applications that interact with your microcontroller over Bluetooth Low Energy\. The SDK is available on [GitHub](https://github.com/aws/amazon-freertos-ble-android-sdk/)\. 

To install the Android SDK for FreeRTOS Bluetooth devices, follow the instructions for "Setting up the SDK" in the project's [ README\.md](https://github.com/aws/amazon-freertos-ble-android-sdk/blob/master/README.md) file\.

For information about setting up and running the demo mobile application that is included with the SDK, see [Prerequisites](ble-demo.md#ble-demo-prereqs) and [FreeRTOS Bluetooth Low Energy Mobile SDK demo application](ble-demo.md#ble-sdk-app)\.

## iOS SDK for FreeRTOS Bluetooth devices<a name="freertos-ble-ios"></a>

Use the iOS SDK for FreeRTOS Bluetooth Devices to build iOS mobile applications that interact with your microcontroller over Bluetooth Low Energy\. The SDK is available on [GitHub](https://github.com/aws/amazon-freertos-ble-ios-sdk/)\.

**To install the iOS SDK**

1. Install [CocoaPods](http://cocoapods.org/):

   ```
   $ gem install cocoapods
   $ pod setup
   ```
**Note**  
You might need to use `sudo` to install CocoaPods\.

1. Install the SDK with CocoaPods \(add this to your podfile\):

   ```
   $ pod 'FreeRTOS', :git => 'https://github.com/aws/amazon-freertos-ble-ios-sdk.git'
   ```

For information about setting up and running the demo mobile application that is included with the SDK, see [Prerequisites](ble-demo.md#ble-demo-prereqs) and [FreeRTOS Bluetooth Low Energy Mobile SDK demo application](ble-demo.md#ble-sdk-app)\.