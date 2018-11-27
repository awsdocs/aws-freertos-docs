# Creating a Code\-Signing Certificate for the Amazon FreeRTOS Windows Simulator<a name="ota-code-sign-cert-win"></a>

The Amazon FreeRTOS Windows simulator requires a code\-signing certificate with an ECDSA P\-256 key and SHA\-256 hash to perform OTA updates\. If you don't have a code\-signing certificate, use these steps to create one:

1. In your working directory, use the following text to create a file named `cert_config`\. Replace *test\_signer@amazon\.com* with your email address:

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
   openssl req -new -x509 -config cert_config -extensions my_exts -nodes -days 365 -key ecdsasigner.key -out ecdsasigner.crt
   ```

1. Import the code\-signing certificate, private key, and certificate chain into AWS Certificate Manager:

   ```
   aws acm import-certificate --certificate file://ecdsasigner.crt --private-key
   								file://ecdsasigner.key
   ```

   This command displays an ARN for your certificate\. You need this ARN when you create an OTA update job\.
**Note**  
This step is written with the assumption that you are going to use Code Signing for Amazon FreeRTOS to sign your firmware images\. Although the use of Code Signing for Amazon FreeRTOS is recommended, you can sign your firmware images manually\.