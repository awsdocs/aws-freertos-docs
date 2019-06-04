# Setting Up the MQTT Library for Testing<a name="afr-porting-mqtt"></a>

Devices on the edge can use the MQTT protocol to communicate with the AWS Cloud\. AWS IoT hosts an MQTT broker that sends and receives messages to and from connected devices at the edge\.

The MQTT library implements the MQTT protocol for devices running Amazon FreeRTOS\. The MQTT library does not need to be ported, but your device's test project must pass all MQTT tests for qualification\. For more information, see [Amazon FreeRTOS MQTT Library](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-lib-cloud-mqtt.html.html) in the Amazon FreeRTOS User Guide\.

## Prerequisites<a name="testing-prereqs-mqtt"></a>

To set up the Amazon FreeRTOS MQTT library tests, you need the following:
+ A port of the TLS library\.

  For information about porting the TLS library to your platform, see [Porting the TLS Library](afr-porting-tls.md)\.

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

## Setting Up the IDE Test Project<a name="testing-ide-mqtt"></a>

**To set up the MQTT library in the IDE project**

1. In your IDE, under `aws_tests/lib/aws`, create virtual folders named `mqtt` and `bufferpool`\.

1. Add all of the source files in `<amazon-freertos>/lib/mqtt` to the `aws_tests/lib/aws/mqtt` virtual folder\.

1. Add all of the source files in `<amazon-freertos>/lib/bufferpool` to the `aws_tests/lib/aws/bufferpool` virtual folder\.

1. Under `aws_tests/application_code/common_tests`, create a virtual folder named `mqtt`\.

1. Add all of the source files in `<amazon-freertos>/tests/common/mqtt` to the virtual folder `aws_tests/application_code/common_tests/mqtt`\.

## Setting Up Your Local Testing Environment<a name="testing-local-mqtt"></a>

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the Wi\-Fi tests**

1. Open `<amazon-freertos>/lib/utils/aws_system_init.c.`, and uncomment all of the initialization functions called from `SYSTEM_Init()`\.

1. To enable the MQTT tests, open `<amazon-freertos>/tests/<vendor>/<board>/common/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_MQTT_ENABLED ` macro to `1`\.

## Running the Tests<a name="testing-run-mqtt"></a>

**To execute the MQTT tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-mqtt-tests1.png)

   If all tests pass, then testing is complete\.

## Validation<a name="w3aac11c29c19"></a>

To officially qualify a device for Amazon FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the Amazon FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you finish setting up the Amazon FreeRTOS MQTT library for your device, you can start porting the OTA agent library\. See [Porting the OTA Library](afr-porting-ota.md) for instructions\.

If your device does not support OTA functionality, you can start porting the Bluetooth Low Energy library\. See [Porting the BLE Library](afr-porting-ble.md) for instructions\.

If your device does not support OTA and BLE functionality, then you are finished porting and can start the Amazon FreeRTOS qualification process\. See the [Amazon FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/) for more information\.