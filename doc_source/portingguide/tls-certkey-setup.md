# Setting Up Certificates and Keys for the TLS Tests<a name="tls-certkey-setup"></a>

## `TLS_ConnectRSA()`<a name="tls-certs-rsa"></a>

This section provides instructions on setting up certificates and keys for testing your TLS port\.

For RSA device authentication, you can use the the private key and the certificate that you downloaded from the AWS IoT console when you registered your device\.

**Note**  
After you have registered your device as an AWS IoT thing, you can retrieve device certificates from the AWS IoT console, but you cannot retrieve private keys\.

Amazon FreeRTOS is a C language project\. You must format certificates and keys before you add them to the `aws_clientcredential_keys.h` header file\.

**To format the certificate and key for `aws_clientcredential_keys.h`**

1. In a browser window, open `<amazon-freertos>\tools\certificate_configuration\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` file that you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` file that you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<amazon-freertos>\demos\common\include`\. This overwrites the existing file in the directory\.
**Note**  
The certificate and private key are hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

## `TLS_ConnectEC()`<a name="tls-certs-ec"></a>

For Elliptic Curve Digital Signature Algorithm \(ECDSA\) authentication, you need to generate a private key, a certificate signing request \(CSR\), and a certificate\. You can use OpenSSL to generate the private key and CSR, and you can use the CSR to generate the certificate in the AWS IoT console\.

**To generate a private key and a CSR**

1. Use the following command to create a private key file named `<p256_privatekey>.pem` in the current working directory:

   ```
   openssl ecparam -name prime256v1 -genkey -noout -out <p256_privatekey>.pem
   ```

1. Use the following command to create a CSR file named `<csr>.csr` in the current working directory\.

   ```
   openssl req –new –key p256_privatekey.pem –out <csr>.csr
   ```

**To create a certificate in the AWS IoT console with a CSR**

1. Open the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Secure**, choose **Certificates**, and then choose **Create**\.

1. Choose **Create with CSR**, and then find and upload the `<csr>.csr` file that you created with OpenSSL\.

1. Choose **Activate** to activate the certificate, and then choose **Download** to download the certificate as a `.cert.pem` file\.

1. Choose **Attach a policy**, and then find and select the AWS IoT policy that you created and attached to your RSA certificate in the [Connecting Your Device to AWS IoT](testing-connect-iot.md) instructions, and choose **Done**\.

1. Attach the certificate to the AWS IoT thing that you created when you registered your device\.

1. From the **Certificates** page, find and select the certificate that you just created\. From the upper right of the page, choose **Actions**, and then choose **Attach thing**\.

1. Find and select the thing that you created for your device, and then choose **Attach**\.

You must format the certificate and private key for your device before you add them to the `aws_test_tls.h` header file\.

**To format the certificate and key for `aws_test_tls.h`**

1. In a browser window, open `<amazon-freertos>/tools/certificate_configuration/PEMfileToCString.html`\.

1. Under **PEM Certificate or Key**, choose the `<ID>-certificate.pem.crt` that you downloaded from the AWS IoT console\.

1. Choose **Display formatted PEM string to be copied into aws\_clientcredential\_keys\.h**, and then copy the certificate string\.

1. Open `<amazon-freertos>/tests/common/aws_test_tls.h`, and paste the formatted certificate string into the definition for `tlstestCLIENT_CERTIFICATE_PEM_EC`\.
**Note**  
The certificate and private key are hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

1. Follow the same steps to get the formatted string for the private key file that you created using OpenSSL \(`<p256_privatekey>.pem`\)\. Copy and paste the formatted private key string into the definition for `tlstestCLIENT_PRIVATE_KEY_PEM_EC` in `<amazon-freertos>/tests/common/aws_test_tls.h`\.

In `<amazon-freertos>/tests/common/aws_test_tls.h`, define the `tlstestMQTT_BROKER_ENDPOINT_EC` with the same AWS IoT MQTT broker endpoint address that you used in [Connecting Your Device to AWS IoT](testing-connect-iot.md)\.

## `TLS_ConnectMalformedCert()`<a name="tls-certs-malformed"></a>

This test verifies that you can use a malformed certificate to authenticate with the server\. Random modification of a certificate is likely to be rejected by X\.509 certificate verification before the connection request is sent out\. To set up a malformed certificate, we suggest that you modify the issuer of the certificate\.

**To modify the issuer of a certificate**

1. Take the valid client certificate that you have been using, `<ID>-certificate.pem.crt`\.

   In the Windows Certificate Manager, the certificate properties appear as follows:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/certificate1.png)

1. Using the following command, convert the certificate from PEM to DER:

   ```
   openssl x509 -outform der -in <ID>-certificate.pem.crt -out <ID>-certificate.der.crt
   ```

