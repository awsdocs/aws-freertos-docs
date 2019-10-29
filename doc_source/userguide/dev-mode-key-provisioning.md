# Developer\-Mode Key Provisioning<a name="dev-mode-key-provisioning"></a>

## Introduction<a name="dev-mode-key-provisioning-intro"></a>

This section discusses two options to get a trusted X\.509 client certificate onto an IoT device for lab testing\. Depending on the capabilities of the device, various provisioning\-related operations may or may not be supported, including onboard ECDSA key generation, private key import, and X\.509 certificate enrollment\. In addition, different use cases call for different levels of key protection, ranging from onboard flash storage to the use of dedicated crypto hardware\. This section provides logic for working within the cryptographic capabilities of your device\.

## Option \#1: Private Key Import from AWS IoT<a name="dev-mode-key-provisioning-option1"></a>

For lab testing purposes, if your device allows the import of private keys, follow the instructions in [Configuring the Amazon FreeRTOS Demos](freertos-configure.md)\.

## Option \#2: Onboard Private Key Generation<a name="dev-mode-key-provisioning-option2"></a>

If your device has a secure element, or if you prefer to generate your own device key pair and certificate, follow the instructions here\.

Initial Configuration  
First, perform the steps in [Configuring the Amazon FreeRTOS Demos](freertos-configure.md),  but skip the last step \(that is, don't do *To format your AWS IoT credentials*\)\. The net result should be that the `demos/include/aws_clientcredential.h` file has been updated with your settings, but the `demos/include/aws_clientcredential_keys.h` file has not\.

Demo Project Configuration  
Open the Hello World MQTT demo as described in the guide for your board in [Board\-specific Getting Started Guides](getting-started-guides.md) \. In the project, open the file `aws_dev_mode_key_provisioning.c` and change the definition of `keyprovisioningFORCE_GENERATE_NEW_KEY_PAIR`, which is set to zero by default, to one:  

```
#define keyprovisioningFORCE_GENERATE_NEW_KEY_PAIR 1
```
Then build and run the demo project and continue to the next step\.

Public Key Extraction  
Since the device has not yet been provisioned with a private key and client certificate, the demo will fail to authenticate to AWS IoT\. However, the Hello World MQTT demo starts by running developer\-mode key provisioning, resulting in the creation of a private key if one was not already present\. You should see something like the following near the beginning of the serial console output:  

```
7 910 [IP-task] Device public key, 91 bytes:
3059 3013 0607 2a86 48ce 3d02 0106 082a
8648 ce3d 0301 0703 4200 04cd 6569 ceb8
1bb9 1e72 339f e8cf 60ef 0f9f b473 33ac
6f19 1813 6999 3fa0 c293 5fae 08f1 1ad0
41b7 345c e746 1046 228e 5a5f d787 d571
dcb2 4e8d 75b3 2586 e2cc 0c
```
Copy the six lines of key bytes into a file called `DevicePublicKeyAsciiHex.txt`\. Then use the command\-line tool "xxd" to parse the hex bytes into binary:  

```
xxd -r -ps DevicePublicKeyAsciiHex.txt DevicePublicKeyDer.bin
```
Use "openssl" to format the binary encoded \(DER\) device public key as PEM:  

```
openssl ec -inform der -in DevicePublicKeyDer.bin -pubin -pubout -outform pem -out DevicePublicKey.pem
```
Don't forget to disable the temporary key generation setting you enabled above\. Otherwise, the device will create yet another key pair, and you will have to repeat the previous steps:  

```
#define keyprovisioningFORCE_GENERATE_NEW_KEY_PAIR 0
```

Public Key Infrastructure Setup  
Follow the instructions in [ Registering Your CA Certificate](https://docs.aws.amazon.com/iot/latest/developerguide/device-certs-your-own.html#register-CA-cert) to create a certificate hierarchy for your device lab certificate\. Stop before executing the sequence described in the section *Creating a Device Certificate Using Your CA Certificate*\.  
In this case, the device will not be signing the certificate request \(that is, the Certificate Service Request or CSR\) because the X\.509 encoding logic required for creating and signing a CSR has been excluded from the Amazon FreeRTOS demo projects to reduce ROM size\. Instead, for lab testing purposes, create a private key on your workstation and use it to sign the CSR\.  

```
openssl genrsa -out tempCsrSigner.key 2048
openssl req -new -key tempCsrSigner.key -out deviceCert.csr
```
Once your Certificate Authority has been created and registered with AWS IoT, use the following command to issue a client certificate based on the device CSR that was signed in the previous step:  

```
openssl x509 -req -in deviceCert.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out deviceCert.pem -days 500 -sha256 -force_pubkey DevicePublicKey.pem 
```
Even though the CSR was signed with a temporary private key, the issued certificate can only be used with the actual device private key\. The same mechanism can be used in production if you store the CSR signer key in separate hardware, and configure your certificate authority so that it only issues certificates for requests that have been signed by that specific key\. That key should also remain under the control of a designated administrator\.

Certificate Import  
With the certificate issued, the next step is to import it into your device\. You will also need to import your Certificate Authority \(CA\) certificate, since it is required in order for first\-time authentication to AWS IoT to succeed when using JITP\. In the `aws_clientcredential_keys.h` file in your project, set the `keyCLIENT_CERTIFICATE_PEM` macro to be the contents of deviceCert\.pem and set the `keyJITR_DEVICE_CERTIFICATE_AUTHORITY_PEM` macro to be the contents of `rootCA.pem`\.

Device Authorization  
Import `deviceCert.pem` into the AWS IoT registry as described in [ Use Your Own Certificate](https://docs.aws.amazon.com/iot/latest/developerguide/device-certs-your-own.html#manual-cert-registration)\. You must create a new AWS IoT thing, attach the PENDING certificate and a policy to your thing, then mark the certificate as ACTIVE\. All of these steps can be performed manually in the AWS IoT console\.  
Once the new client certificate is ACTIVE and associated with a thing and a policy, run the MQTT Hello World demo again\. This time, the connection to the AWS IoT MQTT broker will succeed\.