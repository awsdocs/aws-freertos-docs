# Create a code\-signing certificate<a name="ota-code-sign-cert"></a>

To digitally sign firmware images, you need a code\-signing certificate and private key\. For testing purposes, you can create a self‐signed certificate and private key\. For production environments, purchase a certificate through a well‐known certificate authority \(CA\)\.

Different platforms require different types of code\-signing certificates\. The following sections describe how to create code\-signing certificates for different FreeRTOS\-qualified platforms\.

**Topics**
+ [Creating a code\-signing certificate for the Texas Instruments CC3220SF\-LAUNCHXL](ota-code-sign-cert-ti.md)
+ [Creating a code\-signing certificate for the Espressif ESP32](ota-code-sign-cert-esp.md)
+ [Creating a code\-signing certificate for the Nordic nrf52840\-dk](ota-code-sign-cert-nordic.md)
+ [Creating a code\-signing certificate for the FreeRTOS Windows simulator](ota-code-sign-cert-win.md)
+ [Creating a code\-signing certificate for custom hardware](ota-code-sign-cert-other.md)