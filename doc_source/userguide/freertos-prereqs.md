# First steps<a name="freertos-prereqs"></a>

To get started using FreeRTOS with AWS IoT, you need an AWS account, an IAM user with permission to access AWS IoT and FreeRTOS cloud services\. You also need to download FreeRTOS and configure your board's FreeRTOS demo project to work with AWS IoT\. The following sections walk you through these requirements\.

**Note**  
If you're using the Espressif ESP32\-DevKitC, ESP\-WROVER\-KIT, or the ESP32\-WROOM\-32SE, skip these steps and go to [Getting started with the Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT](getting_started_espressif.md)\.  
If you're using the Nordic nRF52840\-DK, skip these steps and go to [Getting started with the Nordic nRF52840\-DK](getting_started_nordic.md)\.

1. [Setting up your AWS account and permissions](#freertos-account-and-permissions) 

   After you complete the instructions in [Setting up your AWS account and permissions](#freertos-account-and-permissions), you can follow the **Quick Connect** workflow in the [FreeRTOS console](https://console.aws.amazon.com/freertos) to quickly connect your board to the AWS Cloud\. If you follow the **Quick Connect** workflow, you do not need to complete the remaining steps in this list\. Note that configurations of FreeRTOS are currently not available on the FreeRTOS console for the following boards:
   + Cypress CYW943907AEVAL1F Development Kit
   + Cypress CYW954907AEVAL1F Development Kit

1. [Registering your MCU board with AWS IoT](#get-started-freertos-thing)

1. [Downloading FreeRTOS](#freertos-download)

1. [Configuring the FreeRTOS demos](#freertos-configure)

## Setting up your AWS account and permissions<a name="freertos-account-and-permissions"></a>

To create an AWS account, see [Create and Activate an AWS Account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

To add an IAM user to your AWS account, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\. To grant your IAM user account access to AWS IoT and FreeRTOS, attach the following IAM policies to your IAM user account:
+ `AmazonFreeRTOSFullAccess`
+ `AWSIoTFullAccess`

**To attach the AmazonFreeRTOSFullAccess policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AmazonFreeRTOSFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

**To attach the AWSIoTFullAccess policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AWSIoTFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

For more information about IAM and user accounts, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

For more information about policies, see [IAM Permissions and Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\.

After you set up your AWS account and permissions, you can continue to [Registering your MCU board with AWS IoT](#get-started-freertos-thing) or to the **Quick Connect** workflow in the [FreeRTOS console](https://console.aws.amazon.com/freertos)\. 

## Registering your MCU board with AWS IoT<a name="get-started-freertos-thing"></a>

Your board must be registered with AWS IoT to communicate with the AWS Cloud\. To register your board with AWS IoT, you need the following:

An AWS IoT policy  
The AWS IoT policy grants your device permissions to access AWS IoT resources\. It is stored on the AWS Cloud\.

An AWS IoT thing  
An AWS IoT thing allows you to manage your devices in AWS IoT\. It is stored on the AWS Cloud\.

A private key and X\.509 certificate  
The private key and certificate allow your device to authenticate with AWS IoT\. 

If you use the **Quick Connect** workflow in the [ FreeRTOS console](https://console.aws.amazon.com/freertos), a policy, an AWS IoT thing, and a key and certificate are created for you\. If you use the **Quick Connect** workflow, you can ignore the following procedures\.

To register your board manually, follow the procedures below\.

**To create an AWS IoT policy**

1. To create an IAM policy, you need to know your AWS Region and AWS account number\. 

   To find your AWS account number, open the [AWS Management Console](https://console.aws.amazon.com/), locate and expand the menu beneath your account name in the upper\-right corner, and choose **My Account**\. Your account ID is displayed under **Account Settings**\.

   To find the AWS region for your AWS account, use the AWS Command Line Interface\. To install the AWS CLI, follow the instructions in the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)\. After you install the AWS CLI, open a command prompt window and enter the following command:

   ```
   aws iot describe-endpoint
   ```

   The output should look like this:

   ```
   {
       "endpointAddress": "xxxxxxxxxxxxxx.iot.us-west-2.amazonaws.com"
   }
   ```

   In this example, the region is `us-west-2`\.

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Secure**, choose **Policies**, and then choose **Create**\.

1. Enter a name to identify your policy\.

1. In the **Add statements** section, choose **Advanced mode**\. Copy and paste the following JSON into the policy editor window\. Replace *aws\-region* and *aws\-account* with your AWS Region and account ID\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
       {
           "Effect": "Allow",
           "Action": "iot:Connect",
           "Resource":"arn:aws:iot:aws-region:aws-account-id:*"
       }, 
       {
           "Effect": "Allow",
           "Action": "iot:Publish",
           "Resource": "arn:aws:iot:aws-region:aws-account-id:*"
       },
       {
            "Effect": "Allow",
            "Action": "iot:Subscribe",
            "Resource": "arn:aws:iot:aws-region:aws-account-id:*"
       },
       {
            "Effect": "Allow",
            "Action": "iot:Receive",
            "Resource": "arn:aws:iot:aws-region:aws-account-id:*"
       }
       ]
   }
   ```

   This policy grants the following permissions:  
`iot:Connect`  
Grants your device the permission to connect to the AWS IoT message broker with any client ID\.  
`iot:Publish`  
Grants your device the permission to publish an MQTT message on any MQTT topic\.  
`iot:Subscribe`  
Grants your device the permission to subscribe to any MQTT topic filter\.  
`iot:Receive`  
Grants your device the permission to receive messages from the AWS IoT message broker on any MQTT topic\.

1. Choose **Create**\.

**To create an IoT thing, private key, and certificate for your device**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\.

1. If you do not have any IoT things registered in your account, the **You don't have any things yet** page is displayed\. If you see this page, choose **Register a thing**\. Otherwise, choose **Create**\.

1. On the **Creating AWS IoT things** page, choose **Create a single thing**\.

1. On the **Add your device to the thing registry** page, enter a name for your thing, and then choose **Next**\.

1. On the **Add a certificate for your thing** page, under **One\-click certificate creation**, choose **Create certificate**\.

1. Download your private key and certificate by choosing the **Download** links for each\.

1. Choose **Activate** to activate your certificate\. Certificates must be activated prior to use\.

1. Choose **Attach a policy** to attach a policy to your certificate that grants your device access to AWS IoT operations\.

1. Choose the policy you just created and choose **Register thing**\.

After your board is registered with AWS IoT, you can continue to [Downloading FreeRTOS](#freertos-download)\.

## Downloading FreeRTOS<a name="freertos-download"></a>

You can download FreeRTOS from the FreeRTOS console or from the [FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\.

**Note**  
If you're following the **Quick Connect** workflow in the [FreeRTOS console](https://console.aws.amazon.com/freertos), you can ignore the following procedure\.\.<a name="download-free-rtos"></a>

**To download FreeRTOS from the FreeRTOS console**

1. Sign in to the [FreeRTOS console](https://console.aws.amazon.com/freertos)\.

1. Under **Predefined configurations**, find **Connect to AWS IoT\- *Platform***, and then choose **Download**\.

1. Unzip the downloaded file to a directory, and copy the directory path\.
**Important**  
In this topic, the path to the FreeRTOS download directory is referred to as `freertos`\.
Space characters in the `freertos` path can cause build failures\. When you clone or copy the repository, make sure the path you that create doesn't contain space characters\.
The maximum length of a file path on Microsoft Windows is 260 characters\. Long FreeRTOS download directory paths can cause build failures\.

**Note**  
If you're getting started with the Cypress CYW954907AEVAL1F or CYW943907AEVAL1F development kits, you must download FreeRTOS from GitHub\. See the [README\.md](https://github.com/aws/amazon-freertos/blob/master/README.md) file for instructions\. Configurations of FreeRTOS for these boards aren't currently available from the FreeRTOS console\.

After you download FreeRTOS, you can continue to [Configuring the FreeRTOS demos](#freertos-configure)\.

## Configuring the FreeRTOS demos<a name="freertos-configure"></a>

You need to edit some configuration files in your FreeRTOS directory before you can compile and run any demos on your board\.

If you are following the **Quick Connect** workflow on the [FreeRTOS console](https://console.aws.amazon.com/freertos), follow the configuration instructions in the workflow on the console, and ignore these procedures\.

**To configure your AWS IoT endpoint**

You need to provide FreeRTOS with your AWS IoT endpoint so the application running on your board can send requests to the correct endpoint\.

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint is displayed in **Endpoint**\. It should look like `1234567890123-ats.iot.us-east-1.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\.

   Your device should have an AWS IoT thing name\. Make a note of this name\.

1. Open `demos/include/aws_clientcredential.h`\. 

1. Specify values for the following constants:
   + `#define clientcredentialMQTT_BROKER_ENDPOINT "Your AWS IoT endpoint";`
   + `#define clientcredentialIOT_THING_NAME "The AWS IoT thing name of your board"`

**To configure your Wi\-Fi**

If your board is connecting to the internet across a Wi\-Fi connection, you need to provide FreeRTOS with Wi\-Fi credentials to connect to the network\. If your board does not support Wi\-Fi, you can skip these steps\.

1. `demos/include/aws_clientcredential.h`\.

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

FreeRTOS needs the AWS IoT certificate and private keys associated with your registered thing and its permissions policies to successfully communicate with AWS IoT on behalf of your device\.
**Note**  
To configure your AWS IoT credentials, you need the private key and certificate that you downloaded from the AWS IoT console when you registered your device\. After you have registered your device as an AWS IoT thing, you can retrieve device certificates from the AWS IoT console, but you cannot retrieve private keys\.

FreeRTOS is a C language project, and the certificate and private key must be specially formatted to be added to the project\.

1. In a browser window, open `tools/certificate_configuration/CertificateConfigurator.html`\.

1. Under **Certificate PEM file**, choose the `ID-certificate.pem.crt` that you downloaded from the AWS IoT console\.

1. Under **Private Key PEM file**, choose the `ID-private.pem.key` that you downloaded from the AWS IoT console\.

1. Choose **Generate and save aws\_clientcredential\_keys\.h**, and then save the file in `demos/include`\. This overwrites the existing file in the directory\.
**Note**  
The certificate and private key are hard\-coded for demonstration purposes only\. Production\-level applications should store these files in a secure location\.

After you configure FreeRTOS, you can continue to the Getting Started guide for your board to compile and run the FreeRTOS demo\. The demo application that is used in the Getting Started tutorial is the coreMQTT Mutual Authentication demo, which is located at `demos/mqtt/iot_demo_mqtt.c`\.

After you complete the [First steps](#freertos-prereqs), you can set up your platform's hardware and its software development environment, and then compile and run the demo on your board\. For board\-specific instructions, see the [Board\-specific getting started guides](getting-started-guides.md)\.