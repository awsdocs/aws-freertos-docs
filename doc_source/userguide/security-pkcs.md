# corePKCS11 library<a name="security-pkcs"></a>

## Overview<a name="freertos-pkcs-overview"></a>

The Public Key Cryptography Standard \#11 defines a platform\-independent API to manage and use cryptographic tokens\. [PKCS \#11](https://en.wikipedia.org/wiki/PKCS_11) refers to the API defined by the standard and to the standard itself\. The PKCS \#11 cryptographic API abstracts key storage, get/set properties for cryptographic objects, and session semantics\. It's widely used for manipulating common cryptographic objects, and it's important because the functions it specifies allow application software to use, create, modify, and delete cryptographic objects, without ever exposing those objects to the application's memory\. For example, FreeRTOS AWS reference integrations use a small subset of the PKCS \#11 API to access the secret \(private\) key necessary to create a network connection that is authenticated and secured by the [Transport Layer Security \(TLS\)](https://en.wikipedia.org/wiki/Transport_Layer_Security) protocol without the application ever 'seeing' the key\.

The corePKCS11 library contains a software\-based mock implementation of the PKCS \#11 interface \(API\) that uses the cryptographic functionality provided by Mbed TLS\. Using a software mock enables rapid development and flexibility, but it's expected that you will replace the mock with an implementation specific to the secure key storage used in your production devices\. Generally, vendors for secure cryptoprocessors, such as Trusted Platform Module \(TPM\), Hardware Security Module \(HSM\), Secure Element, or any other type of secure hardware enclave, distribute a PKCS \#11 implementation with the hardware\. The purpose of the corePKCS11 software only mock library is therefore to provide a non hardware specific PKCS \#11 implementation that allows for rapid prototyping and development before switching to a cryptoprocessor specific PKCS \#11 implementation in production devices\.

Only a subset of the PKCS \#11 standard is implemented, with a focus on operations involving asymmetric keys, random number generation, and hashing\. The targeted use cases include certificate and key management for TLS authentication, and code\-sign signature verification, on small embedded devices\. See the file `pkcs11.h` \(obtained from OASIS, the standard body\) in the FreeRTOS source code repository\. In the [ FreeRTOS reference implementation](https://docs.aws.amazon.com/embedded-csdk/latest/lib-ref/libraries/standard/corePKCS11/docs/doxygen/output/html/index.html), PKCS \#11 API calls are made by the TLS helper interface in order to perform TLS client authentication during `SOCKETS_Connect`\. PKCS \#11 API calls are also made by our one\-time developer provisioning workflow to import a TLS client certificate and private key for authentication to the AWS IoT MQTT broker\. Those two use cases, provisioning and TLS client authentication, require implementation of only a small subset of the PKCS \#11 interface standard\.

## Features<a name="freertos-pcks-features"></a>

The following subset of PKCS \#11 is used\. This list is in roughly the order in which the routines are called in support of provisioning, TLS client authentication, and cleanup\. For detailed descriptions of the functions, see the PKCS \#11 documentation provided by the standard committee\.

### General setup and tear down API<a name="pkcs-required-setup-teardown"></a>
+ `C_Initialize`
+ `C_Finalize`
+ `C_GetFunctionList`
+ `C_GetSlotList`
+ `C_GetTokenInfo`
+ `C_OpenSession`
+ `C_CloseSession`
+ `C_Login`

### Provisioning API<a name="pkcs-required-provisioning"></a>
+ `C_CreateObject CKO_PRIVATE_KEY` \(for device private key\)
+ `C_CreateObject CKO_CERTIFICATE` \(for device certificate and code verification certificate\)
+ `C_GenerateKeyPair`
+ `C_DestroyObject`

### Client authentication<a name="pkcs-required-client-auth"></a>
+ `C_GetAttributeValue`
+ `C_FindObjectsInit`
+ `C_FindObjects`
+ `C_FindObjectsFinal`
+ `C_GenerateRandom`
+ `C_SignInit`
+ `C_Sign`
+ `C_VerifyInit`
+ `C_Verify`
+ `C_DigestInit`
+ `C_DigestUpdate`
+ `C_DigestFinal`

