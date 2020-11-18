# Configuring the coreMQTT library for testing<a name="afr-porting-mqtt"></a>

Devices on the edge can use the MQTT protocol to communicate with the AWS Cloud\. AWS IoT hosts an MQTT broker that sends and receives messages to and from connected devices at the edge\.

The coreMQTT library implements the MQTT protocol for devices running FreeRTOS\. The coreMQTT library doesn't need to be ported, but your device's test project must pass all MQTT tests for qualification\. For more information, see [coreMQTT Library](https://docs.aws.amazon.com/freertos/latest/userguide/coremqtt.html) in the *FreeRTOS User Guide\.*

## Prerequisites<a name="testing-prereqs-mqtt"></a>

To set up the coreMQTT library tests, you need the following:
+ A port of the TLS library\.

  For information about porting the TLS library to your platform, see [Porting the TLS library](afr-porting-tls.md)\.

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

## Setting up the IDE test project<a name="testing-ide-mqtt"></a>

**To set up the coreMQTT library in the IDE project**

1. Add all the library source files in the `freertos/libraries/coreMQTT/source` directory and its subdirectories to the `aws_tests` IDE project\.

1. Add all the source files for the network layer \(used in the tests\) in the `freertos/libraries/abstractions/transport` directory and `secure_sockets` subdirectory to the `aws_tests` IDE project\.

1. Add a `core_mqtt_config.h` file, required for building the coreMQTT library, to the config files directory at `freertos/vendors/vendor/boards/board/aws_tests/config_files`\. 

1. Add the test source file at `freertos/tests/integration_test/core_mqtt_system.c` to the `aws_tests` IDE project\.

## Setting up your local testing environment<a name="testing-local-mqtt"></a>

After you set up the library in the IDE project, you need to configure other files for testing\.

**To configure the source and header files for the MQTT tests**
+ To enable the MQTT tests, open `freertos/vendors/vendor-name/boards/board-name/aws_tests/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_CORE_MQTT_AWS_IOT_ENABLED` macro to `1`\.

## Running the tests<a name="testing-run-mqtt"></a>

**To execute the MQTT tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\. If all tests pass, then testing is complete\.

## Validation<a name="validation-mqtt"></a>

To officially qualify a device for FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [ Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you set up the coreMQTT library for your device, you can start porting the OTA agent library\. See [Porting the OTA library](afr-porting-ota.md) for instructions\.

If your device doesn't support OTA functionality, you can start porting the Bluetooth Low Energy library\. See [Porting the Bluetooth Low Energy library](afr-porting-ble.md) for instructions\.

If your device doesn't support OTA and Bluetooth Low Energy functionality, then you are finished porting and can start the FreeRTOS qualification process\. See the [FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/) for more information\.