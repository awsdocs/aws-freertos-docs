# Using AWS IoT Device Tester for Amazon FreeRTOS<a name="device-tester-for-freertos-ug"></a>

AWS IoT Device Tester \(IDT\) allows you to test that the Amazon FreeRTOS operating system works locally on your device and can communicate with the AWS IoT cloud\. AWS IoT Device Tester checks if the porting layer interfaces for Amazon FreeRTOS libraries function correctly on top of microcontroller board device drivers\. In addition, it performs end\-to\-end tests with AWS IoT Core \(for example, to test if the board is able to send or receive MQTT messages and process correctly\)\. AWS IoT Device Tester for Amazon FreeRTOS uses the test cases published in the [Amazon FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\. AWS IoT Device Tester consists of a Test Manager command\-line tool and a set of test cases\.

Test Manager runs on a host computer \(Windows, Mac, or Linux\) that is connected to the device to be tested\. The Test Manager executes test cases and aggregates results\. It also provides a command line interface to manage test execution\. Test cases contain test logic and set up the resources required for tests\.

Test cases are part of the application binary image that is flashed onto your board\. Application binary images include Amazon FreeRTOS, the semiconductor vendor’s ported Amazon FreeRTOS interfaces, and board device drivers\. Test Cases run as embedded applications and verify the ported Amazon FreeRTOS interfaces function correctly on top of the device drivers\.

The following diagram shows the test infrastructure setup:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/devicetester_afr.png)

## AWS IoT Device Tester for Amazon FreeRTOS Versions<a name="dev-test-versions-afr"></a>

The following tabs describe the versions of AWS IoT Device Tester for Amazon FreeRTOS\. By downloading the software you agree to the [AWS IoT Device Tester License agreement](https://d232ctwt5kahio.cloudfront.net/greengrass/AWS%20IoT%20Device%20Tester%20License%20Agreement.pdf)\. Each version of Amazon FreeRTOS has a corresponding version of AWS IoT Device Tester\. Newer versions of AWS IoT Device Tester will be added to this page with each new release of Amazon FreeRTOS\. Please refer to the [ AWS IoT Device Tester for Amazon FreeRTOS User Guide](https://docs.aws.amazon.com/freertos/latest/userguide/what-is-amazon-freertos.html) for more details about AWS IoT Device Tester for Amazon FreeRTOS\.

------
#### [ IDT for Amazon FreeRTOS v1\.4 ]

**IDT for Amazon FreeRTOS v1\.4\.6 \- Current Version**
+ IDT for Amazon FreeRTOS \- [ Linux](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_linux_1.0.190204214205.zip) \(MD5 bb4290f556b7c828ec9621c7229b1d6d\)
+ IDT for Amazon FreeRTOS \- [ Mac](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_mac_1.0.190204214205.zip) \(MD5 d5f62d6c1a253ef1fb0c70f2a87836fc\)
+ IDT for Amazon FreeRTOS \- [ Windows]( https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_win_1.0.190204214205.zip) \(MD5 9be81e92e7b1c5a85f8a4d433fc2bb43\)

**IDT for Amazon FreeRTOS v1\.4\.5**
+ IDT for Amazon FreeRTOS \- [ Linux](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_linux_1.0.190122221640.zip) \(MD5 ee7b167e4e1cc02c256b5c3edc0a8abe\)
+ IDT for Amazon FreeRTOS \- [ Mac](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_mac_1.0.190122221640.zip) \(MD5 791580fa714ef1a01c3c6bc32c2ff448\)
+ IDT for Amazon FreeRTOS \- [ Windows](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_win_1.0.190122221640.zip) \(MD5 ba744c23f6276bba928775bc024ec108\)

**IDT for Amazon FreeRTOS v1\.4\.4**
+ IDT for Amazon FreeRTOS \- [ Linux](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_linux_1.0.181213003249.zip) \(MD5 64613157503fc55ce581310f819d6b3c\)
+ IDT for Amazon FreeRTOS \- [ Mac](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_mac_1.0.181213003249.zip) \(MD5 b15840ab9dc14f076ce36cb9d111070f\)
+ IDT for Amazon FreeRTOS \- [ Windows](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_win_1.0.181213003249.zip) \(MD5 b84178c6cf98b376f50c360eb7a5208f\)

**IDT for Amazon FreeRTOS v1\.4\.3**
+ IDT for Amazon FreeRTOS \- [ Linux](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_linux_1.0.181211220033.zip) \(MD5 49946aec8634d20d38c449f39ac58fef\)
+ IDT for Amazon FreeRTOS \- [ Mac](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_mac_1.0.181211220033.zip) \(MD5 c8cc6c388a35482ea50f1693b40b5e72\)
+ IDT for Amazon FreeRTOS \- [ Windows](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_win_1.0.181211220033.zip) MD5 a1ca9101729bc41ad29fc32458c8920d\)

