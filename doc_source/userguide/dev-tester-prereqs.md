# Prerequisites<a name="dev-tester-prereqs"></a>

This section describes the prerequisites for testing microcontrollers with AWS IoT Device Tester\.

## Download Amazon FreeRTOS<a name="download-afr"></a>

You can download the version of Amazon FreeRTOS that you want to test from [GitHub](https://github.com/aws/amazon-freertos)\. Windows has a path length limitation of 260 characters\. The path structure of Amazon FreeRTOS is many levels deep, so if you are using Windows, keep your file paths under the 260\-character limit\. For example, clone Amazon FreeRTOS to `C:\AFreeRTOS` rather than `C:\Users\username\programs\projects\AmazonFreeRTOS\`\.

## Download IDT for Amazon FreeRTOS<a name="download-dev-tester-afr"></a>

Every version of Amazon FreeRTOS has a corresponding version of IDT for Amazon FreeRTOS for performing qualification tests\. Download the appropriate version of IDT for Amazon FreeRTOS from [Download IDT v1\.2 for Amazon FreeRTOS 1\.4\.8](dev-test-versions-afr.md)\.

Extract IDT for Amazon FreeRTOS into a location on the file system where you have read and write permissions\. Due to a path length limitation, on Microsoft Windows, extract IDT for Amazon FreeRTOS into a root directory like `C:\` or `D:\`\.

## Create and Configure an AWS Account<a name="config-aws-account"></a>

Follow these steps to create and configure an AWS account, an IAM user, and an IAM policy that grants IDT for Amazon FreeRTOS permission to access resources on your behalf while running tests\.

1. [Create an AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

1. [Create an IAM policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) that grants IDT for Amazon FreeRTOS the permissions it needs to run the tests\. Use the policy templates described in [Permissions Policy Template](policy-template.md)\.

1. [Create an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) in your AWS account and attach the policy you just created\.

## \(Optional\) Install the AWS Command Line Interface \(CLI\)<a name="install-cli"></a>

You might prefer to use the CLI to perform some operations\. If you don't have the CLI installed, follow the instructions in [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

Configure the CLI for the AWS Region you want to use by running aws configure from a command line\. For information about the AWS Regions that support IDT for Amazon FreeRTOS, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#amazon-freertos-ota-control)\.