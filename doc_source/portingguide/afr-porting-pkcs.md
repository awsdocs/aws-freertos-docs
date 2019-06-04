# Porting the PKCS \#11 Library<a name="afr-porting-pkcs"></a>

Amazon FreeRTOS uses the open standard PKCS \#11 “CryptoKi” API as the abstraction layer for cryptographic operations, including:
+ Signing and verifying\.
+ Storage and enumeration of X\.509 certificates\.
+ Storage and management of cryptographic keys\.

For more information, see [PKCS \#11 Cryptographic Token Interface Base Specification](http://docs.oasis-open.org/pkcs11/pkcs11-base/v2.40/os/pkcs11-base-v2.40-os.html)\.

Storing private keys in general\-purpose flash memory can be convenient in evaluation and rapid prototyping scenarios\. In production scenarios, to reduce the threats of data theft and device duplication, we recommend that you use dedicated cryptographic hardware\. Cryptographic hardware includes components with features that prevent cryptographic secret keys from being exported\. To use dedicated cryptographic hardware with Amazon FreeRTOS, you need to port the PKCS \#11 API to the hardware\. For information about the Amazon FreeRTOS PKCS \#11 library, see [Amazon FreeRTOS PKCS \#11 Library](https://docs.aws.amazon.com/freertos/latest/userguide/security-pkcs.html) in the Amazon FreeRTOS User Guide\.

## Prerequisites<a name="porting-prereqs-pkcs"></a>

To port the PKCS \#11 library, you need the following:
+ An IDE project that includes vendor\-supplied drivers that are suitable for sensitive data\.

  For information about setting up a test project, see [Setting Up Your Amazon FreeRTOS Source Code for Porting](porting-set-up-project.md)\.
+ A validated configuration of the FreeRTOS kernel\.

  For information about configuring the FreeRTOS kernel for your platform, see [Configuring a FreeRTOS Kernel Port](afr-porting-kernel.md)\.

## Porting<a name="porting-steps-pkcs"></a>

**To port the PKCS \#11 library**

1. Port the PKCS \#11 API functions\.

   The PKCS \#11 API is dependent on the implementation of cryptographic primitives, such as SHA256 hashing and Elliptic Curve Digital Signature Algorithm \(ECDSA\) signing\.

   The Amazon FreeRTOS implementation of PKCS \#11 uses the cryptographic primitives implemented in the mbedTLS library\. Amazon FreeRTOS includes a port for mbedTLS\. If your target hardware offloads crypto to a separate module, or if you want to use a software implementation of the cryptographic primitives other than mbedTLS, you need to modify the existing PKCS \#11 port\.

1. Port the PKCS \# 11 Platform Abstraction Layer \(PAL\) for device\-specific certificate and key storage\.

   If you decide to use the Amazon FreeRTOS implementation of PKCS \#11, little customization is required to read and write cryptographic objects to non\-volatile memory \(NVM\), such as onboard flash memory\.

   Cryptographic objects should be stored in a section of NVM that is not initialized and is not erased on device reprogramming\. Users of the PKCS \#11 library should be able to provision devices with credentials, and then reprogram the device with a new application that accesses these credentials through the PKCS \#11 interface\.

   PKCS \#11 PAL ports must provide a location to store:
   + The device client certificate\. 
   + The device client private key\.
   + The device client public key\.
   + A trusted root CA\.
   + A code\-verification public key \(or a certificate that contains the code\-verification public key\) for secure bootloader and over\-the\-air \(OTA\) updates\.
   + A Just\-In\-Time provisioning certificate\.

    `<amazon-freertos>/lib/pkcs11/portable/<vendor>/<board>/aws_pkcs11_pal.c` contains empty definitions for the PAL functions\. You must provide ports for, at minimum, the functions listed in this table:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-pkcs.html)

