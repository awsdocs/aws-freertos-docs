# Creating a Code\-Signing Certificate for the Texas Instruments CC3200SF\-LAUNCHXL<a name="ota-code-sign-cert-ti"></a>

The SimpleLink Wi\-Fi CC3220SF Wireless Microcontroller Launchpad Development Kit supports two certificate chains for firmware code signing:
+ Production \(certificate\-catalog\)

  To use the production certificate chain, you must purchase a commercial code\-signing certificate and use the [TI Uniflash tool](http://www.ti.com/tool/UNIFLASH) to set the board to production mode\.
+ Testing and development \(certificate\-playground\) 

  The playground certificate chain allows you to try out OTA updates with a self‐signed code\-signing certificate\.

You need to use the AWS Command Line Interface to import your code\-signing certificate, private key, and certificate chain into AWS Certificate Manager\. For information about installing the AWS CLI, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)\.

Download and install the latest version of [SimpleLink CC3220 SDK](http://www.ti.com/tool/download/SIMPLELINK-CC3220-SDK)\. By default, the files you need are located here:

`C:\ti\simplelink_cc32xx_sdk_version\tools\cc32xx_tools\certificate-playground` \(Windows\) 

`/Applications/Ti/simplelink_cc32xx_version/tools/cc32xx_tools/certificate-playground` \(macOS\)

The certificates in the SimpleLink CC3220 SDK are in DER format\. To create a self‐signed code\-signing certificate, you must convert them to PEM format\.

Follow these steps to create a code\-signing certificate that is linked to the Texas Instruments playground certificate hierarchy and meets AWS Certificate Manager and Code Signing for AWS IoT criteria\.

**Note**  
To create a code signing certificate, install [OpenSSL](https://www.openssl.org/) on your machine\. After you install OpenSSL, make sure that `openssl` is assigned to the OpenSSL executable in your command prompt or terminal environment\.

**To create a self‐signed code signing certificate**

1. Open a command prompt or terminal with administrator permissions\.

1. In your working directory, use the following text to create a file named `cert_config.txt`\. Replace *test\_signer@amazon\.com* with your email address\.

   ```
   [ req ]
   prompt             = no
   distinguished_name = my dn
   
   [ my dn ]
   commonName = test_signer@amazon.com
   
   [ my_exts ]
   keyUsage         = digitalSignature
   extendedKeyUsage = codeSigning
   ```

1. Create a private key and certificate signing request \(CSR\):

   ```
   openssl req -config cert_config.txt -extensions my_exts -nodes -days 365 -newkey rsa:2048 -keyout tisigner.key -out tisigner.csr
   ```

1. Convert the Texas Instruments playground root CA private key from DER format to PEM format\.

   The TI playground root CA private key is located here:

   `C:\ti\simplelink_cc32xx_sdk_version\tools\cc32xx_tools\certificate-playground\dummy-root-ca-cert-key` \(Windows\) 

   `/Applications/Ti/simplelink_cc32xx_sdk_version/tools/cc32xx_tools/certificate-playground/dummy-root-ca-cert-key` \(macOS\)

   ```
   openssl rsa -inform DER -in dummy-root-ca-cert-key -out dummy-root-ca-cert-key.pem
   ```

1. Convert the Texas Instruments playground root CA certificate from DER format to PEM format\.

   The TI playground root certificate is located here:

   `C:\ti\simplelink_cc32xx_sdk_version\tools\cc32xx_tools\certificate-playground/dummy-root-ca-cert` \(Windows\) 

   `/Applications/Ti/simplelink_cc32xx_sdk_version/tools/cc32xx_tools/certificate-playground/dummy-root-ca-cert` \(macOS\)

   ```
   openssl x509 -inform DER -in dummy-root-ca-cert -out dummy-root-ca-cert.pem
   ```

1. Sign the CSR with the Texas Instruments root CA:

   ```
   openssl x509 -extfile cert_config.txt -extensions my_exts  -req -days 365 -in tisigner.csr -CA dummy-root-ca-cert.pem -CAkey dummy-root-ca-cert-key.pem -set_serial 01 -out tisigner.crt.pem -sha1
   ```

1. Convert your code\-signing certificate \(`tisigner.crt.pem`\) to DER format: 

   ```
   openssl x509 -in tisigner.crt.pem -out tisigner.crt.der -outform DER
   ```
**Note**  
You write the `tisigner.crt.der` certificate onto the TI development board later\.

1. Import the code\-signing certificate, private key, and certificate chain into AWS Certificate Manager:

   ```
   aws acm import-certificate --certificate file://tisigner.crt.pem --private-key file://tisigner.key --certificate-chain file://dummy-root-ca-cert.pem
   ```

   This command displays an ARN for your certificate\. You need this ARN when you create an OTA update job\.
**Note**  
This step is written with the assumption that you are going to use Code Signing for AWS IoT to sign your firmware images\. Although the use of Code Signing for AWS IoT is recommended, you can sign your firmware images manually\.