1. Open the DER certificate, and search for the following hexidecimal sequence:

   ```
   41 6d 61 7a 6f 6e 20 57 65 62 20 53 65 72 76 69 63 65 73
   ```

   This sequence, translated to plaintext, reads "Amazon Web Services\."

1. Change the `53` to `43`, so that the sequence becomes "Amazon Web Cervices" in plaintext, and save the file\.

   In the Windows Certificate Manager, the certificate properties now appear as follows:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/certificate2.png)

1. Use the following command to convert the certificate back to PEM:

   ```
   openssl x509 -inform der -in <ID>-certificate.der.crt -out <ID>-cert-modified.pem.crt
   ```

You must format the malformed certificate for your device before you add it to the `aws_test_tls.h` header file\.

**To format the certificate for `aws_test_tls.h`**

1. In a browser window, open `<amazon-freertos>/tools/certificate_configuration/PEMfileToCString.html`\.

1. Under **PEM Certificate or Key**, choose the `<ID>-certificate.pem.crt` that you created and then modified\.

1. Choose **Display formatted PEM string to be copied into aws\_clientcredential\_keys\.h**, and then copy the certificate string\.

1. Open `<amazon-freertos>/tests/common/aws_test_tls.h`, and paste the formatted certificate string into the definition for `tlstestCLIENT_CERTIFICATE_PEM_MALFORMED`\.
**Note**  
The certificate is hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

## `TLS_ConnectBYOCCredentials()`<a name="tls-certs-byoc"></a>

You can use your own certificate for authentication\. To create and register a certificate with a valid rootCA/CA chain, follow the instructions in [Creating a BYOC \(ECDSA\)](afr-byoc.md)\. After you create the certificate, you need to attach some policies to your device certificate, and then you need to attach your device's thing to the certificate\.

**To attach a policy to your device certificate**

1. Open the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Secure**, choose **Certificates**, and then choose the device certificate that you created and registered in [Creating a BYOC \(ECDSA\)](afr-byoc.md)\.

1. Choose **Actions**, and then choose **Attach policy**\.

1. Find and choose the AWS IoT policy that you created and attached to your RSA certificate in the [Connecting Your Device to AWS IoT](testing-connect-iot.md) instructions, and then choose **Attach**\.

**To attach a thing to your device certificate**

1. From the **Certificates** page, find and choose the same device certificate, choose **Actions**, and then choose **Attach thing**\.

1. Find and choose the thing that you created for your device, and then choose **Attach**\.

**To format the certificate for `aws_test_tls.h`**

1. In a browser window, open `<amazon-freertos>/tools/certificate_configuration/PEMfileToCString.html`\.

1. Under **PEM Certificate or Key**, choose the `<ID>-certificate.pem.crt` that you created and then modified\.

1. Choose **Display formatted PEM string to be copied into aws\_clientcredential\_keys\.h**, and then copy the certificate string\.

1. Open `<amazon-freertos>/tests/common/aws_test_tls.h`, and paste the formatted certificate string into the definition for `tlstestCLIENT_BYOC_CERTIFICATE_PEM`\.
**Note**  
The certificate is hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

1. Follow the same steps to get the formatted string for the private key file that you created\. Copy and paste the formatted private key string into the definition for `tlstestCLIENT_BYOC_PRIVATE_KEY_PEM` in `<amazon-freertos>/tests/common/aws_test_tls.h`\.

## `TLS_ConnectUntrustedCert()`<a name="tls-certs-untrusted"></a>

You can use your own certificate for authentication, without registering your certificate with AWS IoT\. To create a certificate with a valid rootCA/CA chain, follow the instructions in [Creating a BYOC \(ECDSA\)](afr-byoc.md), but skip the final instructions for registering your device with AWS IoT\.

**To format the certificate for `aws_test_tls.h`**

1. In a browser window, open `<amazon-freertos>/tools/certificate_configuration/PEMfileToCString.html`\.

1. Under **PEM Certificate or Key**, choose the `<ID>-certificate.pem.crt` that you created and then modified\.

1. Choose **Display formatted PEM string to be copied into aws\_clientcredential\_keys\.h**, and then copy the certificate string\.

1. Open `<amazon-freertos>/tests/common/aws_test_tls.h`, and paste the formatted certificate string into the definition for `tlstestCLIENT_UNTRUSTED_CERTIFICATE_PEM`\.
**Note**  
The certificate is hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

1. Follow the same steps to get the formatted string for the private key file that you created\. Copy and paste the formatted private key string into the definition for `tlstestCLIENT_UNTRUSTED_PRIVATE_KEY_PEM` in `<amazon-freertos>/tests/common/aws_test_tls.h`\.