# Porting the corePKCS11 library<a name="afr-porting-pkcs"></a>

The Public Key Cryptography Standard \#11 defines a platform\-independent API to manage and use cryptographic tokens\. [PKCS 11](https://en.wikipedia.org/wiki/PKCS_11) refers to the standard and the APIs defined by it\. The PKCS \#11 cryptographic API abstracts key storage, get/set properties for cryptographic objects, and session semantics\. It's widely used for manipulating common cryptographic objects\. Its functions allow application software to use, create, modify, and delete cryptographic objects, without exposing those objects to the application's memory\. 

FreeRTOS libraries and reference integrations use a subset of the PCKS \#11 interface standard, with a focus on the operations involving asymmetric keys, random number generation, and hashing\. The below table lists the use cases and required PKCS \#11 APIs to support\.


**Use Cases**  

| Use Case | Required PKCS \#11 API Family | 
| --- | --- | 
| All | Initialize, Finalize, Open/Close Session, GetSlotList, Login | 
| Provisioning | GenerateKeyPair, CreateObject, DestroyObject, InitToken, GetTokenInfo | 
| TLS | Random, Sign, FindObject, GetAttributeValue | 
| FreeRTOS\+TCP | Random, Digest | 
| OTA | Verify, Digest, FindObject, GetAttributeValue | 

## When to implement a complete PKCS \#11 module<a name="implemeting-pkcs"></a>

Storing private keys in general\-purpose flash memory can be convenient in evaluation and rapid prototyping scenarios\. We recommend you use dedicated cryptographic hardware to reduce the threats of data theft and device duplication in production scenarios\. Cryptographic hardware includes components with features that prevent cryptographic secret keys from being exported\. To support this, you will have to implement a subset of PKCS \#11 required to work with FreeRTOS libraries as defined in the above table\. 

## When to use FreeRTOS corePKCS11<a name="using-pkcs"></a>

The corePKCS11 library contains a software\-based implementation of the PKCS \#11 interface \(API\) that uses the cryptographic functionality provided by [Mbed TLS](https://tls.mbed.org/)\. This is provided for rapid prototyping and evaluation scenarios where the hardware does not have a dedicated cryptographic hardware\. In this case, you only have to implement corePKCS11 PAL to make the corePKCS11 software\-based implementation to work with your hardware platform\. 

## Porting corePKCS11<a name="porting-core-pkcs"></a>

You will have to have implementations to read and write cryptographic objects to non\-volatile memory \(NVM\), such as on\-board flash memory\. Cryptographic objects must be stored in a section of NVM that is not initialized and is not erased on device reprogramming\. Users of the corePKCS11 library will provision devices with credentials, and then reprogram the device with a new application that accesses these credentials through the corePKCS11 interface\. The corePKCS11 PAL ports must provide a location to store: 
+ The device client certificate
+ The device client private key
+ The device client public key
+ A trusted root CA
+ A code\-verification public key \(or a certificate that contains the code\-verification public key\) for secure boot\-loader and over\-the\-air \(OTA\) updates
+ A Just\-In\-Time provisioning certificate

