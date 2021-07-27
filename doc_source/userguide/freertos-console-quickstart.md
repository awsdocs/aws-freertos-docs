# First steps with the console Quick Connect workflow<a name="freertos-console-quickstart"></a>

**Note**  
Configurations of FreeRTOS are currently not available on the FreeRTOS console for the following boards:  
Cypress CYW943907AEVAL1F Development Kit
Cypress CYW954907AEVAL1F Development Kit

To get started using FreeRTOS with AWS IoT, you must have an AWS account, and an IAM user with permission to access AWS IoT and FreeRTOS cloud services\. Then you can use the Quick Connect workflow in the FreeRTOS console to download FreeRTOS, configure the FreeRTOS demos for your board, and register your board with AWS IoT so it can communicate with the AWS Cloud\. The following sections walk you through these requirements\.

1. [Set up your AWS account and permissions](#freertos-console-quickstart-account-and-permissions) 

1. [Download and configure FreeRTOS and register your MCU board with AWS IoT](#freertos-console-quickstart-download)

## Set up your AWS account and permissions<a name="freertos-console-quickstart-account-and-permissions"></a>

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

## Download and configure FreeRTOS and register your MCU board with AWS IoT<a name="freertos-console-quickstart-download"></a>

Open the [FreeRTOS console](https://console.aws.amazon.com/freertos) and choose the **Quick Connect** workflow for your MCU board\. This will download FreeRTOS, configure the FreeRTOS demos, and register your board with AWS IoT so it can communicate with the AWS Cloud\. The Quick Connect workflow will create the following:

**An AWS IoT policy**  
The AWS IoT policy grants your device permissions to access AWS IoT resources\. It is stored on the AWS Cloud\.

**An AWS IoT thing**  
An AWS IoT thing allows you to manage your devices in AWS IoT\. It is stored on the AWS Cloud\.

**A private key and X\.509 certificate**  
The private key and certificate allow your device to authenticate with AWS IoT\. 

After you configure FreeRTOS, you can continue to the Getting Started guide for your board to compile and run the FreeRTOS demo\. For board\-specific instructions, see the [Board\-specific getting started guides](getting-started-guides.md)\. The demo application that is used in the Getting Started tutorial is the coreMQTT Agent demo, which is located at `demos/coreMQTT_Agent/mqtt_agent_task.c`\.