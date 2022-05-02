# Porting the corePKCS11 library<a name="afr-porting-pkcs"></a>

The corePKCS11 library contains a software\-based mock implementation of the PKCS \#11 interface \(API\) that uses the cryptographic functionality provided by Mbed TLS\. Storing private keys in general\-purpose flash memory can be convenient in evaluation and rapid prototyping scenarios\. In production scenarios, to reduce the threats of data theft and device duplication, we recommend that you use dedicated cryptographic hardware\. Cryptographic hardware includes components with features that prevent cryptographic secret keys from being exported\. 

To use dedicated cryptographic hardware with FreeRTOS, port the PKCS \#11 API for the hardware you are using\. Generally, vendors for secure cryptoprocessors, such as Trusted Platform Module \(TPM\), Hardware Security Module \(HSM\), Secure Element, or any other type of secure hardware enclave, distribute a PKCS \#11 implementation with the hardware\. You can add the library to CMake and your IDE project, compile it and run the PKCS \#11 test suite\.

This section describes how to use the FreeRTOS corePKCS11 library as the basis of your own port of the PKCS \#11 API\. Only a subset of the PKCS \#11 standard is implemented, with a focus on operations involving asymmetric keys, random number generation, and hashing\. PKCS \#11 API calls are made by the TLS helper interface in order to perform TLS client authentication during `SOCKETS_Connect`\. PKCS \#11 API calls are also made by our one\-time developer provisioning workflow to import a TLS client certificate and private key for authentication to the AWS IoT MQTT broker\. Those two use cases, provisioning and TLS client authentication, require implementation of only a small subset of the PKCS \#11 interface standard\.

For information about the FreeRTOS corePKCS11 library, see [FreeRTOS corePKCS11 Library](https://docs.aws.amazon.com/freertos/latest/userguide/security-pkcs.html) in the *FreeRTOS User Guide*\.

## Prerequisites<a name="porting-prereqs-pkcs"></a>

To port the corePKCS11 library, you need the following:
+ An IDE project or `CMakeLists.txt` list file that includes vendor\-supplied drivers that are suitable for sensitive data\.

  For information about setting up a test project, see [Setting Up Your FreeRTOS Source Code for Porting](porting-set-up-project.md)\.
+ A validated configuration of the FreeRTOS kernel\.

  For information about configuring the FreeRTOS kernel for your platform, see [Configuring a FreeRTOS kernel port](afr-porting-kernel.md)\.

## Porting<a name="porting-steps-pkcs"></a>

**To port the corePKCS11 library**

1. Port the PKCS \#11 API functions implemented by corePKCS11\.

   The PKCS \#11 API is dependent on the implementation of cryptographic primitives, such as SHA256 hashing and Elliptic Curve Digital Signature Algorithm \(ECDSA\) signing\.

   The FreeRTOS implementation of PKCS \#11 uses the cryptographic primitives implemented in the mbedTLS library\. FreeRTOS includes a port for mbedTLS\. If your target hardware offloads crypto to a separate module, or if you want to use a software implementation of the cryptographic primitives other than mbedTLS, you need to modify the existing PKCS \#11 implementation\.

1. Port the corePKCS11 Platform Abstraction Layer \(PAL\) for device\-specific certificate and key storage\.

   If you decide to use the FreeRTOS implementation of PKCS \#11, little customization is required to read and write cryptographic objects to non\-volatile memory \(NVM\), such as onboard flash memory\.

   Cryptographic objects should be stored in a section of NVM that is not initialized and is not erased on device reprogramming\. Users of the corePKCS11 library should be able to provision devices with credentials, and then reprogram the device with a new application that accesses these credentials through the corePKCS11 interface\.

   corePKCS11 PAL ports must provide a location to store:
   + The device client certificate\. 
   + The device client private key\.
   + The device client public key\.
   + A trusted root CA\.
   + A code\-verification public key \(or a certificate that contains the code\-verification public key\) for secure bootloader and over\-the\-air \(OTA\) updates\.
   + A Just\-In\-Time provisioning certificate\.

    `freertos/vendors/vendor/boards/board/ports/pkcs11/core_pkcs11_pal.c` contains empty definitions for the PAL functions\. You must provide ports for, at minimum, the functions listed in this table:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-pkcs.html)