Include [the header file](https://github.com/FreeRTOS/corePKCS11/blob/main/source/include/core_pkcs11_pal.h) and implement the PAL APIs defined\.


**PAL APIs**  

| Function | Description | 
| --- | --- | 
| PKCS11\_PAL\_Initialize |  Initializes the PAL layer\. Called by the corePKCS11 library at the start of its initialization sequence\.  | 
| PKCS11\_PAL\_SaveObject |  Writes data to non\-volatile storage\.  | 
| PKCS11\_PAL\_FindObject |  Uses a PKCS \#11 `CKA_LABEL` to search for a corresponding PKCS \#11 object in non\-volatile storage, and returns that object’s handle, if it exists\.  | 
| PKCS11\_PAL\_GetObjectValue |  Retrieves the value of an object, given the handle\.  | 
| PKCS11\_PAL\_GetObjectValueCleanup |  Cleanup for the `PKCS11_PAL_GetObjectValue` call\. Can be used to free memory allocated in a `PKCS11_PAL_GetObjectValue` call\.  | 

## Testing<a name="porting-testing-pkcs"></a>

If you use the FreeRTOS corePKCS11 library or implement the required subset of PKCS11 APIs, you must pass FreeRTOS PKCS11 tests\. These test if the required functions for FreeRTOS libraries perform as expected\.

This section also describes how you can locally run the FreeRTOS PKCS11 tests with the qualification tests\.

### Prerequisites<a name="porting-testing-prereqs"></a>

To set up the FreeRTOS PKCS11 tests, the following has to be implemented\.
+ A supported port of PKCS11 APIs\.
+ An implementation of FreeRTOS qualification tests [platform functions](https://github.com/FreeRTOS/Labs-FreeRTOS-Libraries-Integration-Tests/blob/dev-core-pkcs11-test/src/common/platform_function.h) which include the following:
  + `FRTest_ThreadCreate`
  + `FRTest_ThreadTimedJoin`
  + `FRTest_MemoryAlloc`
  + `FRTest_MemoryFree`

### Porting tests<a name="porting-tests-pkcs11"></a>
+ Add [ FreeRTOS\-Libraries\-Integration\-Tests](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests/tree/dev-core-pkcs11-test) as a submodule into your project\. The submodule can be placed in any directory of the project, as long as it can be built\.
+ Copy `config_template/test_execution_config_template.h` and `config_template/test_param_config_template.h` to a project location in the build path, and rename them to `test_execution_config.h` and `test_param_config.h`\. 
+ Include relevant files into the build system\. If using `CMake`, `qualification_test.cmake` and `src/pkcs11_tests.cmake` can be used to include relevant files\.
+ Implement `UNITY_OUTPUT_CHAR` so that test output logs and device logs do not interleave\.
+ Integrate the MbedTLS, which verifies the cryptoki operation result\.
+ Call `RunQualificationTest()` from the application\.

### Configuring tests<a name="configure-pkcs11-tests"></a>

The PKCS11 test suite must be configured according to the PKCS11 implementation\. The following table lists the configuration required by PKCS11 tests in the `test_param_config.h` header file\.


**PKSC11 test configurations**  

| Configuration | Description | 
| --- | --- | 
| PKCS11\_TEST\_RSA\_KEY\_SUPPORT |  The porting supports RSA key functions\.  | 
| PKCS11\_TEST\_EC\_KEY\_SUPPORT |  The porting supports EC key functions\.  | 
| PKCS11\_TEST\_IMPORT\_PRIVATE\_KEY\_SUPPORT |  The porting supports the import of the private key\. RSA and EC key import are validated in the test if the supporting key functions are enabled\.  | 
| PKCS11\_TEST\_GENERATE\_KEYPAIR\_SUPPORT |  The porting supports keypair generation\. EC keypair generation is validated in the test if the supporting key functions are enabled\.  | 
| PKCS11\_TEST\_PREPROVISIONED\_SUPPORT |  The porting has pre\-provisioned credentials\. `PKCS11_TEST_LABEL_DEVICE_PRIVATE_KEY_FOR_TLS`, `PKCS11_TEST_LABEL_DEVICE_PUBLIC_KEY_FOR_TLS` and `PKCS11_TEST_LABEL_DEVICE_CERTIFICATE_FOR_TLS`, are examples of the credentials\.  | 
| PKCS11\_TEST\_LABEL\_DEVICE\_PRIVATE\_KEY\_FOR\_TLS |  The label of the private key used in the test\.  | 
| PKCS11\_TEST\_LABEL\_DEVICE\_PUBLIC\_KEY\_FOR\_TLS |  The label of the public key used in the test\.  | 
| PKCS11\_TEST\_LABEL\_DEVICE\_CERTIFICATE\_FOR\_TLS |  The label of the certificate used in the test\.  | 
| PKCS11\_TEST\_JITP\_CODEVERIFY\_ROOT\_CERT\_SUPPORTED |  The porting supports storage for JITP\. Set this to 1 to enable the JITP `codeverify` test\.  | 
| PKCS11\_TEST\_LABEL\_CODE\_VERIFICATION\_KEY |  The label of the code verification key used in JITP `codeverify` test\.  | 
| PKCS11\_TEST\_LABEL\_JITP\_CERTIFICATE |  The label of the JITP certificate used in JITP `codeverify` test\.  | 
| PKCS11\_TEST\_LABEL\_ROOT\_CERTIFICATE |  The label of the root certificate used in JITP `codeverify` test\.  | 

FreeRTOS libraries and reference integrations must support a minimum of one key function configuration like RSA or Elliptic curve keys, and one key provisioning mechanism supported by the PKCS11 APIs\. The test must enable the following configurations: 
+ At least one of the following key function configurations:
  + PKCS11\_TEST\_RSA\_KEY\_SUPPORT
  + PKCS11\_TEST\_EC\_KEY\_SUPPORT
+ At least one of the following key provisioning configurations:
  + PKCS11\_TEST\_IMPORT\_PRIVATE\_KEY\_SUPPORT
  + PKCS11\_TEST\_GENERATE\_KEYPAIR\_SUPPORT
  + PKCS11\_TEST\_PREPROVISIONED\_SUPPORT 

The pre\-provisioned device credential test must run under the following conditions:
+ `PKCS11_TEST_PREPROVISIONED_SUPPORT` must be enabled and other provisioning mechanisms disabled\.
+ Only one key function, either `PKCS11_TEST_RSA_KEY_SUPPORT` or `PKCS11_TEST_EC_KEY_SUPPORT`, is enabled\.
+ Set up the pre\-provisioned key labels according to your key function, including `PKCS11_TEST_LABEL_DEVICE_PRIVATE_KEY_FOR_TLS`, `PKCS11_TEST_LABEL_DEVICE_PUBLIC_KEY_FOR_TLS` and `PKCS11_TEST_LABEL_DEVICE_CERTIFICATE_FOR_TLS`\. These credentials must exist before running the test\.

The test may need to run several times with different configurations, if the implementation supports pre\-provisioned credentials and other provisioning mechanisms\.

**Note**  
The objects with labels `PKCS11_TEST_LABEL_DEVICE_PRIVATE_KEY_FOR_TLS`, `PKCS11_TEST_LABEL_DEVICE_PUBLIC_KEY_FOR_TLS` and `PKCS11_TEST_LABEL_DEVICE_CERTIFICATE_FOR_TLS` are destroyed during the test if either `PKCS11_TEST_GENERATE_KEYPAIR_SUPPORT` or `PKCS11_TEST_GENERATE_KEYPAIR_SUPPORT` is enabled\.

### Running tests<a name="running-tests"></a>

This section describes how you can locally test the PKCS11 interface with the qualification tests\. Alternatively, you can also use IDT to automate the execution\. See [AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the *FreeRTOS User Guide* for details\.

The following instructions describe how to run the tests:
+ Open `test_execution_config.h` and define **CORE\_PKCS11\_TEST\_ENABLED** to 1\.
+ Build and flash the application to your device to run\. The test result are output to the serial port\.

The following is an example of the output test result\.

```
TEST(Full_PKCS11_StartFinish, PKCS11_StartFinish_FirstTest) PASS
TEST(Full_PKCS11_StartFinish, PKCS11_GetFunctionList) PASS
TEST(Full_PKCS11_StartFinish, PKCS11_InitializeFinalize) PASS
TEST(Full_PKCS11_StartFinish, PKCS11_GetSlotList) PASS
TEST(Full_PKCS11_StartFinish, PKCS11_OpenSessionCloseSession) PASS
TEST(Full_PKCS11_Capabilities, PKCS11_Capabilities) PASS
TEST(Full_PKCS11_NoObject, PKCS11_Digest) PASS
TEST(Full_PKCS11_NoObject, PKCS11_Digest_ErrorConditions) PASS
TEST(Full_PKCS11_NoObject, PKCS11_GenerateRandom) PASS
TEST(Full_PKCS11_NoObject, PKCS11_GenerateRandomMultiThread) PASS
TEST(Full_PKCS11_RSA, PKCS11_RSA_CreateObject) PASS
TEST(Full_PKCS11_RSA, PKCS11_RSA_FindObject) PASS
TEST(Full_PKCS11_RSA, PKCS11_RSA_GetAttributeValue) PASS
TEST(Full_PKCS11_RSA, PKCS11_RSA_Sign) PASS
TEST(Full_PKCS11_RSA, PKCS11_RSA_FindObjectMultiThread) PASS
TEST(Full_PKCS11_RSA, PKCS11_RSA_GetAttributeValueMultiThread) PASS
TEST(Full_PKCS11_RSA, PKCS11_RSA_DestroyObject) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_GenerateKeyPair) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_CreateObject) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_FindObject) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_GetAttributeValue) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_Sign) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_Verify) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_FindObjectMultiThread) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_GetAttributeValueMultiThread) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_SignVerifyMultiThread) PASS
TEST(Full_PKCS11_EC, PKCS11_EC_DestroyObject) PASS

-----------------------
27 Tests 0 Failures 0 Ignored
OK
```

 Testing is complete when all tests pass\.

**Note**  
To officially qualify a device for FreeRTOS, you must validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide to set up AWS IoT Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the AWS IoT Device Tester `configs` folder\.