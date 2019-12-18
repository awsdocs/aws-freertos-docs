# Mobile SDKs for Amazon FreeRTOS Bluetooth Devices<a name="freertos-ble-mobile"></a>

You can use the Mobile SDKs for Amazon FreeRTOS Bluetooth Devices to create mobile applications that interact with your microcontroller over Bluetooth Low Energy\. The Mobile SDKs can also communicate with AWS services, using Amazon Cognito for user authentication\.

## Android SDK for Amazon FreeRTOS Bluetooth Devices<a name="freertos-ble-android"></a>

Use the Android SDK for Amazon FreeRTOS Bluetooth Devices to build Android mobile applications that interact with your microcontroller over Bluetooth Low Energy\. The SDK is available on [GitHub](https://github.com/aws/amazon-freertos-ble-android-sdk/)\. 

**To install the Android SDK for Amazon FreeRTOS Bluetooth Devices**

1. Download the SDK from [GitHub](https://github.com/aws/amazon-freertos-ble-android-sdk/)\.

1. Open Android Studio, and import the `amazon-freertos-ble-android-sdk/amazonfreertossdk/` directory into your app project\. The [ Android Studio User Guide](https://developer.android.com/studio/intro) has more information on using Android Studio\. 

1. In your app's `gradle` file, add the following dependencies:

   ```
   dependencies {
       implementation project(":amazonfreertossdk")
   }
   ```

1. In your app's `AndroidManifest.xml` file, add the following permissions:

   ```
   <uses-permission android:name="android.permission.BLUETOOTH"/>
       <!-- initiate device discovery and manipulate bluetooth settings -->
   <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
       <!-- allow scan Bluetooth Low Energy -->
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
   
       <!-- AWS Mobile SDK -->
   <uses-permission android:name="android.permission.INTERNET" />
   <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
   ```

For information about setting up and running the demo mobile application that is included with the SDK, see [Prerequisites](ble-demo.md#ble-demo-prereqs) and [Amazon FreeRTOS Bluetooth Low Energy Mobile SDK Demo Application](ble-demo.md#ble-sdk-app)\.

## iOS SDK for Amazon FreeRTOS Bluetooth Devices<a name="freertos-ble-ios"></a>

Use the iOS SDK for Amazon FreeRTOS Bluetooth Devices to build iOS mobile applications that interact with your microcontroller over Bluetooth Low Energy\. The SDK is available on [GitHub](https://github.com/aws/amazon-freertos-ble-ios-sdk/)\.

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
   $ pod 'AmazonFreeRTOS', :git => 'https://github.com/aws/amazon-freertos-ble-ios-sdk.git'
   ```

For information about setting up and running the demo mobile application that is included with the SDK, see [Prerequisites](ble-demo.md#ble-demo-prereqs) and [Amazon FreeRTOS Bluetooth Low Energy Mobile SDK Demo Application](ble-demo.md#ble-sdk-app)\.