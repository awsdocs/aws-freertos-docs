# Prerequisites<a name="lts-idt-dev-tester-prereqs"></a>

This section describes the prerequisites for testing microcontrollers with AWS IoT Device Tester\.

## Prepare for FreeRTOS qualification<a name="idt-preparing-qualification"></a>

IDT for FRQ 2\.0 is a qualification for FreeRTOS\. Before running IDT FRQ 2\.0 for qualification, you must complete [Qualifying your board](https://docs.aws.amazon.com/freertos/latest/qualificationguide/freertos-qualification.html) in the *FreeRTOS Qualification Guide*\. To port libraries, tests, and setup the `manifest.yml`, see [Porting the FreeRTOS libraries](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting.html) in the *FreeRTOS Porting Guide*\. FRQ 2\.0 contains a different process for qualification\. See [Latest changes in qualification](https://docs.aws.amazon.com/freertos/latest/qualificationguide/latest-changes.html) in the *FreeRTOS qualification guide* for details\.

**Note**  
IDT is dependent on the tests repository's implementation of `UNITY_OUTPUT_CHAR`\. The test output logs and the device logs must not interleave with each other\. See [ Implementing the library logging macros](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-library-logging-macros.html) section in the *FreeRTOS Porting Guide* for further details\. 

## Download IDT for FreeRTOS<a name="idt-download-dev-tester-afr"></a>

Every version of FreeRTOS has a corresponding version of IDT for FreeRTOS to perform qualification tests\. Download the appropriate version of IDT for FreeRTOS from [ Supported versions of AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/dev-test-versions-afr.html)\.

Extract IDT for FreeRTOS to a location on the file system where you have read and write permissions\. Since Microsoft Windows has a character limit for the path length, extract IDT for FreeRTOS into a root directory such as `C:\` or `D:\`\.

**Note**  
Multiple users must not run IDT from a shared location, such as an NFS directory or a Windows network shared folder\. This will result in crashes or data corruption\. We recommend that you extract the IDT package to a local drive\.

## Create and configure an AWS account<a name="lts-config-aws-account"></a>

**Note**  
The full IDT qualification suite is supported only in the following AWS Regions   
US East \(N\. Virginia\)
 US West \(Oregon\) 
Asia Pacific \(Tokyo\) 
Europe \(Ireland\) 

In order to test your device, IDT for FreeRTOS creates resources like AWS IoT things, FreeRTOS groups and Lambda functions\. To create those resources, IDT for FreeRTOS requires you to create and configure an AWS account, an IAM user account, and an IAM policy that grants IDT for FreeRTOS permission to access resources on your behalf while running tests\.

The following steps are to create and configure your AWS account\.

1. If you already have an AWS account, skip to the next step\. Else create an [AWS account](http://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

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
                  "Action": "iotdeviceadvisor:*",
                  "Resource": [
                      "arn:aws:iotdeviceadvisor:*:*:suiterun/*/*",
                      "arn:aws:iotdeviceadvisor:*:*:suitedefinition/*"
                  ]
              },
              {
                  "Effect": "Allow",
                  "Action": "iam:PassRole",
                  "Resource": "arn:aws:iam::*:role/idt*",
                  "Condition": {
                      "StringEquals": {
                          "iam:PassedToService": "iotdeviceadvisor.amazonaws.com"
                      }
                  }
              },
              {
                  "Effect": "Allow",
                  "Action": [
                      "execute-api:Invoke*",
                      "iam:ListRoles",
                      "iot:Connect",  
                      "iot:CreateJob",
                      "iot:DeleteJob",
                      "iot:DescribeCertificate", 
                      "iot:DescribeEndpoint",
                      "iot:DescribeJobExecution",
                      "iot:DescribeJob",                                 
                      "iot:DescribeThing",
                      "iot:GetPolicy",
                      "iot:ListAttachedPolicies",
                      "iot:ListCertificates",
                      "iot:ListPrincipalPolicies",
                      "iot:ListThingPrincipals",
                      "iot:ListThings",
                      "iot:Publish",    
                      "iot:UpdateThingShadow",                
                      "logs:CreateLogGroup",
                      "logs:CreateLogStream",
                      "logs:DescribeLogGroups",
                      "logs:DescribeLogStreams",
                      "logs:PutLogEvents",
                      "logs:PutRetentionPolicy"
                  ],
                  "Resource": "*"
              },
              {
                  "Effect": "Allow",
                  "Action": "iotdeviceadvisor:*",
                  "Resource": "*"
              },
              {
                  "Effect": "Allow",
                  "Action": "logs:DeleteLogGroup",
                  "Resource": "arn:aws:logs:*:*:log-group:/aws/iot/deviceadvisor/*"
              },
              {
                  "Effect": "Allow",
                  "Action": "logs:GetLogEvents",
                  "Resource": "arn:aws:logs:*:*:log-group:/aws/iot/deviceadvisor/*:log-stream:*"
              },
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
              },
              {
                  "Effect": "Allow",
                  "Action": [
                      "ssm:GetParameters"
                  ],
                  "Resource": [
                      "arn:aws:ssm:*::parameter/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
                  ]
              },
              {
                  "Effect": "Allow",
                  "Action": [
                      "ec2:DescribeInstances",
                      "ec2:RunInstances",
                      "ec2:CreateSecurityGroup",
                      "ec2:CreateTags",
                      "ec2:DeleteTags"
                  ],
                  "Resource": [
                      "*"
                  ]
              },
              {
                  "Effect": "Allow",
                  "Action": [
                      "ec2:CreateKeyPair",
                      "ec2:DeleteKeyPair"
                  ],
                  "Resource": [
                      "arn:aws:ec2:*:*:key-pair/idt-ec2-ssh-key-*"
                  ]
              },
              {
                  "Effect": "Allow",
                  "Condition": {
                      "StringEqualsIgnoreCase": {
                          "aws:ResourceTag/Owner": "IoTDeviceTester"
                      }
                  },
                  "Action": [
                      "ec2:TerminateInstances",
                      "ec2:DeleteSecurityGroup",
                      "ec2:AuthorizeSecurityGroupIngress",
                      "ec2:RevokeSecurityGroupIngress"
                  ],
                  "Resource": [
                      "*"
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
              },
              {
                  "Effect": "Allow",
                  "Action": [
                      "ssm:GetParameters"
                  ],
                  "Resource": [
                      "arn:aws-cn:ssm:*::parameter/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
                  ]
              },
              {
                  "Effect": "Allow",
                  "Action": [
                      "ec2:DescribeInstances",
                      "ec2:RunInstances",
                      "ec2:CreateSecurityGroup",
                      "ec2:CreateTags",
                      "ec2:DeleteTags"
                  ],
                  "Resource": [
                      "*"
                  ]
              },
              {
                  "Effect": "Allow",
                  "Action": [
                      "ec2:CreateKeyPair",
                      "ec2:DeleteKeyPair"
                  ],
                  "Resource": [
                      "arn:aws-cn:ec2:*:*:key-pair/idt-ec2-ssh-key-*"
                  ]
              },
              {
                  "Effect": "Allow",
                  "Condition": {
                      "StringEqualsIgnoreCase": {
                          "aws-cn:ResourceTag/Owner": "IoTDeviceTester"
                      }
                  },
                  "Action": [
                      "ec2:TerminateInstances",
                      "ec2:DeleteSecurityGroup",
                      "ec2:AuthorizeSecurityGroupIngress",
                      "ec2:RevokeSecurityGroupIngress"
                  ],
                  "Resource": [
                      "*"
                  ]
              }       
          ]
      }
      ```

------

   1. When you're finished, choose **Review policy**\.

   1. On the **Review** page, enter `IDTFreeRTOSIAMandEC2Permissions ` for the policy name\. Review the policy **Summary** to verify the permissions granted by your policy\.

   1. Choose **Create policy**\.

1. Create an IAM user with the necessary permissions to run AWS IoT Device Tester\. 

   1. Follow steps 1 through 5 in [Creating IAM Users \(Console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console)\.

   1. To attach the necessary permissions to your IAM user:

      1. On the **Set permissions** page, choose **Attach existing policies to user directly**\.

      1. Search for the **IDTFreeRTOSIAMandEC2Permissions** policy that you created in step 2\. Select the check box\.

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

You might prefer to use the AWS CLI to perform some operations\. If you don't have the AWS CLI installed, follow the instructions at [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

Configure the AWS CLI for the AWS Region you want to use by running aws configure from a command line\. For information about the AWS Regions that support IDT for FreeRTOS, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#amazon-freertos-ota-control)\. For more information about aws configure see [ Quick configuration with aws configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config)\.