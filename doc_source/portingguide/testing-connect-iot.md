# Connecting your device to AWS IoT<a name="testing-connect-iot"></a>

Your device must be registered with AWS IoT to communicate with the AWS Cloud\. To register your board with AWS IoT, you need the following:

An AWS IoT policy  
The AWS IoT policy grants your device permissions to access AWS IoT resources\. It is stored in the AWS Cloud\.

An AWS IoT thing  
An AWS IoT thing allows you to manage your devices in AWS IoT\. It is stored in the AWS Cloud\.

A private key and X\.509 certificate  
The private key and certificate allow your device to authenticate with AWS IoT\.

Follow these procedures to create a policy, thing, and key and certificate\.

**To create an AWS IoT policy**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Secure**, choose **Policies**, and then choose **Create**\.

1. Enter a name to identify your policy\.

1. In the **Add statements** section, choose **Advanced mode**\. Copy and paste the following JSON into the policy editor window:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
       {
   "Effect": "Allow",
   "Action": "iot:*",
   "Resource": "*"
   }
       ]
   }
   ```
**Important**  
This policy grants all AWS IoT resources access to all AWS IoT actions\. This policy is convenient for development and testing purposes, but it is not recommended for production\.

1. Choose **Create**\.

**To create an IoT thing, private key, and certificate for your device**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\.

1. If you do not have any things registered in your account, the **You don't have any things yet** page is displayed\. If you see this page, choose **Register a thing**\. Otherwise, choose **Create**\.

1. On the **Creating AWS IoT things** page, choose **Create a single thing**\.

1. On the **Add your device to the thing registry** page, enter a name for your thing, and then choose **Next**\.

1. On the **Add a certificate for your thing** page, under **One\-click certificate creation**, choose **Create certificate**\.

1. Download your private key and certificate by choosing the **Download** links for each\.

1. Choose **Activate** to activate your certificate\. Certificates must be activated prior to use\.

1. Choose **Attach a policy** to attach a policy to your certificate that grants your device access to AWS IoT operations\.

1. Choose the policy you just created, and then choose **Register thing**\.

After you obtain your certificates and keys from the AWS IoT console, you need to configure the `freertos/tests/include/aws_clientcredential.h` header file so your device can connect to AWS IoT\.

**To configure `freertos/tests/include/aws_clientcredential.h`**

1. Browse to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Settings**\.

   Your AWS IoT endpoint is displayed in **Device data endpoint**\. It should look like `123456789012-ats.iot.us-east-1.amazonaws.com`\. Make a note of this endpoint\.

1. In the navigation pane, choose **Manage**, and then choose **Things**\.

   Your device should have an AWS IoT thing name\. Make a note of this name\.

1. On the computer where you build the FreeRTOS source code, open the `freertos/test/include/aws_clientcredential.h` file in your IDE and specify values for the following constants:
   + `static const char clientcredentialMQTT_BROKER_ENDPOINT[] = "Your AWS IoT endpoint";`
   + `#define clientcredentialIOT_THING_NAME "The AWS IoT thing name of your board"`