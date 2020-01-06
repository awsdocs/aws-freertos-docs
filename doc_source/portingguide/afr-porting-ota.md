# Porting the OTA Library<a name="afr-porting-ota"></a>

With Amazon FreeRTOS over\-the\-air \(OTA\) updates, you can do the following:
+ Deploy new firmware images to a single device, a group of devices, or your entire fleet\.
+ Deploy firmware to devices as they are added to groups, are reset, or are reprovisioned\.
+ Verify the authenticity and integrity of new firmware after it has been deployed to devices\.
+ Monitor the progress of a deployment\.
+ Debug a failed deployment\.
+ Digitally sign firmware using Code Signing for AWS IoT\.

For more information, see [Amazon FreeRTOS Over\-the\-Air Updates](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ota-dev.html) in the *Amazon FreeRTOS User Guide*\.

You can use the OTA agent library to integrate OTA functionality into your Amazon FreeRTOS applications\. For more information, see [Amazon FreeRTOS OTA Agent Library](https://docs.aws.amazon.com/freertos/latest/userguide/ota-agent-library.html) in the *Amazon FreeRTOS User Guide*\.

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

  For more information about porting a bootloader demo application, see [Porting the Bootloader Demo](#afr-porting-bootloader)\.

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

Make sure that you have a bootloader that can support OTA updates\. For instructions on porting the bootloader demo application provided with Amazon FreeRTOS or creating your IoT device bootloader, see [IoT Device Bootloader](#afr-bootloader)\.

## IoT Device Bootloader<a name="afr-bootloader"></a>

### Porting the Bootloader Demo<a name="afr-porting-bootloader"></a>

Amazon FreeRTOS includes a demo bootloader application for the Microchip Curiosity PIC32MZEF platform\. For more information, see [Demo Bootloader for the Microchip Curiosity PIC32MZEF](https://docs.aws.amazon.com/freertos/latest/userguide/microchip-bootloader.html) in the *Amazon FreeRTOS User Guide*\. You can port this demo to other platforms\.

If you choose not to port the demo to your platform, you can use your own bootloader application\. [Threat Modeling for the IoT Device Bootloader](#afr-threat-model-for-bootloader) discusses threat modeling to help you write your own secure bootloader application\.

### Threat Modeling for the IoT Device Bootloader<a name="afr-threat-model-for-bootloader"></a>

#### Background<a name="afr-threat-model-for-bootloader-background"></a>

As a working definition, the embedded IoT devices referenced by this threat model are microcontroller\-based products that interact with cloud services\. They may be deployed in consumer, commercial, or industrial settings\. IoT devices may gather data about a user, a patient, a machine, or an environment, and may control anything from light bulbs and door locks to factory machinery\.

Threat modeling is an approach to security from the point of view of a hypothetical adversary\. By considering the adversary's goals and methods, a list of threats is created\. Threats are attacks against a resource or asset performed by an adversary\. The list is prioritized and used to identify or create mitigations\. When choosing mitigations, the cost of implementing and maintaining them should be balanced with the real security value they provide\. There are multiple [threat model methodologies](https://en.wikipedia.org/wiki/Threat_model)\. Each is capable of supporting the development of a secure and successful IoT product\.

Amazon FreeRTOS offers OTA \("over\-the\-air"\) software updates to IoT devices\. The update facility combines cloud services with on\-device software libraries and a partner\-supplied bootloader\. This threat model focuses specifically on threats against the bootloader\.

**Bootloader Use Cases**
+ Digitally sign and encrypt firmware before deployment\.
+ Deploy new firmware images to a single device, a group of devices, or an entire fleet\.
+ Verify the authenticity and integrity of new firmware after it's deployed to devices\.
+ Devices only run unmodified software from a trusted source\.
+ Devices are resilient to faulty software received through OTA\.

**Data Flow Diagram**

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/bootloader-dataflow-diagram.png)

#### Threats<a name="afr-threat-model-for-bootloader-threats"></a>

Some attacks will have multiple mitigations; for example, a network man\-in\-the\-middle intended to deliver a malicious firmware image is mitigated by verifying trust in both the certificate offered by the TLS server and the code\-signer certificate of the new firmware image\. To maximize the security of the bootloader, any non\-bootloader mitigations are considered unreliable\. The bootloader should have intrinsic mitigations for each attack\. Having layered mitigations is known as defense\-in\-depth\.

**Threats:**
+ An attacker hijacks the device's connection to the server to deliver a malicious firmware image\.

**Mitigation example**
  + Upon boot, the bootloader verifies the cryptographic signature of the image using a known certificate\. If the verification fails, the bootloader rolls back to the previous image\.
+ An attacker exploits a buffer overflow to introduce malicious behavior to the existing firmware image stored in flash\.

**Mitigation examples**
  + Upon boot, the bootloader verifies, as previously described\. When verification fails with no previous image available, the bootloader halts\.
  + Upon boot, the bootloader verifies, as previously described\. When verification fails with no previous image available, the bootloader enters a failsafe OTA\-only mode\.
+ An attacker boots the device to a previously stored image, which is exploitable\.

**Mitigation examples**
  + Flash sectors storing the last image are erased upon successful installation and test of a new image\.
  + A fuse is burned with each successful upgrade, and each image refuses to run unless the correct number of fuses have been burned\.
+ An OTA update delivers a faulty or malicious image that bricks the device\.

**Mitigation example**
  + The bootloader starts a hardware watchdog timer that triggers rollback to the previous image\.
+ An attacker patches the bootloader to bypass image verification so the device will accept unsigned images\.

**Mitigation examples**
  + The bootloader is in ROM \(read\-only memory\), and cannot be modified\.
  + The bootloader is in OTP \(one\-time\-programmable memory\), and cannot be modified\.
  + The bootloader is in the secure zone of ARM TrustZone, and cannot be modified\.
+ An attacker replaces the verification certificate so the device will accept malicious images\.

**Mitigation examples**
  + The certificate is in a cryptographic co\-processor, and cannot be modified\.
  + The certificate is in ROM \(or OTP, or secure zone\), and cannot be modified\.

#### Further Threat Modeling<a name="afr-threat-model-for-bootloader-further"></a>

This threat model considers only the bootloader\. Further threat modeling could improve overall security\. A recommended method is to list the adversary's goals, the assets targeted by those goals, and points of entry to the assets\. A list of threats can be made by considering attacks on the points of entry to gain control of the assets\. The following are lists of examples of goals, assets, and entry points for an IoT device\. These lists are not exhaustive, and are intended to spur further thought\.

**Adversary's Goals**
+ Extort money 
+ Ruin reputations 
+ Falsify data 
+ Divert resources 
+ Remotely spy on a target 
+ Gain physical access to a site 
+ Wreak havoc
+ Instill terror 

**Key Assets**
+ Private keys 
+ Client certificate 
+ CA root certificates 
+ Security credentials and tokens 
+ Customer's personally identifiable information 
+ Implementations of trade secrets 
+ Sensor data 
+ Cloud analytics data store 
+ Cloud infrastructure 

**Entry Points**
+ DHCP response 
+ DNS response 
+ MQTT over TLS 
+ HTTPS response 
+ OTA software image 
+ Other, as dictated by application, for example, USB 
+ Physical access to bus 
+ Decapped IC 

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

The following is an example of a portable layer target definition for the OTA library\.

```
# OTA
afr_mcu_port(ota)
target_sources(
    AFR::ota::mcu_port
    INTERFACE "path/aws_ota_pal.c"
)
```

There are two sets of tests for the OTA library port: [OTA Agent and OTA PAL Tests](#testing-agent-pal) and [OTA End\-to\-End Tests](#testing-e2e)\.

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

### OTA End\-to\-End Tests<a name="testing-e2e"></a>

**To set up and run the end\-to\-end OTA tests**

1. Follow the setup instructions in the README file \(`<amazon-freertos>/tools/ota_e2e_test/README.md`\)\. 

1. Make sure that running the agent and PAL tests do not modify the `aws_clientcredential.h`, `aws_clientcredential_keys.h`, `aws_application_version.h`, or `aws_ota_codesigner_certificate.h` header files\.

1. To run the OTA end\-to\-end test script, follow the example in the README file \(`<amazon-freertos>/tools/ota_e2e_test/README.md`\)\. 

## Validation<a name="w3aac11c38c25"></a>

To officially qualify a device for Amazon FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [ Using AWS IoT Device Tester for Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the Amazon FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you have ported the Amazon FreeRTOS OTA library and the bootloader demo, you can start porting the Bluetooth Low Energy library\. For instructions, see [Porting the Bluetooth Low Energy Library](afr-porting-ble.md)\.

If your device does not support Bluetooth Low Energy functionality, then you are finished and can start the Amazon FreeRTOS qualification process\. For more information, see the [Amazon FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\.