1. Add support for a cryptographically random entropy source to your port:
   + If your ports use the mbedTLS library for underlying cryptographic and TLS support, and your device has a true random number generator \(TRNG\):

     1. Implement the [ mbedtls\_hardware\_poll\(\)](https://github.com/ARMmbed/mbedtls/blob/master/library/entropy_poll.h#L49-L60) function to seed the deterministic random bit generator \(DRBG\) that mbedTLS uses to produce a cryptographically random bit stream\. The `mbedtls_hardware_poll()` function is located in `freertos/vendors/vendor/boards/board/ports/pkcs11/core_pkcs11_pal.c`\. 
   + If your ports use the mbedTLS library for underlying cryptographic and TLS support, but your device does not have a TRNG:

     1. Make a copy of `freertos/libraries/3rdparty/mbedtls/include/mbedtls/config.h`, and in that copy, uncomment `MBEDTLS_ENTROPY_NV_SEED`, and comment out `MBEDTLS_ENTROPY_HARDWARE_ALT`\.

        Save the modified version of `config.h` to `freertos/vendors/vendor/boards/board/aws_tests/config_files/config.h`\. Do not overwrite the original file\.

     1. Implement the functions `mbedtls_nv_seed_poll()`, `nv_seed_read_func()`, and `nv_seed_write_func()`\.

        For information about implementing these functions, see the comments in the [ mbedtls/library/mbedtls/entropy\_poll\.h](https://github.com/ARMmbed/mbedtls/blob/master/library/entropy_poll.h#L62-L70) and [ mbedtls/include/mbedtls/platform\.h](https://github.com/ARMmbed/mbedtls/blob/master/include/mbedtls/platform.h#L315-L355) mbedTLS header files\.
**Important**  
A seed file with an NIST\-approved entropy source must be supplied to the device at manufacturing time\.
**Note**  
If you are interested in the FreeRTOS Qualification Program, please read our requirements for [RNG](https://docs.aws.amazon.com/freertos/latest/qualificationguide/afq-checklist.html)\.

   For more information about NIST\-approved DRBGs and entropy sources, see the following NIST publications:
   + [ Recommendation for Random Number Generation Using Deterministic Random Bit Generators](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-90Ar1.pdf)
   + [ Recommendation for Random Bit Generator \(RBG\) Constructions](https://csrc.nist.gov/csrc/media/publications/sp/800-90c/draft/documents/draft-sp800-90c.pdf)

## Testing<a name="porting-testing-pkcs"></a>

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

### Setting up the IDE test project<a name="testing-ide-pkcs"></a>

If you are using an IDE for porting and testing, you need to add some source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Do not create duplicate copies of source files\.

**To set up the corePKCS11 library in the IDE project**

1. Add the source file `freertos/vendors/vendor/boards/board/ports/pkcs11/core_pkcs11_pal.c` to the `aws_tests` IDE project\.

1. Add all of the files in the `freertos/libraries/abstractions/pkcs11` directory and its subdirectories to the `aws_tests` IDE project\.

1. Add all of the files in the `freertos/libraries/freertos_plus/standard/pkcs11` directory and its subdirectories to the `aws_tests` IDE project\. These files implement wrappers for commonly grouped PKCS \#11 function sets\.

1. Add the source file `freertos/libraries/freertos_plus/standard/crypto/src/iot_crypto.c` to the `aws_tests` IDE project\. This file implements the CRYPTO abstraction wrapper for mbedTLS\.

1. Add all of the source and header files from `freertos/libraries/3rdparty/mbedtls` and its subdirectories to the `aws_tests` IDE project\.

1. Add `freertos/libraries/3rdparty/mbedtls/include` and `freertos/libraries/abstractions/pkcs11` to the compiler's include path\.

### Configuring the `CMakeLists.txt` file<a name="testing-cmake-pkcs"></a>

If you're using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in `CMakeLists.txt`, follow the instructions in [FreeRTOS portable layers](cmake-template.md#cmake-portable)\.

The `CMakeLists.txt` template list file under `freertos/vendors/vendor/boards/board/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

See the following example portable layer target definition for the corePKCS11 library that uses the mbedTLS\-based software implementation of PKCS \#11 and supplies a port\-specific corePKCS11 PAL file\.

```
# PKCS11
afr_mcu_port(pkcs11_implementation DEPENDS AFR::pkcs11_mbedtls)
target_sources(
    AFR::pkcs11_implementation::mcu_port
    INTERFACE
    "${afr_ports_dir}/pkcs11/core_pkcs11_pal.c"
)
```

### Setting up your local testing environment<a name="testing-local-pkcs"></a>

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the PKCS \#11 tests**

1. If you have ported the Secure Sockets library, open `freertos/libraries/freertos_plus/standard/utils/src/iot_system_init.c`, and in the function `SYSTEM_Init()`, uncomment calls to `SOCKETS_Init()`\.

1. Open `freertos/vendors/vendor/boards/board/aws_tests/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_PKCS11_ENABLED` macro to `1` to enable the PKCS \#11 test\.

### Running the tests<a name="testing-run-pkcs"></a>

**To execute the PKCS \#11 tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-pkcs-tests1.png)

   `...`  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/porting-pkcs-tests2.png)

   Testing is complete when all tests pass\.

## Validation<a name="pkcs-validation"></a>

To officially qualify a device for FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you finish porting the corePKCS11 library to your device, you can start porting the TLS library\. See [Porting the TLS library](afr-porting-tls.md) for instructions\.