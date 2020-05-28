# Creating a Code\-Signing Certificate for Custom Hardware<a name="ota-code-sign-cert-other"></a>

Using an appropriate toolset, create a self\-signed certificate and private key for your hardware\.

Use the AWS Command Line Interface to import your code\-signing certificate, private key, and certificate chain into AWS Certificate Manager\. For information about installing the AWS CLI, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)\.

After you create your code\-signing certificate, you can use the AWS CLI to import it into ACM:

```
aws acm import-certificate --certificate file://code-sign.crt --private-key file://code-sign.key
```

The output from this command displays an ARN for your certificate\. You need this ARN when you create an OTA update job\.

ACM requires certificates to use specific algorithms and key sizes\. For more information, see [Prerequisites for Importing Certificates](https://docs.aws.amazon.com/acm/latest/userguide/import-certificate-prerequisites.html)\. For more information about ACM, see [Importing Certificates into AWS Certificate Manager](https://docs.aws.amazon.com/acm/latest/userguide/import-certificate.html)\.

You must copy, paste, and format the contents of your code\-signing certificate into the `aws_ota_codesigner_certificate.h` file that is part of the FreeRTOS code you download later\.
