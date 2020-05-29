# Porting the Wi\-Fi library<a name="afr-porting-wifi"></a>

The FreeRTOS Wi\-Fi library interfaces with vendor\-supplied Wi\-Fi drivers\. For more information about the FreeRTOS Wi\-Fi library, see [FreeRTOS Wi\-Fi Library](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-wifi.html) in the FreeRTOS User Guide\.

If your device does not support Wi\-Fi networking, you can skip porting the FreeRTOS Wi\-Fi library and start [Porting a TCP/IP stack](afr-porting-tcp.md)\.

**Note**  
For qualification, your device must connect to the AWS Cloud\. If your device does not support Wi\-Fi, you can use an ethernet connection instead\. A port of the FreeRTOS Wi\-Fi library is not necessarily required\.

## Prerequisites<a name="porting-prereqs-wifi"></a>

To port the Wi\-Fi library, you need the following:
+ An IDE project or `CMakeLists.txt` list file that includes the vendor\-supplied Wi\-Fi drivers\.

  For information about setting up a test project, see [Setting Up Your FreeRTOS Source Code for Porting](porting-set-up-project.md)\.
+ A validated configuration of the FreeRTOS kernel\.

  For information about configuring the FreeRTOS kernel for your platform, see [Configuring a FreeRTOS kernel port](afr-porting-kernel.md)\.
+ Two wireless access points\.

## Porting<a name="porting-steps-wifi"></a>

`freertos/vendors/vendor/boards/board/ports/wifi/iot_wifi.c` contains empty definitions of a set of Wi\-Fi management functions\. Use the vendor\-supplied Wi\-Fi driver library to implement at least the set of functions listed in the following table\.


| Function | Description | 
| --- | --- | 
| WIFI\_On | Turns on Wi\-Fi module and initializes the drivers\. | 
| WIFI\_ConnectAP | Connects to a Wi\-Fi access point \(AP\)\. | 
| WIFI\_Disconnect | Disconnects from an AP\. | 
| WIFI\_Scan | Performs a Wi\-Fi network scan\. | 
| WIFI\_GetIP | Retrieves the Wi\-Fi interface’s IP address\. | 
| WIFI\_GetMAC | Retrieves the Wi\-Fi interface’s MAC address\. | 
| WIFI\_GetHostIP | Retrieves the host IP address from a hostname using DNS\. | 

`freertos/libraries/abstractions/wifi/include/⁠iot_wifi.h` provides the information required to implement these functions\.

## Testing<a name="porting-testing-wifi"></a>

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

### Setting up the IDE test project<a name="testing-ide-wifi"></a>

If you are using an IDE for porting and testing, you need to add some source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Do not create duplicate copies of source files\.

**To set up the Wi\-Fi library in the IDE project**

1. Add the source file `freertos/vendors/vendor/boards/board/ports/wifi/iot_wifi.c` to your `aws_tests` IDE project\.

1. Add the source file `aws_test_wifi.c` to the `aws_tests` IDE project\.

### Configuring the `CMakeLists.txt` file<a name="testing-cmake-wifi"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in `CMakeLists.txt`, follow the instructions in [FreeRTOS portable layers](cmake-template.md#cmake-portable)\.

The `CMakeLists.txt` template list file under `freertos/vendors/vendor/boards/board/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

See below for an example portable layer target definition for the Wi\-Fi library\.

```
# WiFi
afr_mcu_port(wifi)
target_sources(
    AFR::wifi::mcu_port
    INTERFACE "freertos/vendors/vendor/boards/board/ports/wifi/iot_wifi.c"
)
```

### Setting up your local testing environment<a name="testing-local-wifi"></a>

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the Wi\-Fi tests**

1. Open `freertos/vendors/vendor/boards/board/aws_tests/application_code/main.c`, and delete the `#if 0` and `#endif` compiler directives in the function definitions of `vApplicationDaemonTaskStartupHook(void)` and `prvWifiConnect(void)`\.

1. If you have not ported the Secure Sockets library, open `freertos/libraries/freertos_plus/standard/utils/src/iot_system_init.c`, and comment out the line that calls `SOCKETS_Init()`\. When you reach the [Porting the Secure Sockets library](afr-porting-ss.md) section, you will be instructed to uncomment this initialization function call\.

1. Open `freertos/tests/include/aws_clientcredential.h`, and set the macros in the following table for the first AP\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-wifi.html)

1. Open `freertos/libraries/abstractions/wifi/test/aws_test_wifi.h`, and set the macros in the following table for the second AP\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-wifi.html)

1. To enable the Wi\-Fi tests, open `freertos/vendors/vendor/boards/board/aws_tests/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_WIFI_ENABLED` to `1`\. 
**Important**  
The following tests require a port of the Secure Sockets library and a running echo server:  
`WiFiConnectionLoop`
`WiFiIsConnected`
`WiFiConnectMultipleAP`
You won't be able to pass these tests until you port the Secure Sockets library and start an echo server\. After you port the Secure Sockets library and start an echo server, rerun the Wi\-Fi tests to be sure that all tests pass\. For information about porting the Secure Sockets library, see [Porting the Secure Sockets library](afr-porting-ss.md)\. For information about setting up an echo server, see [Setting up an echo server](afr-echo-server.md)\.

### Running the tests<a name="testing-run-wifi"></a>

**To execute the Wi\-Fi tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-wifi-tests1.png)

   `...`  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-wifi-tests2.png)

## Validation<a name="w3aac11c23c15"></a>

To officially qualify a device for FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [ Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you finish porting the FreeRTOS Wi\-Fi library to your device, you can start porting a TCP/IP stack\. See [Porting a TCP/IP stack](afr-porting-tcp.md) for instructions\.