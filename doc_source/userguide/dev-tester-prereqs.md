# Prerequisites<a name="dev-tester-prereqs"></a>

This section describes the prerequisites for testing microcontrollers with AWS IoT Device Tester\.

## Download FreeRTOS<a name="download-afr"></a>

You can download a release of FreeRTOS from [GitHub](https://github.com/aws/amazon-freertos) with the following command:

```
git clone --branch <FREERTOS_RELEASE_VERSION> --recurse-submodules https://github.com/aws/amazon-freertos.git
```

where <FREERTOS\_RELEASE\_VERSION> is a version of FreeRTOS \(for example, 202007\.00\) corresponding to an IDT version listed in [Supported versions of AWS IoT Device Tester for FreeRTOS](dev-test-versions-afr.md)\. This ensures you have the full source code, including submodules, and are using the correct version of IDT for your version of FreeRTOS, and vice versa\.

Windows has a path length limitation of 260 characters\. The path structure of FreeRTOS is many levels deep, so if you are using Windows, keep your file paths under the 260\-character limit\. For example, clone FreeRTOS to `C:\FreeRTOS` rather than `C:\Users\username\programs\projects\myproj\FreeRTOS\`\.

## Download IDT for FreeRTOS<a name="download-dev-tester-afr"></a>

Every version of FreeRTOS has a corresponding version of IDT for FreeRTOS to perform qualification tests\. Download the appropriate version of IDT for FreeRTOS from [Supported versions of AWS IoT Device Tester for FreeRTOS](dev-test-versions-afr.md)\.

Extract IDT for FreeRTOS to a location on the file system where you have read and write permissions\. Because Microsoft Windows has a character limit for the path length, extract IDT for FreeRTOS into a root directory such as `C:\` or `D:\`\.

**Note**  
We don't recommend that multiple users run IDT from a shared location, such as an NFS directory or a Windows network shared folder\. This may result in crashes or data corruption\. We recommend that you extract the IDT package to a local drive\.

## Create and configure an AWS account<a name="config-aws-account"></a>

Follow these steps to create and configure an AWS account, an IAM user, and an IAM policy that grants IDT for FreeRTOS permission to access resources on your behalf while running tests\.

1. If you already have an AWS account, skip to the next step\. Create an [AWS account](http://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

1. Create an IAM policy that grants IDT for FreeRTOS the IAM permissions to create service roles with specific permissions\. 

   1. Sign in to the [IAM console](https://console.aws.amazon.com/iam)\.

   1. In the navigation pane, choose **Policies**\.

   1. In the content pane, choose **Create policy**\.

   1. Choose the **JSON** tab and copy the following permissions in to the **JSON** text box\.
**Important**  
The following policy template grants IDT permission to create roles, create policies, and attach policies to roles\. IDT for FreeRTOS uses these permissions for tests that create roles\. Although the policy template doesn't provide administrator privileges to the user, the permissions could potentially be used to gain administrator access to your AWS account\.

------
#### [ Most Regions ]

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "iam:CreatePolicy",
                      "iam:DetachRolePolicy",
                      "iam:DeleteRolePolicy",
                      "iam:DeletePolicy",
                      "iam:CreateRole",
                      "iam:DeleteRole",
                      "iam:AttachRolePolicy"
                  ],
                  "Resource": [
                      "arn:aws:iam::*:policy/idt*",
                      "arn:aws:iam::*:role/idt*"
                  ]
              }
          ]
      }
      ```

------
#### [ Beijing and Ningxia Regions ]

      The following policy template can be used in the Beijing and Ningxia Regions\.

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "iam:CreatePolicy",
                      "iam:DetachRolePolicy",
                      "iam:DeleteRolePolicy",
                      "iam:DeletePolicy",
                      "iam:CreateRole",
                      "iam:DeleteRole",
                      "iam:AttachRolePolicy"
                  ],
                  "Resource": [
                      "arn:aws-cn:iam::*:policy/idt*",
                      "arn:aws-cn:iam::*:role/idt*"
                  ]
              }
          ]
      }
      ```

------

   1. When you're finished, choose **Review policy**\.

   1. On the **Review** page, enter `IDTFreeRTOSIAMPermissions` for the policy name\. Review the policy **Summary** to verify the permissions granted by your policy\.

   1. Choose **Create policy**\.

1. Create an IAM user with the necessary permissions to run AWS IoT Device Tester\. 

   1. Follow steps 1 through 5 in [Creating IAM Users \(Console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console)\.

   1. To attach the necessary permissions to your IAM user:

      1. On the **Set permissions** page, choose **Attach existing policies to user directly**\.

      1. Search for the **IDTFreeRTOSIAMPermissions** policy that you created in step 2\. Select the check box\.

      1. Search for the **AWSIoTDeviceTesterForFreeRTOSFullAccess** policy\. Select the check box\.

   1. Choose **Next: Tags**\.

   1. Choose **Next: Review** to view a summary of your choices\.

   1. Choose **Create user**\.

   1. To view the users' access keys \(access key IDs and secret access keys\), choose **Show** next to each password and access key and then choose **Download\.csv**\. Save the file to a safe location\.

## AWS IoT Device Tester managed policy<a name="managed-policy"></a>

The `AWSIoTDeviceTesterForFreeRTOSFullAccess` managed policy contains the following permissions to enable device tester to execute and to collect metrics:
+ `iot-device-tester:SupportedVersion`

  Grants permission to get the list of FreeRTOS versions and test suite versions supported by IDT, so that they are available from the AWS CLI\.
+ `iot-device-tester:LatestIdt`

  Grants permission to get the latest AWS IoT Device Tester version that is available for download\.
+ `iot-device-tester:CheckVersion`

  Grants permission to check that a combination of product, test suite, and AWS IoT Device Tester versions are compatible\.
+ `iot-device-tester:DownloadTestSuite`

  Grants permission to AWS IoT Device Tester to download test suites\.
+ `iot-device-tester:SendMetrics`

  Grants permission to publish AWS IoT Device Tester usage metrics data\.

## \(Optional\) Install the AWS Command Line Interface<a name="install-cli"></a>

You might prefer to use the AWS CLI to perform some operations\. If you don't have the AWS CLI installed, follow the instructions in [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

Configure the CLI for the AWS Region you want to use by running `aws configure` from a command line\. For information about the AWS Regions that support IDT for FreeRTOS, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#amazon-freertos-ota-control)\. For more information about `aws configure` see [ Quick configuration with `aws configure`](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config)\.