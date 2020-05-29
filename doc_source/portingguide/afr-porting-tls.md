# Porting the TLS library<a name="afr-porting-tls"></a>

For Transport Layer Security \(TLS\) authentication, FreeRTOS uses either mbedTLS or an off\-chip TLS implementation, such as those found on some network co\-processors\. FreeRTOS includes a port of mbedTLS\. If you use mbedTLS for TLS, TLS porting is not required\. To allow different TLS implementations, third\-party TLS libraries are accessed through a TLS abstraction layer\.

**Note**  
No matter which TLS implementation is used by your device's port of FreeRTOS, the port must pass the qualification tests for TLS\. Qualification is based on results from AWS IoT Device Tester\.

To prepare your platform for testing TLS, you need to configure your device in the AWS Cloud, and you need certificate and key provisioning on the device\.

## Prerequisites<a name="porting-prereqs-tls"></a>

To port the FreeRTOS TLS library, you need the following:
+ A port of the FreeRTOS Secure Sockets library\.

  For information about porting the Secure Sockets library to your platform, see [Porting the Secure Sockets library](afr-porting-ss.md)\.
+ A port of the FreeRTOS PKCS \#11 library\.

  For information about porting the PKCS \#11 library to your platform, see [Porting the PKCS \#11 library](afr-porting-pkcs.md)\.
+ An AWS account\.

  For information about setting up an AWS account, see [How do I create and activate a new Amazon Web Services account?](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/) on the AWS Knowledge Center\.
+ OpenSSL\.

  You can download a version of OpenSSL for Windows from [Shining Light](https://slproweb.com/products/Win32OpenSSL.html)\. For a Linux source code download, see [OpenSSL\.org](https://www.openssl.org/source/)\. You can also use a package manager to install OpenSSL for Linux and macOS\.

## Porting<a name="porting-steps-tls"></a>

If your target hardware offloads TLS functionality to a separate network chip, you need to implement the TLS abstraction layer functions in the following table\.


| Function | Description | 
| --- | --- | 
| TLS\_Init | Initialize the TLS context\. | 
| TLS\_Connect | Negotiate TLS and connect to the server\. | 
| TLS\_Recv | Read the requested number of bytes from the TLS connection\. | 
| TLS\_Send | Write the requested number of bytes to the TLS connection\. | 
| TLS\_Cleanup | Free resources consumed by the TLS context\. | 

`iot_tls.h` contains the information required to implement these functions\. Save the file in which you implement the functions as `iot_tls.c`\.

## Testing<a name="porting-testing-tls"></a>

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

### Setting up the IDE test project<a name="testing-ide-tls"></a>

If you are using an IDE for porting and testing, you need to add some source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Do not create duplicate copies of source files\.

**To set up the TLS library in the IDE project**

1. Add `iot_tls.c` to the `aws_tests` IDE project\.

1. Add the source file `iot_test_tls.c` to the virtual folder `aws_tests/application_code/common_tests/tls`\. This file includes the TLS tests\.

### Configuring the `CMakeLists.txt` file<a name="testing-cmake-tls"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in `CMakeLists.txt`, follow the instructions in [FreeRTOS portable layers](cmake-template.md#cmake-portable)\.

The `CMakeLists.txt` template list file under `freertos/vendors/vendor/boards/board/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

### Setting up your local testing environment<a name="testing-local-tls"></a>

There are five separate tests for the TLS port, one for each type of authentication supported by the FreeRTOS TLS library:
+ `TLS_ConnectRSA()`
+ `TLS_ConnectEC()`
+ `TLS_ConnectMalformedCert()`
+ `TLS_ConnectBYOCCredentials()`
+ `TLS_ConnectUntrustedCert()`

To run these tests, your board must use the MQTT protocol to communicate with the AWS Cloud\. AWS IoT hosts an MQTT broker that sends and receives messages to and from connected devices at the edge\. The AWS IoT MQTT broker accepts mutually authenticated TLS connections only\.

Follow the instructions in [Connecting your device to AWS IoT](testing-connect-iot.md) to connect your device to AWS IoT\.

Each TLS test requires a different certificate/key combination, formatted and defined in either `freertos/tests/include/aws_clientcredential_keys.h` or `freertos/libraries/freertos_plus/standard/tls/test/iot_test_tls.h`\.

Follow the instructions in [Setting up certificates and keys for the TLS tests](tls-certkey-setup.md) to obtain the certificates and keys that you need for testing\.

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the TLS tests**

1. To enable the TLS tests, open `freertos/vendors/vendor/boards/board/aws_tests/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_TLS_ENABLED` macro to `1`\.

1. Open `freertos/libraries/freertos_plus/standard/utils/src/iot_system_init.c`, and in the function `SYSTEM_Init()`, make sure that the line that calls `SOCKETS_Init()` is uncommented\.

### Running the tests<a name="testing-run-tls"></a>

**To execute the TLS tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-tls-tests1.png)

   If all tests pass, then testing is complete\.

**Important**  
After you have ported the TLS library and tested your ports, you must run the Secure Socket tests that depend on TLS functionality\. For more information, see [Testing](afr-porting-ss.md#porting-testing-ss) in the Secure Sockets porting documentation\.

## Validation<a name="afr-porting-tls-validation"></a>

To officially qualify a device for FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [ Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you finish porting the FreeRTOS TLS library to your device, you can start setting up the MQTT library for testing\. See [Configuring the MQTT library for testing](afr-porting-mqtt.md) for instructions\.