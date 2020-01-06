# Registering Your MCU Board with AWS IoT<a name="get-started-freertos-thing"></a>

Your board must be registered with AWS IoT to communicate with the AWS Cloud\. To register your board with AWS IoT, you need the following:

An AWS IoT policy  
The AWS IoT policy grants your device permissions to access AWS IoT resources\. It is stored on the AWS Cloud\.

An AWS IoT thing  
An AWS IoT thing allows you to manage your devices in AWS IoT\. It is stored on the AWS Cloud\.

A private key and X\.509 certificate  
The private key and certificate allow your device to authenticate with AWS IoT\. 

If you use the **Quick Connect** workflow in the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), a policy, an AWS IoT thing, and a key and certificate are created for you\. If you use the **Quick Connect** workflow, you can ignore the following procedures\.

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
           "Resource":"arn:aws:iot:<aws-region>:<aws-account-id>:*"
   	}, 
       {
           "Effect": "Allow",
           "Action": "iot:Publish",
           "Resource": "arn:aws:iot:<aws-region>:<aws-account-id>:*"
       },
       {
            "Effect": "Allow",
            "Action": "iot:Subscribe",
            "Resource": "arn:aws:iot:<aws-region>:<aws-account-id>:*"
       },
       {
            "Effect": "Allow",
            "Action": "iot:Receive",
            "Resource": "arn:aws:iot:<aws-region>:<aws-account-id>:*"
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

After your board is registered with AWS IoT, you can continue to [Downloading Amazon FreeRTOS](freertos-download.md)\.