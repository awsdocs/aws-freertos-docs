# Configuring the Amazon FreeRTOS Demos<a name="freertos-configure"></a>

You need to edit some configuration files in your Amazon FreeRTOS directory before you can compile and run any demos on your board\.

If you are following the **Quick Connect** workflow on the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), follow the configuration instructions in the workflow on the console, and ignore these procedures\.

**To configure your AWS IoT endpoint**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint is displayed in **Endpoint**\. It should look like `<1234567890123>-ats.iot.<us-east-1>.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\.

   Your device should have an AWS IoT thing name\. Make a note of this name\.

1. Open `<BASE_FOLDER>\demos\common\include\aws_clientcredential.h`\. 

1. Specify values for the following constants:
   + `static const char clientcredentialMQTT_BROKER_ENDPOINT[] = "Your AWS IoT endpoint";`
   + `#define clientcredentialIOT_THING_NAME "The AWS IoT thing name of your board"`

**To configure your Wi\-Fi**
**Note**  
If your board does not support Wi\-Fi, you can skip these steps\.

1. `<BASE_FOLDER>\demos\common\include\aws_clientcredential.h`\.

1. Specify values for the following `#define` constants:
   + `#define clientcredentialWIFI_SSID "The SSID for your Wi-Fi network"`
   + `#define clientcredentialWIFI_PASSWORD "The password for your Wi-Fi network"`
   + `#define clientcredentialWIFI_SECURITY` *The security type of your Wi\-Fi network*

     Valid security types are:
     + `eWiFiSecurityOpen` \(Open, no security\)
     + `eWiFiSecurityWEP` \(WEP security\)
     + `eWiFiSecurityWPA` \(WPA security\)
     + `eWiFiSecurityWPA2` \(WPA2 security\)

**To format your AWS IoT credentials**
**Note**  
To configure your AWS IoT credentials, you need the private key and certificate that you downloaded from the AWS IoT console when you registered your device\. After you have registered your device as an AWS IoT thing, you can retrieve device certificates from the AWS IoT console, but you cannot retrieve private keys\.

Amazon FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\.

1. In a browser window, open `<BASE_FOLDER>\tools\certificate_configuration\CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `<ID>-certificate.pem.crt` that you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `<ID>-private.pem.key` that you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `<BASE_FOLDER>\demos\common\include`\. This overwrites the existing file in the directory\.
**Note**  
The certificate and private key are hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

After you configure Amazon FreeRTOS, you can continue to the Getting Started guide for your board to compile and run the Amazon FreeRTOS demo\. The demo application that is used in the Getting Started tutorial is the Hello World MQTT demo, which is located at `<BASE_FOLDER>/demos/common/mqtt/aws_hello_world.c`\.