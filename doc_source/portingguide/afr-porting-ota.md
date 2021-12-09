# Porting the AWS IoT over\-the\-air update library<a name="afr-porting-ota"></a>

With FreeRTOS over\-the\-air \(OTA\) updates, you can do the following:
+ Deploy new firmware images to a single device, a group of devices, or your entire fleet\.
+ Deploy firmware to devices as they are added to groups, are reset, or are reprovisioned\.
+ Verify the authenticity and integrity of new firmware after it has been deployed to devices\.
+ Monitor the progress of a deployment\.
+ Debug a failed deployment\.
+ Digitally sign firmware using Code Signing for AWS IoT\.

For more information, see [FreeRTOS Over\-the\-Air Updates](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ota-dev.html) in the *FreeRTOS User Guide* along with the [ AWS IoT Over\-the\-air Update Documentation](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/index.html)\.

You can use the OTA update library to integrate OTA functionality into your FreeRTOS applications\. For more information, see [FreeRTOS OTA update Library](https://docs.aws.amazon.com/freertos/latest/userguide/ota-update-library.html) in the *FreeRTOS User Guide*\.

FreeRTOS devices must enforce cryptographic code\-signing verification on the OTA firmware images that they receive\. We recommend the following algorithms:
+ Elliptic\-Curve Digital Signature Algorithm \(ECDSA\)
+ NIST P256 curve
+ SHA\-256 hash

**Note**  
A port of the FreeRTOS OTA update library is currently not required for device qualification\.

## Prerequisites<a name="porting-prereqs-ota"></a>
+ Complete the instructions in [Setting Up Your FreeRTOS Source Code for Porting](porting-set-up-project.md)\.
+ Create a port of the TLS library\.

  For information, see [Porting the TLS library](afr-porting-tls.md)\.
+ Create a bootloader that can support OTA updates\.

  For more information about porting a bootloader demo application, see [Porting the bootloader demo](#afr-porting-bootloader)\.

## Platform porting<a name="porting-steps-ota"></a>

You must provide an implementation of the OTA portable abstraction layer \(PAL\) to port the OTA library to a new device\. The `freertos/vendors/vendor/boards/board/ports/ota_pal_for_aws/` directory contains the `ota_pal.h` and `ota_pal.c` template files\. The `ota_pal.c` file contains empty definitions of a set of platform abstraction layer \(PAL\) functions\. At a minimum, you must implement the set of functions listed in the following table\. For an example, see the [ Windows Simulator OTA PAL implementation](https://github.com/aws/amazon-freertos/blob/main/vendors/pc/boards/windows/ports/ota_pal_for_aws/ota_pal.c)\.


| Function name | Description | 
| --- | --- | 
| `otaPal_Abort` | Stops an OTA update\. | 
| `otaPal_CreateFileForRx` | Creates a file to store the received data chunks\. | 
| `otaPal_CloseFile` | Closes the specified file\. This might authenticate the file if you use storage that implements cryptographic protection\. | 
| `otaPal_WriteBlock` | Writes a block of data to the specified file at the given offset\. On success, the function returns the number of bytes written\. Otherwise, the function returns a negative error code\. The block size will always be a power of two and so will be aligned\. For more information, see the [ OTA library configuration documentation](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_config.html)\. | 
| `otaPal_ActivateNewImage` | Activates or launches the new firmware image\. For some ports, if the device is programmatically reset synchronously, this function might not return\. | 
| `otaPal_SetPlatformImageState` | Does what is required by the platform to accept or reject the most recent OTA firmware image \(or bundle\)\. To determine how to implement this function, see the documentation for your board \(platform\) details and architecture\. | 
| `otaPal_GetPlatformImageState` | Gets the state of the OTA update image\. | 

Implement the functions in this table if your device has built\-in support for them\.


| Function name | Description | 
| --- | --- | 
| `otaPal_CheckFileSignature` | Verifies the signature of the specified file\. | 
| `otaPal_ReadAndAssumeCertificate` | Reads the specified signer certificate from the file system and returns it to the caller\. | 
| `otaPal_ResetDevice` | Resets the device\. | 

**Note**  
Make sure that you have a bootloader that can support OTA updates\. For instructions about how to port the bootloader demo application provided with FreeRTOS or create your IoT device bootloader, see [IoT device bootloader](#afr-bootloader)\.

### Additional Information<a name="porting-steps-ota-additional"></a>

The portable abstraction layer \(PAL\) enables the OTA update library to be independent of any specific hardware platform\. The OTA library depends on interfaces to remain independent of the operating system and MQTT protocol implementation\. If your application performs OTA over HTTP, then there is also an interface for the HTTP protocol implementation\. The example applications in FreeRTOS use ports for these interfaces that are common to all of the platforms\. Because of this, the PAL is the only interface that must be ported before a new platform can use the OTA library\. Information on these interfaces can be found in the [ FreeRTOS documentation for the OTA update library](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_design.html#ota_design_library)\. 

## IoT device bootloader<a name="afr-bootloader"></a>

### Porting the bootloader demo<a name="afr-porting-bootloader"></a>

The [v202012\.00 release](https://github.com/aws/amazon-freertos/tree/202012.00) of FreeRTOS includes a demo bootloader application for the Microchip Curiosity PIC32MZEF platform\. For more information, see [Demo Bootloader for the Microchip Curiosity PIC32MZEF](https://docs.aws.amazon.com/freertos/latest/userguide/microchip-bootloader.html) in the *FreeRTOS User Guide*\. You can port this demo to other platforms\. If you don't port the demo to your platform, you can use your own bootloader application\. For more information about how to write your own secure bootloader application, see [Threat modeling for the IoT device bootloader](#afr-threat-model-for-bootloader)\.

### Threat modeling for the IoT device bootloader<a name="afr-threat-model-for-bootloader"></a>

#### Background<a name="afr-threat-model-for-bootloader-background"></a>

As a working definition, the embedded IoT devices referenced by this threat model are microcontroller\-based products that interact with cloud services\. They may be deployed in consumer, commercial, or industrial settings\. IoT devices may gather data about a user, a patient, a machine, or an environment, and may control anything from light bulbs and door locks to factory machinery\.

Threat modeling is an approach to security from the point of view of a hypothetical adversary\. By considering the adversary's goals and methods, a list of threats is created\. Threats are attacks against a resource or asset performed by an adversary\. The list is prioritized and used to identify or create mitigations\. When choosing mitigations, the cost of implementing and maintaining them should be balanced with the real security value they provide\. There are multiple [threat model methodologies](https://en.wikipedia.org/wiki/Threat_model)\. Each is capable of supporting the development of a secure and successful IoT product\.

FreeRTOS offers OTA \("over\-the\-air"\) software updates to IoT devices\. The update facility combines cloud services with on\-device software libraries and a partner\-supplied bootloader\. This threat model focuses specifically on threats against the bootloader\.

**Bootloader use cases**
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
  + Upon boot, the bootloader verifies, as previously described\. When verification fails with no previous image available, the bootloader enters a fail safe OTA only mode\.
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

#### Further threat modeling<a name="afr-threat-model-for-bootloader-further"></a>

This threat model considers only the bootloader\. Further threat modeling could improve overall security\. A recommended method is to list the adversary's goals, the assets targeted by those goals, and points of entry to the assets\. A list of threats can be made by considering attacks on the points of entry to gain control of the assets\. The following are lists of examples of goals, assets, and entry points for an IoT device\. These lists are not exhaustive, and are intended to spur further thought\.

**Adversary's goals**
+ Extort money 
+ Ruin reputations 
+ Falsify data 
+ Divert resources 
+ Remotely spy on a target 
+ Gain physical access to a site 
+ Wreak havoc
+ Instill terror 

**Key assets**
+ Private keys 
+ Client certificate 
+ CA root certificates 
+ Security credentials and tokens 
+ Customer's personally identifiable information 
+ Implementations of trade secrets 
+ Sensor data 
+ Cloud analytics data store 
+ Cloud infrastructure 

**Entry points**
+ DHCP response 
+ DNS response 
+ MQTT over TLS 
+ HTTPS response 
+ OTA software image 
+ Other, as dictated by application, for example, USB 
+ Physical access to bus 
+ Decapped IC 

## Testing<a name="porting-testing-ota"></a>

If you're using an IDE to build test projects, you need to set up your library port in the IDE project\.

### Setting up the IDE test project<a name="testing-ide-ota"></a>

If you're using an IDE for porting and testing, you must add some source files to the IDE test project before you can test your ported code\. This includes code for the OTA library, PAL port, and OTA test code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Don't create duplicate copies of source files\.

In this procedure, you add files for the OTA library, OTA port, and OTA test code to your test IDE project\.

**To set up your IDE test project**

1. Add the OTA library code to your project: 

   1. In a text editor, open the `freertos/libraries/ota_for_aws/otaFilePaths.cmake` file to see various CMake variables related to building the OTA library\.

   1. Add the files listed in the variables `OTA_SOURCES`, `OTA_OS_FREERTOS_SOURCES`, `OTA_HTTP_SOURCES` \(for the OTA over HTTP demo\), and `OTA_MQTT_SOURCES` to your IDE project\.

   1. Add the include paths listed in the variables `OTA_INCLUDE_PUBLIC_DIRS`, `OTA_INCLUDE_PRIVATE_DIRS`, and `OTA_INCLUDE_OS_FREERTOS_DIRS` to your IDE project\.

1. Add the OTA PAL code to the project: 

   1. Add `freertos/vendors/vendor/boards/board/ports/ota_pal_for_aws/` to your include path\.

   1. Add the `ota_pal.c` and `ota_pal.h` files located in the `freertos/vendors/vendor/boards/board/ports/ota_pal_for_aws/` directory to your project\.

   1. Add any platform specific files or include paths to your project\.

1. Add the OTA test source file to the project: 

   1. Add all source files located in `freertos\tests\integration_test\ota_pal` and its subdirectories to your project\.

   1. Add `freertos\tests\integration_test\ota_pal` and `freertos\tests\integration_test\ota_pal\test_files` to the include path of your test project\.

1. Add the OTA related config files to the test project: 

   1. Add the `ota_config.h` file \(for the aws\_demo project\) and the `aws_test_ota_config.h` file \(for the aws\_test project\) to the config files directory at `freertos/vendors/vendor/boards/board/aws_tests/config_files`\. 

     You can find examples in the `freertos/vendors/vendor/boards/board/aws_tests/config_files` directory\. 

For an example of an IDE test project for OTA, see the [ GitHub](https://github.com/aws/amazon-freertos/tree/main/projects/pc/windows/visual_studio/aws_tests) website\.

### Configuring the `CMakeLists.txt` file<a name="testing-cmake-ota"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in `CMakeLists.txt`, follow the instructions in [FreeRTOS portable layers](cmake-template.md#cmake-portable)\.

The `CMakeLists.txt` template list file under `freertos/vendors/vendor/boards/board/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

The following is an example of a portable layer target definition for the OTA library\.

```
# Over-the-air Updates
afr_mcu_port(ota)
target_sources(
    AFR::ota::mcu_port
    INTERFACE
        "${afr_ports_dir}/ota_pal_for_aws/ota_pal.c"
        "${afr_ports_dir}/ota_pal_for_aws/ota_pal.h"
        # Add platform specific files that are required to build ota_pal.c.
)

target_include_directories(
    AFR::ota::mcu_port
    INTERFACE "${afr_ports_dir}/ota_pal_for_aws"
    # Add platform specific include paths that are required to build ota_pal.c.
)

target_link_libraries(
    AFR::ota::mcu_port
    INTERFACE
        AFR::crypto
        AFR::ota
        # Add platform specific target dependencies that are required to build ota_pal.c.
)

# The qualification tests for the OTA PAL port requires this include path to run.
if(AFR_ENABLE_TESTS)
    target_include_directories(
        AFR::ota::mcu_port
        INTERFACE "${PROJECT_SOURCE_DIR}/tests/integration_test/ota_pal"
    )
endif()
```

You can find an example `CMakeLists.txt` file for the Windows Simulator platform on [ GitHub](https://github.com/aws/amazon-freertos/blob/main/vendors/pc/boards/windows/CMakeLists.txt)\.

### OTA PAL tests<a name="testing-update-pal"></a>

#### Setting up the local testing environment<a name="testing-local-ota-update"></a>

**To configure the source and header files for the OTA PAL tests**

1. In a text editor, open the `freertos/vendors/vendor/boards/board/aws_tests/config_files/aws_test_runner_config.h` file, and set the `testrunnerFULL_OTA_PAL_ENABLED` macro to **1** to enable the PAL tests\.

1. Open the `freertos/vendors/vendor/boards/board/aws_tests/config_files/aws_test_ota_config.h` file, and configure it for your platform\. You can find the specific details on what configurations exist and how to set them in the configuration file\.

1. Choose a signing certificate for your device from `freertos/tests/integration_test/ota_pal/test_files`\. The certificates are used in the OTA tests for verification\. 

   Three types of signing certificates are available in the test code:
   + RSA/SHA1
   + RSA/SHA256
   + ECDSA/SHA256

   RSA/SHA1 and RSA/SHA256 are available for existing platforms only\. ECDSA/SHA256 is recommended for OTA updates\. If you have a different scheme, [contact the FreeRTOS engineering team](https://freertos.org/RTOS-contact-and-support.html)\.

#### Running the tests<a name="testing-run-ota-update"></a>

**To run the OTA PAL tests**

1. Build the test project, and then flash it to your device to run the tests\.

1. Check the test results in the UART console\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/ex_tests_passing.png)

Your platform passes the OTA PAL tests if there are 25 tests that ran with 0 failures and 0 ignores\. At this point your platform can be used to run the OTA demo if you have configured your demo project\.

## Validation<a name="w3aac11c39c27"></a>

To officially qualify a device for FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you have ported the FreeRTOS OTA library and the bootloader demo, you can start porting the Bluetooth Low Energy library\. For instructions, see [Porting the Bluetooth Low Energy library](afr-porting-ble.md)\.

If your device does not support Bluetooth Low Energy functionality, then you are finished and can start the FreeRTOS qualification process\. For more information, see the [ FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\.