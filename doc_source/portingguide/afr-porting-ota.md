# Porting the OTA Library<a name="afr-porting-ota"></a>

With Amazon FreeRTOS over\-the\-air \(OTA\) updates, you can do the following:
+ Deploy new firmware images to a single device, a group of devices, or your entire fleet\.
+ Deploy firmware to devices as they are added to groups, are reset, or are reprovisioned\.
+ Verify the authenticity and integrity of new firmware after it has been deployed to devices\.
+ Monitor the progress of a deployment\.
+ Debug a failed deployment\.
+ Digitally sign firmware using Code Signing for AWS IoT\.

For more information, see [Amazon FreeRTOS Over\-the\-Air Updates](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ota-dev.html) in the Amazon FreeRTOS User Guide\.

You can use the OTA agent library to integrate OTA functionality into your Amazon FreeRTOS applications\. For more information, see [Amazon FreeRTOS OTA Agent Library](https://docs.aws.amazon.com/freertos/latest/userguide/ota-agent-library.html) in the Amazon FreeRTOS User Guide\.

Amazon FreeRTOS devices must enforce cryptographic code\-signing verification on the OTA firmware images that they receive\. We recommend the following algorithms:
+ Elliptic\-Curve Digital Signature Algorithm \(ECDSA\)
+ NIST P256 curve
+ SHA\-256 hash

**Note**  
A port of the Amazon FreeRTOS OTA update library is currently not required for qualification\.

## Prerequisites<a name="porting-prereqs-ota"></a>

To port the OTA agent library, you need the following:
+ A port of the TLS library\.

  For information, see [Porting the TLS Library](afr-porting-tls.md)\.
+ A bootloader that can support OTA updates\.

  For more information about porting a bootloader demo application, see [Porting the Bootloader Demo](afr-porting-bootloader.md)\.

## Porting<a name="porting-steps-ota"></a>

`<amazon-freertos>/vendors/<vendor>/boards/<board>/ports/ota/aws_ota_pal.c` contains empty definitions of a set of platform abstraction layer \(PAL\) functions\. Implement at least the set of functions listed in this table\.


| Function | Description | 
| --- | --- | 
| prvPAL\_Abort | Aborts an OTA update\. | 
| prvPAL\_CreateFileForRx | Creates a file to store the data chunks as they are received\. | 
| prvPAL\_CloseFile | Closes the specified file\. This might authenticate the file if storage that implements cryptographic protection is being used\. | 
| prvPAL\_WriteBlock | Writes a block of data to the specified file at the given offset\. On success, returns the number of bytes written\. Otherwise, a negative error code\. | 
| prvPAL\_ActivateNewImage | Activates or launches the new firmware image\. For some ports, if the device is programmatically reset synchronously, this function might not return\. | 
| prvPAL\_SetPlatformImageState | Does what is required by the platform to accept or reject the most recent OTA firmware image \(or bundle\)\. To determine how to implement this function, consult the documentation for your board \(platform\) details and architecture\. \. | 
| prvPAL\_GetPlatformImageState | Gets the state of the OTA update image\. | 

Implement the functions in this table if your device has built\-in support for them\.


| Function | Description | 
| --- | --- | 
| prvPAL\_CheckFileSignature | Verifies the signature of the specified file\. | 
| prvPAL\_ReadAndAssumeCertificate | Reads the specified signer certificate from the file system and returns it to the caller\. | 

Make sure that you have a bootloader that can support OTA updates\. For instructions on porting the bootloader demo application provided with Amazon FreeRTOS, see [Porting the Bootloader Demo](afr-porting-bootloader.md)\.

## Testing<a name="porting-testing-ota"></a>

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

### Setting Up the IDE Test Project<a name="testing-ide-ota"></a>

If you are using an IDE for porting and testing, you need to add some source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Do not create duplicate copies of source files\.

**To set up the OTA library in the IDE project**

1. Add the source file `<amazon-freertos>/vendors/<vendor>/boards/<board>/ports/ota/aws_ota_pal.c` to the `aws_tests` IDE project\.

1. Add the following test source files to the `aws_tests` IDE project:
   + `<amazon-freertos>/libraries/freertos_plus/aws/ota/test/aws_test_ota_cbor.c`
   + `<amazon-freertos>/libraries/freertos_plus/aws/ota/test/aws_test_ota_agent.c`
   + `<amazon-freertos>/libraries/freertos_plus/aws/ota/test/aws_test_ota_pal.c`
   + `/demos/ota/aws_iot_ota_update_demo.c`

### Configuring the `CMakeLists.txt` File<a name="testing-cmake-ota"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in `CMakeLists.txt`, follow the instructions in [Amazon FreeRTOS Portable Layers](cmake-template.md#cmake-portable)\.

The `CMakeLists.txt` template list file under `<amazon-freertos>/vendors/<vendor>/boards/<board>/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

See below for an example portable layer target definition for the OTA library\.

```
# OTA
afr_mcu_port(ota)
target_sources(
    AFR::ota::mcu_port
    INTERFACE "path/aws_ota_pal.c"
)
```

### OTA Agent and OTA PAL Tests<a name="testing-agent-pal"></a>

#### Setting Up Your Local Testing Environment<a name="testing-local-ota-agent"></a>

**To configure the source and header files for the OTA agent and OTA PAL tests**

1. Open `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_OTA_AGENT_ENABLED` and `testrunnerFULL_OTA_PAL_ENABLED` macros to `1` to enable the agent and PAL tests\. 

1. Choose a signing certificate for your device from `ota/test`\. The certificate are used in OTA tests for verification\. 

   Three types of signing certificates are available in the test code:
   + RSA/SHA1
   + RSA/SHA256
   + ECDSA/SHA256

   RSA/SHA1 and RSA/SHA256 are available for existing platforms only\. ECDSA/SHA256 is recommended for OTA updates\. If you have a different scheme, [contact the Amazon FreeRTOS engineering team](https://freertos.org/RTOS-contact-and-support.html)\.

#### Running the Tests<a name="testing-run-ota-agent"></a>

**To execute the OTA agent and OTA PAL tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-ota-tests1.png)

   `...`  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-ota-tests2.png)

## Validation<a name="w3aac11c31c25"></a>

To officially qualify a device for Amazon FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the Amazon FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you have ported the Amazon FreeRTOS OTA library and the bootloader demo, you can start porting the Bluetooth Low Energy library\. For instructions, see [Porting the Bluetooth Low Energy Library](afr-porting-ble.md)\.

If your device does not support Bluetooth Low Energy functionality, then you are finished and can start the Amazon FreeRTOS qualification process\. For more information, see the [Amazon FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\.