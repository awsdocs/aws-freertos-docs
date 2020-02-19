# Configuring the MQTT Library for Testing<a name="afr-porting-mqtt"></a>

Devices on the edge can use the MQTT protocol to communicate with the AWS Cloud\. AWS IoT hosts an MQTT broker that sends and receives messages to and from connected devices at the edge\.

The MQTT library implements the MQTT protocol for devices running FreeRTOS\. The MQTT library does not need to be ported, but your device's test project must pass all MQTT tests for qualification\. For more information, see [FreeRTOS MQTT Library](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-lib-cloud-mqtt.html.html) in the FreeRTOS User Guide\.

## Prerequisites<a name="testing-prereqs-mqtt"></a>

To set up the FreeRTOS MQTT library tests, you need the following:
+ A port of the TLS library\.

  For information about porting the TLS library to your platform, see [Porting the TLS Library](afr-porting-tls.md)\.

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

## Setting Up the IDE Test Project<a name="testing-ide-mqtt"></a>

**To set up the MQTT library in the IDE project**
+ Add all of the test source files in `<freertos>/libraries/c_sdk/standard/mqtt` and its subdirectories to the `aws_tests` IDE project\.

## Configuring the `CMakeLists.txt` File<a name="testing-cmake-mqtt"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in `CMakeLists.txt`, follow the instructions in [FreeRTOS Portable Layers](cmake-template.md#cmake-portable)\.

The `CMakeLists.txt` template list file under `<freertos>/vendors/<vendor>/boards/<board>/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

## Setting Up Your Local Testing Environment<a name="testing-local-mqtt"></a>

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the MQTT tests**
+ To enable the MQTT tests, open `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_MQTTv4_ENABLED ` macro to `1`\.

## Running the Tests<a name="testing-run-mqtt"></a>

**To execute the MQTT tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\. If all tests pass, then testing is complete\.

## Validation<a name="w3aac11c33c19"></a>

To officially qualify a device for FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [ Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you finish setting up the FreeRTOS MQTT library for your device, you can start porting the OTA agent library\. See [Porting the OTA Library](afr-porting-ota.md) for instructions\.

If your device does not support OTA functionality, you can start porting the Bluetooth Low Energy library\. See [Porting the Bluetooth Low Energy Library](afr-porting-ble.md) for instructions\.

If your device does not support OTA and Bluetooth Low Energy functionality, then you are finished porting and can start the FreeRTOS qualification process\. See the [FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/) for more information\.