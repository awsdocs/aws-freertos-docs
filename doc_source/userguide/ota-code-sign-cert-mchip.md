# Creating a Code\-Signing Certificate for the Microchip Curiosity PIC32MZEF<a name="ota-code-sign-cert-mchip"></a>

The Microchip Curiosity PIC32MZEF supports a self\-signed SHA256 with ECDSA code\-signing certificate\.

**Note**  
To create a code\-signing certificate, install [OpenSSL](https://www.openssl.org/) on your machine\. After you install OpenSSL, make sure that `openssl` is assigned to the OpenSSL executable in your command prompt or terminal environment\.  
Use the AWS Command Line Interface to import your code\-signing certificate, private key, and certificate chain into AWS Certificate Manager\. For information about installing the AWS CLI, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)\.

1. In your working directory, use the following text to create a file named `cert_config.txt`\. Replace *test\_signer@amazon\.com* with your email address:

   ```
   [ req ]
   prompt             = no
   distinguished_name = my_dn
                       
   [ my_dn ]
   commonName = test_signer@amazon.com
                       
   [ my_exts ]
   keyUsage         = digitalSignature
   extendedKeyUsage = codeSigning
   ```

1. Create an ECDSA code\-signing private key:

   ```
   openssl genpkey -algorithm EC -pkeyopt ec_paramgen_curve:P-256 -pkeyopt ec_param_enc:named_curve -outform PEM -out ecdsasigner.key
   ```

1. Create an ECDSA code\-signing certificate:

   ```
   openssl req -new -x509 -config cert_config.txt -extensions my_exts -nodes -days 365 -key ecdsasigner.key -out ecdsasigner.crt
   ```

1. Import the code\-signing certificate, private key, and certificate chain into AWS Certificate Manager:

   ```
   aws acm import-certificate --certificate file://ecdsasigner.crt --private-key file://ecdsasigner.key
   ```

   This command displays an ARN for your certificate\. You need this ARN when you create an OTA update job\.
**Note**  
This step is written with the assumption that you are going to use Code Signing for AWS IoT to sign your firmware images\. Although the use of Code Signing for AWS IoT is recommended, you can sign your firmware images manually\.