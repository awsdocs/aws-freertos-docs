# Creating a BYOC \(ECDSA\)<a name="afr-byoc"></a>

In these procedures, you use the AWS IoT console, the AWS Command Line Interface, and OpenSSL to create and register certificates and keys for a device on the AWS Cloud\. Make sure that you have installed and configured the AWS CLI on your machine before you run the AWS CLI commands\.

**Note**  
When you create CA certificates, use valid, consistent values for the Distinguished Name \(DN\) fields, when prompted\. For the Common Name field, you can use any value, unless otherwise instructed\.

**To generate a root CA**

1. Use the following command to generate a root CA private key:

   ```
   openssl ecparam -name prime256v1 -genkey -noout -out rootCA.key
   ```

1. Use the following command to generate a root CA certificate:

   ```
   openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.crt
   ```

**To generate an intermediate CA**

1. Create required files:

   ```
   touch index.txt
   ```

   ```
   echo 1000 > serial
   ```

1. Save the [`ca.config`](porting-ca-config.md) file in the current working directory\.

1. Use the following command to generate the intermediate CA's private key:

   ```
   openssl ecparam -name prime256v1 -genkey -noout -out intermediateCA.key
   ```

1. Use the following command to generate the intermediate CA's CSR:

   ```
   openssl req -new -sha256 -key intermediateCA.key -out intermediateCA.csr
   ```

1. Use the following command to sign the intermediate CA’s CSR with the root CA:

   ```
   openssl ca -config ca.config -notext -cert rootCA.crt -keyfile rootCA.key -days 500 -in intermediateCA.csr -out intermediateCA.crt
   ```

**To generate a device certificate**
**Note**  
An ECDSA certificate is used here as an example\.

1. Use the following command to generate a private key:

   ```
   openssl ecparam -name prime256v1 -genkey -noout -out deviceCert.key
   ```

1. Use the following command to generate a CSR for a device certificate:

   ```
   openssl req -new -key deviceCert.key -out deviceCert.csr
   ```

1. Use the following command to sign the device certificate with the intermediate CA:

   ```
   openssl x509 -req -in deviceCert.csr -CA intermediateCA.crt -CAkey intermediateCA.key -CAcreateserial -out deviceCert.crt -days 500 -sha256
   ```

**To register both CA certificates**

1. Use the following AWS CLI command to get the registration code:

   ```
   aws iot get-registration-code
   ```

1. Use the following command to generate a private key for verification certificates:

   ```
   openssl ecparam -name prime256v1 -genkey -noout -out verificationCert.key
   ```

1. Use the following command to create CSR for verification certificates:

   ```
   openssl req -new -key verificationCert.key -out verificationCert.csr
   ```

   When prompted, for `Common Name`, enter the registration code that you obtained in the first step\.

1. Use the following command to sign a verification certificate using the root CA:

   ```
   openssl x509 -req -in verificationCert.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out rootCAverificationCert.crt -days 500 -sha256
   ```

1. Use the following command to sign a verification certificate using the intermediate CA:

   ```
   openssl x509 -req -in verificationCert.csr -CA intermediateCA.crt -CAkey intermediateCA.key -CAcreateserial -out intermediateCAverificationCert.crt -days 500 -sha256
   ```

1. Use the following AWS CLI commands to register both CA certificates with AWS IoT:

   ```
   aws iot register-ca-certificate --ca-certificate file://rootCA.crt --verification-cert file://rootCAverificationCert.crt
   ```

   ```
   aws iot register-ca-certificate --ca-certificate file://intermediateCA.crt --verification-cert file://intermediateCAverificationCert.crt
   ```

1. Use the following AWS CLI command to activate both CA certificates:

   ```
   aws iot update-ca-certificate --certificate-id ID --new-status ACTIVE
   ```

   Where *ID* is the certificate ID of one of the certificates\.

**To register the device certificate**

1. Use the following AWS CLI command to register the device certificate with AWS IoT:

   ```
   aws iot register-certificate --certificate-pem file://deviceCert.crt --ca-certificate-pem file://intermediateCA.crt
   ```

1. Use the following AWS CLI command to activate the device certificate:

   ```
   aws iot update-certificate --certificate-id ID --new-status ACTIVE
   ```

   Where *ID* is the certificate ID of the certificate\.