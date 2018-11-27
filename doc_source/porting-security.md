# Security<a name="porting-security"></a>

Amazon FreeRTOS has two libraries that work together to provide platform security: TLS and PKCS\#11\. Amazon FreeRTOS provides a software security solution built on mbed TLS \(a third\-party TLS library\)\. The TLS API uses mbed TLS to encrypt and authenticate network traffic\. PKCS\#11 provides an standard interface to handle cryptographic material and replace software cryptographic operations with implementations that fully use the hardware\.

## TLS<a name="porting-security-tls"></a>

If you choose to use an mbed TLS\-based implementation, you can use aws\_tls\.c as\-is, provided that PKCS\#11 is implemented\.

The public interfaces of this library and a detailed explanation for each TLS interface are listed in `lib/include/aws _tls.h`\. The Amazon FreeRTOS implementation of the TLS library is in `lib/tls/aws_tls.c`\. If you decide to use your own TLS support, you can either implement the TLS public interfaces and plug them into the sockets public interfaces, or you can directly port the sockets library using your own TLS interfaces\.

The `mbedtls_hardware_poll` function provides randomness for the deterministic random bit generator\. For security, no two boards should provide identical randomness, and a board must not provide the same random value repeatedly, even if the board is reset\. Examples of implementations for this function can be found in ports using mbed TLS at `demos\<vendor>\<platform>\common\application_code\<vendor code> \aws_entropy_hardware_poll.c`

### Using TLS Libraries Other Than mbed TLS<a name="porting-security-other"></a>

If you are porting another TLS library to Amazon FreeRTOS, make sure that a compatible TLS cipher suite is implemented in your port\. For more information, see [Cipher Suites Compatible with AWS IoT](http://docs.aws.amazon.com/iot/latest/developerguide/iot-security-identity.html)\. The following cipher suites are compatible with AWS IoT Greengrass devices:
+ `TLS_RSA_WITH_AES_128_GCM_SHA256`
+ `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
+ `TLS_RSA_WITH_AES_256_GCM_SHA384`
+ `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA` \(not recommended\)
+ `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA` \(not recommended\)
+ `TLS_RSA_WITH_AES_128_CBC_SHA` \(not recommended\)
+ `TLS_RSA_WITH_AES_256_CBC_SHA` \(not recommended\)

Due to attacks on SHA1, we recommend that you use SHA256 or SHA384 for Amazon FreeRTOS connections\.

## PKCS\#11<a name="porting-security-pkcs"></a>

Amazon FreeRTOS implements a PKCS\#11 standard for cryptographic operations and key storage\. The header file for PKCS\#11 is an industry standard\. To port PKCS\#11, you must implement functions to read and write credentials to and from non\-volatile memory \(NVM\)\. 

The functions you need to implement are listed in `lib/third_party/pkcs11/pkcs11f.h`\. The implementation of the public interfaces is located in: `lib/pkcs11/portable/vendor/board/pkcs11.c`\.

The following functions are the minimum required to support TLS client authentication in Amazon FreeRTOS:
+ `C_GetFunctionList`
+ `C_Initialize`
+ `C_GetSlotList`
+ `C_OpenSession`
+ `C_FindObjectsInit`
+ `C_FindObjects`
+ `C_FindObjectsFinal`
+ `C_GetAttributeValue`
+ `C_FindObjectsInit`
+ `C_FindObjects`
+ `C_FindObjectsFinal`
+ `C_GetAttributeValue`
+ `C_SignInit`
+ `C_Sign`
+ `C_CloseSession`
+ `C_Finalize`

For a general porting guide, see the open standard, [PKCS \#11 Cryptographic Token Interface Base Specification](http://docs.oasis-open.org/pkcs11/pkcs11-base/v2.40/os/pkcs11-base-v2.40-os.html)\.

Two additional non\-PKCS\#11 standard functions must be implemented for keys and certificates to survive power cycle:

`prvSaveFile`  
Writes the client \(device\) private key and certificate to memory\. If your NVM is susceptible to damage from write cycles, you might want to use an additional variable to record whether the device private key and device certificate have been initialized\.

`prvReadFile`  
Retrieves either the device private key or device certificate from NVM into RAM for use by the TLS library\.