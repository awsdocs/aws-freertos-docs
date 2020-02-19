# Migrating from Version 1\.4\.x to Version 201906\.00 \(and newer\)<a name="porting-migration"></a>

For a list of FreeRTOS releases, see [https://github\.com/aws/amazon\-freertos/releases](https://github.com/aws/amazon-freertos/releases)\.

## Migrating Applications<a name="w3aac13b5"></a>

FreeRTOS version 201906\.00 introduced some changes to the FreeRTOS directory structure that break project files built on previous versions of FreeRTOS\. In order for applications built on previous versions of FreeRTOS to work with FreeRTOS version 201906\.00 or newer, you must move the application code to new projects and include the 201906\.00 header files in the application\.

Version 201906\.00 introduced new APIs for the MQTT, Device Shadow, and Device Defender libraries\. The APIs for previous versions of these libraries are accessible through header files of the 201906\.00 implementations of these libraries, making FreeRTOS version 201906\.00 backward\-compatible\.

**Note**  
If you are migrating from previous versions to version 201906\.00 or newer, you might need to reconfigure your `iot_config.h` and [https://www.freertos.org/a00110.html](https://www.freertos.org/a00110.html) files to accommodate the new library implementations\. For information about global configuration settings, see the [ Global Configuration File Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/main/global_library_config.html#IOT_CONFIG_FILE)\.

## Migrating Ports<a name="w3aac13b7"></a>

If you have ported a version of FreeRTOS released prior to the 201906\.00 release, you need to migrate your ported code to be compatible with versions 201906\.00 and later\. For information about porting, see the [FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)\.

### FreeRTOS Code Directory Structure<a name="w3aac13b7b5"></a>

In versions released prior to 201906\.00, the `<freertos>/lib/third_party/mcu_vendor/vendor` folder held the vendor\-ported code\. One or more project files under the same *vendor* folder compiled the code\. In versions 201906\.00 and later, vendor code is located under the `<freertos>/vendors/vendor` folder, and project files are located under the `<freertos>/projects/vendor` folder\.

**Note**  
The code for ports did not change with version 201906\.00\. Only the location of the code changed\. Move any existing ports to the new folder structure\.

### CMake Build System<a name="w3aac13b7b7"></a>

Version 201906\.00 introduced support for using CMake to generate project files\. For information about using CMake with FreeRTOS, see [Building FreeRTOS with CMake](building-cmake.md)\.

A CMake list file is required for qualification\. For information about creating a CMake list file, see [Creating a CMake List File](porting-cmake-setup.md)\.

### Migrating the Wi\-Fi Library Port<a name="migrate-wifi"></a>

The FreeRTOS Wi\-Fi library features four new APIs to add, remove, and retrieve a Wi\-Fi network, and to receive notifications for Wi\-Fi network state changes\. All of these new APIs are optional, and are intended to support Wi\-Fi credentials provisioning over Bluetooth Low Energy\. If your device does not support Bluetooth Low Energy, you do not need to implement these APIs\.
+ `WIFI_NetworkAdd`

  ```
  WIFIReturnCode_t WIFI_NetworkAdd( 
      const WIFINetworkProfile_t * const pxNetworkProfile,
      uint16_t * pusIndex );
  ```
+ `WIFI_NetworkGet`

  ```
  WIFIReturnCode_t WIFI_NetworkGet( 
  WIFINetworkProfile_t * pxNetworkProfile,
      uint16_t usIndex );
  ```
+ `WIFI_NetworkDelete`

  ```
  WIFIReturnCode_t WIFI_NetworkDelete( uint16_t usIndex );
  ```
+ `WIFI_RegisterNetworkStateChangeEventCallback`

  ```
  WIFIReturnCode_t WIFI_RegisterNetworkStateChangeEventCallback( 
      IotNetworkStateChangeEventCallback_t xCallback );
  ```

With the following `typedef` statements:

```
typedef void ( *IotNetworkStateChangeEventCallback_t ) ( 
    uint32_t ulNetworkType, 
    AwsIotNetworkState_t xState );
```

```
typedef enum AwsIotNetworkState
{
    eNetworkStateUnknown = 0,
    eNetworkStateDisabled,
    eNetworkStateEnabled
} AwsIotNetworkState_t;
```

For information about porting the Wi\-Fi library, see [Porting the Wi\-Fi Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-wifi.html) in the FreeRTOS Porting Guide\.