1. Add support for a cryptographically random entropy source to your port\.

   If your ports use the mbedTLS library for underlying cryptographic and TLS support, and your device has a true random number generator \(TRNG\), implement the [https://github.com/ARMmbed/mbedtls/blob/master/include/mbedtls/entropy_poll.h#L92](https://github.com/ARMmbed/mbedtls/blob/master/include/mbedtls/entropy_poll.h#L92) function to seed the deterministic random bit generator \(DRBG\) that mbedTLS uses to produce a cryptographically random bit stream\. The `mbedtls_hardware_poll()` function is located in `<amazon-freertos>/lib/pkcs11/portable/<vendor>/<board>/aws_pkcs11_pal.c`\.

   If your ports use the mbedTLS library for underlying cryptographic and TLS support, but your device does not have a TRNG, do the following:

   1. In the mbedTLS [https://github.com/ARMmbed/mbedtls/blob/master/include/mbedtls/config.h](https://github.com/ARMmbed/mbedtls/blob/master/include/mbedtls/config.h), uncomment `MBEDTLS_ENTROPY_NV_SEED`, and comment out `MBEDTLS_ENTROPY_HARDWARE_ALT`\.

   1. Implement the functions `mbedtls_nv_seed_poll()`, `nv_seed_read_func()`, and `nv_seed_write_func()`\.

      For information about implementing these functions, see the comments in the [mbedtls/include/mbedtls/entropy\_poll\.h](https://github.com/ARMmbed/mbedtls/blob/master/include/mbedtls/entropy_poll.h#L102) and [mbedtls/include/mbedtls/config\.h](https://github.com/ARMmbed/mbedtls/blob/master/include/mbedtls/config.h#L1121) mbedTLS header files\.
**Important**  
A seed file with an NIST\-approved entropy source must be supplied to the device at manufacturing time\.

   For more information about NIST\-approved DRBGs and entropy sources, see the following NIST publications:
   + [Recommendation for Random Number Generation Using Deterministic Random Bit Generators](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-90Ar1.pdf)
   + [Recommendation for Random Bit Generator \(RBG\) Constructions](https://csrc.nist.gov/csrc/media/publications/sp/800-90c/draft/documents/draft-sp800-90c.pdf)

## Testing<a name="porting-testing-pkcs"></a>

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

### Setting Up the IDE Test Project<a name="testing-ide-pkcs"></a>

If you are using an IDE for porting and testing, you need to add some source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Do not create duplicate copies of source files\.

**To set up the PKCS \#11 library in the IDE project**

1. In your IDE, create a virtual folder named `pkcs11` under `aws_tests/lib/aws`\.

1. Add the source file `<amazon-freertos>/lib/pkcs11/portable/<vendor>/<board>/aws_pkcs11_pal.c` to the virtual folder `aws_tests/lib/aws/pkcs11`\.

1. Create a virtual folder named `pkcs11` under `aws_tests/lib/third_party`\.

1. Add the PKCS \#11 library headers files from `<amazon-freertos>/lib/third_party/pkcs11` to the virtual folder `aws_tests/lib/third_party/pkcs11`\.

1. Create a virtual folder named `pkcs11` under `aws_tests/application_code/common_tests`\.

1. Add the source file `<amazon-freertos>/tests/common/pkcs11/aws_test_pkcs11.c` to the virtual folder `aws_tests/application_code/common_tests/pkcs11`\. This file includes the PKCS \# 11 tests\.

1. Add the source file `<amazon-freertos>/lib/pkcs11/mbedtls/aws_pkcs11_mbedtls.c` to the `aws_tests/lib/pkcs11` virtual folder\. This file implements PKCS \#11 for mbedTLS\.

1. Create a virtual folder named `crypto` under `aws_tests/lib`\.

1. Import the source file `<amazon-freertos>lib/crypto/aws_crypto.c` to the `aws_tests/lib/crypto` virtual folder\. This file implements the CRYPTO abstraction wrapper for mbedTLS\.

1. Create a virtual folder named `mbedtls` under `aws_tests/lib/third_party`, and then under `aws_tests/lib/third_party`, create virtual folders named `source` and `include`\.

1. Add all of the source files from `<amazon-freertos>/lib/third_party/mbedtls/library` to the `aws_tests/lib/third_party/mbedtls/source` virtual folder\.

1. Add all of the header files from `<amazon-freertos>/lib/third_party/mbedtls/include/mbedtls` to the `aws_tests/lib/third_party/mbedtls/include` virtual folder\.

1. Add `<amazon-freertos>/lib/third_party/mbedtls/include` and `<amazon-freertos>/lib/third_party/pkcs11` to the compiler’s include path\.

### Setting Up Your Local Testing Environment<a name="testing-local-pkcs"></a>

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the PKCS \#11 tests**

1. Open `<amazon-freertos>/lib/utils/aws_system_init.c`, and in the function `SYSTEM_Init()`, comment out the lines that call `BUFFERPOOL_Init()` and `MQTT_AGENT_Init()`, if you have not done so already\. Bufferpool and the MQTT agent are not used in this library's porting tests\. When you reach the [Setting Up the MQTT Library for Testing](afr-porting-mqtt.md) section, you will be instructed to uncomment these initialization function calls for testing the MQTT library\.

   Only uncomment calls to `SOCKETS_Init()` if you have ported the Secure Sockets library\.

1. Open `<amazon-freertos>/tests/<vendor>/<board>/common/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_PKCS11_ENABLED` macro to `1` to enable the PKCS \#11 test\.

### Running the Tests<a name="testing-run-pkcs"></a>

**To execute the PKCS \#11 tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-pkcs-tests1.png)

   `...`  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-pkcs-tests2.png)

   Testing is complete when all tests pass\.

## Validation<a name="w3aac11c25c17"></a>

To officially qualify a device for Amazon FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the Amazon FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you finish porting the Amazon FreeRTOS PKCS \#11 library to your device, you can start porting the TLS library\. See [Porting the TLS Library](afr-porting-tls.md) for instructions\.