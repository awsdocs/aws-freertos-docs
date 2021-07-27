# OTA security<a name="dev-guide-ota-security"></a>

The following are three aspects of over\-the\-air \(OTA\) security:

**Connection security**  
The OTA Update Manager service relies on existing security mechanisms, such as Transport Layer Security \(TLS\) mutual authentication, used by AWS IoT\. OTA update traffic passes through the AWS IoT device gateway and uses AWS IoT security mechanisms\. Each incoming and outgoing HTTP or MQTT message through the device gateway undergoes strict authentication and authorization\.

**Authenticity and integrity of OTA updates**  
Firmware can be digitally signed before an OTA update to ensure that it is from a reliable source and has not been tampered with\.   
The FreeRTOS OTA Update Manager service uses Code Signing for AWS IoT to automatically sign your firmware\. For more information, see [Code Signing for AWS IoT](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html)\.   
The OTA Agent, which runs on your devices, performs integrity checks on the firmware when it arrives on the device\.

**Operator security**  
Every API call made through the control plane API undergoes standard IAM Signature Version 4 authentication and authorization\. To create a deployment, you must have permissions to invoke the `CreateDeployment`, `CreateJob`, and `CreateStream` APIs\. In addition, in your Amazon S3 bucket policy or ACL, you must give read permissions to the AWS IoT service principal so that the firmware update stored in Amazon S3 can be accessed during streaming\. 

## Code Signing for AWS IoT<a name="dev-guide-code-signing"></a>

The AWS IoT console uses [Code Signing for AWS IoT](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html) to automatically sign your firmware image for any device supported by AWS IoT\.

Code Signing for AWS IoT uses a certificate and private key that you import into ACM\. You can use a self–signed certificate for testing, but we recommend that you obtain a certificate from a well–known commercial certificate authority \(CA\)\.

Code–signing certificates use the X\.509 version 3 `Key Usage` and `Extended Key Usage` extensions\. The `Key Usage` extension is set to `Digital Signature` and the `Extended Key Usage` extension is set to `Code Signing`\. For more information about signing your code image, see the [Code Signing for AWS IoT Developer Guide](https://docs.aws.amazon.com/signer/latest/developerguide/Welcome.html) and the [Code Signing for AWS IoT API Reference](https://docs.aws.amazon.com/signer/latest/api/Welcome.html)\.

**Note**  
You can download the Code Signing for AWS IoT SDK from [Tools for Amazon Web Services](https://aws.amazon.com/tools/)\. 