## Asymmetric cryptosystem support<a name="pkcs-asym-crypto"></a>

The FreeRTOS reference implementation uses PKCS \#11 2048\-bit RSA \(signing only\) and ECDSA with the NIST P\-256 curve\. The following instructions describe how to create an AWS IoT thing based on a P\-256 client certificate\.

Make sure you are using the following \(or more recent\) versions of the AWS CLI and OpenSSL:

```
aws --version
aws-cli/1.11.176 Python/2.7.9 Windows/8 botocore/1.7.34

openssl version
OpenSSL 1.0.2g  1 Mar 2016
```

The following procedure assumes that you used the aws configure command to configure the AWS CLI\. For more information, see [Quick configuration with aws configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config) in the *AWS Command Line Interface User Guide*\.

**To create an AWS IoT thing based on a P\-256 client certificate**

1. Create an AWS IoT thing\.

   ```
   aws iot create-thing --thing-name thing-name
   ```

1. Use OpenSSL to create a P\-256 key\.

   ```
   openssl genpkey -algorithm EC -pkeyopt ec_paramgen_curve:P-256 -pkeyopt ec_param_enc:named_curve -outform PEM -out thing-name.key
   ```

1. Create a certificate enrollment request signed by the key created in step 2\.

   ```
   openssl req -new -nodes -days 365 -key thing-name.key -out thing-name.req
   ```

1. Submit the certificate enrollment request to AWS IoT\.

   ```
   aws iot create-certificate-from-csr  \
     --certificate-signing-request file://thing-name.req --set-as-active  \
     --certificate-pem-outfile thing-name.crt
   ```

1. Attach the certificate \(referenced by the ARN output by the previous command\) to the thing\.

   ```
   aws iot attach-thing-principal --thing-name thing-name \
     --principal "arn:aws:iot:us-east-1:123456789012:cert/86e41339a6d1bbc67abf31faf455092cdebf8f21ffbc67c4d238d1326c7de729"
   ```

1. Create a policy\. \(This policy is too permissive\. It should be used for development purposes only\.\)

   ```
   aws iot create-policy --policy-name FullControl --policy-document file://policy.json
   ```

   The following is a listing of the policy\.json file specified in the `create-policy` command\. You can omit the `greengrass:*` action if you don't want to run the FreeRTOS demo for Greengrass connectivity and discovery\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "iot:*",
         "Resource": "*"
       },
       {
         "Effect": "Allow",
         "Action": "greengrass:*",
         "Resource": "*"
       }
     ]
   }
   ```

1. Attach the principal \(certificate\) and policy to the thing\.

   ```
   aws iot attach-principal-policy --policy-name FullControl \
     --principal "arn:aws:iot:us-east-1:123456789012:cert/86e41339a6d1bbc67abf31faf455092cdebf8f21ffbc67c4d238d1326c7de729"
   ```

Now, follow the steps in the [AWS IoT Getting Started](https://docs.aws.amazon.com/iot/latest/developerguide/iot-gs.html) section of this guide\. Don’t forget to copy the certificate and private key you created into your `aws_clientcredential_keys.h` file\. Copy your thing name into `aws_clientcredential.h`\.

**Note**  
The certificate and private key are hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

## Porting<a name="freertos-pkcs-porting"></a>

For information about porting the corePKCS11 library to your platform, see [Porting the corePKCS11 Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-pkcs.html) in the FreeRTOS Porting Guide\.

## Memory use<a name="freertos-pkcs-memory"></a>


****  

| Code Size of corePKCS11 \(example generated with GCC for ARM Cortex\-M\) | File | With \-O1 Optimization | With \-Os Optimization | 
| --- | --- | --- | --- | 
| core\_pkcs11\.c | 0\.8K | 0\.8K | 
| core\_pki\_utils\.c | 0\.5K | 0\.3K | 
| core\_pkcs11\_mbedtls\.c | 8\.7K | 7\.3K | 
| Total estimates | 10\.0K | 8\.4K | 