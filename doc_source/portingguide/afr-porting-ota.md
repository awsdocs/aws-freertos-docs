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

`<amazon-freertos>/lib/ota/portable/<vendor>/<board>/aws_ota_pal.c` contains empty definitions of a set of platform abstraction layer \(PAL\) functions\. Implement at least the set of functions listed in this table\.


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

1. In your IDE, create a virtual folder named `ota` under `aws_tests/lib/aws`\.

1. Add the source file `<amazon-freertos>/lib/ota/portable/<vendor>/<board>/aws_ota_pal.c` to the virtual folder `aws_tests/lib/aws/ota`\.

1. Create a virtual folder named `ota` under `aws_tests/application_code/common_tests`\.

1. Add the following test source files to the virtual folder `aws_tests/application_code/common_tests/ota`:
   + `<amazon-freertos>/tests/common/ota/aws_test_cbor.c`
   + `<amazon-freertos>/tests/common/ota/aws_test_ota_agent.c`
   + `<amazon-freertos>/tests/common/aws_test_pal.c`
   + `<amazon-freertos>/demos/common/ota/aws_ota_update_demo.c`

There are two sets of tests for the OTA library port: [OTA Agent and OTA PAL Tests](#testing-agent-pal) and [OTA End\-to\-End Tests](#testing-e2e)\.

### OTA Agent and OTA PAL Tests<a name="testing-agent-pal"></a>

#### Setting Up Your Local Testing Environment<a name="testing-local-ota-agent"></a>

**To configure the source and header files for the OTA agent and OTA PAL tests**

1. Open `<amazon-freertos>/tests/<vendor>/<board>/common/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_OTA_AGENT_ENABLED` and `testrunnerFULL_OTA_PAL_ENABLED` macros to `1` to enable the agent and PAL tests\. 

1. Choose a signing certificate for your device from `<amazon-freertos>/tests/ota/test_files`\. The certificate are used in OTA tests for verification\. 

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

### OTA End\-to\-End Tests<a name="testing-e2e"></a>

**To set up and run the end\-to\-end OTA tests**

1. To enable the end\-to\-end OTA tests, open `<amazon-freertos>/tests/<vendor>/<board>/common/config_files/aws_test_runner_config.h`, and set the `testrunner_OTA_END_TO_END_ENABLED` macro to `1`\.

1. Follow the setup instructions in the README file \(`<amazon-freertos>/tools/ota_e2e_test/README.md`\)\.

1. Make sure that running the agent and PAL tests did not modify the `aws_demo_runner.c`, `aws_clientcredential.h`, `aws_clientcredential_keys.h`, `aws_application_version.h`, or `aws_ota_codesigner_certificate.h` header files\.

1. To run the OTA end\-to\-end test script, follow the example in the README file \(`<amazon-freertos>/tools/ota_e2e_test/README.md`\)\.

## Validation<a name="w3aac11c31c25"></a>

To officially qualify a device for Amazon FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the Amazon FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you have ported the Amazon FreeRTOS OTA library and the bootloader demo, you can start porting the Bluetooth Low Energy library\. For instructions, see [Porting the BLE Library](afr-porting-ble.md)\.

If your device does not support BLE functionality, then you are finished and can start the Amazon FreeRTOS qualification process\. For more information, see the [Amazon FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\.