**IDT for Amazon FreeRTOS v1\.4\.2**
+ IDT for Amazon FreeRTOS \- [ Linux](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_linux_1.0.181119201633.zip) \(MD5 eb9cfd1ae803d309f8d8b6698e40ae96\)
+ IDT for Amazon FreeRTOS \- [ Mac](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_mac_1.0.181119201633.zip) \(MD5 10cb6c8aa2a54f7a0d26095f18753c2f\)
+ IDT for Amazon FreeRTOS \- [ Windows](https://d232ctwt5kahio.cloudfront.net/afr/devicetester_afreertos_win_1.0.181119201633.zip) \(MD5 57ac5703924e3a2e2f039e97baaca44f\)

------

## Prerequisites<a name="dev-tester-prereqs"></a>

This section describes the prerequisites for testing microcontrollers with AWS IoT Device Tester\.

### Download Amazon FreeRTOS<a name="download-afr"></a>

You can download the version of Amazon FreeRTOS that you want to test from [GitHub](https://github.com/aws/amazon-freertos)\. If you are using Windows, you must keep the file path short\. For example, to avoid a Windows limitation with long file paths, clone to `C:\AFreeRTOS` rather than `C:\Users\username\programs\projects\AmazonFreeRTOS\`\.

### Download AWS IoT Device Tester for Amazon FreeRTOS<a name="download-dev-tester-afr"></a>

Every version of Amazon FreeRTOS has a corresponding version of AWS IoT Device Tester for performing qualification tests\. Download the appropriate version of AWS IoT Device Tester from [AWS IoT Device Tester for Amazon FreeRTOS Versions](#dev-test-versions-afr)\.

Extract AWS IoT Device Tester into a location on the file system where you have read and write permissions\. Due to a path length limitation, on Microsoft Windows, extract AWS IoT Device Tester into a root directory like C:\\ or D:\\\.

### Create and Configure an AWS Account<a name="config-aws-account"></a>

If you don't have an AWS account, follow the instructions on the [AWS webpage](https://aws.amazon.com) to create one\. Choose **Create an AWS Account** and follow the prompts\.

#### Create an IAM User in Your AWS Account<a name="create-iam-user-afr"></a>

When you create an AWS account, a root user that has access to all resources in your account is created for you\. It is a best practice to create another user for everyday tasks\. To create an IAM user, follow the instructions in [Creating an IAM User in Your AWS Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)\. For more information about the root user, see [The AWS Account Root User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)\.

#### Create and Attach an IAM Policy to Your AWS Account<a name="create-policy-bk-afr"></a>

IAM policies grant your IAM user access to AWS resources\. 

**To create an IAM policy**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, and then choose **Create Policy**\.

1. Select the **JSON** tab and copy and paste the policy template located in [Permissions Policy Template](policy-template.md) the [](policy-template.md) into the editor window\.

1. Choose **Review policy**\.

1. In **Name**, enter a name for your policy\. In **Description**, enter an optional description\. Choose **Create Policy**\.

After you create an IAM policy, you must attach it to your IAM user\.

**To attach an IAM policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\. Find and select your IAM user\.

1. Choose **Add permissions**, and then choose **Attach existing policies directly**\. Find and select your IAM policy, choose **Next: Review**, and then choose **Add Permissions**\.

### Install the AWS Command Line Interface \(CLI\)<a name="install-cli"></a>

 You will need to use the CLI to perform some operations, if you don't have the CLI installed, follow the instructions in [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.