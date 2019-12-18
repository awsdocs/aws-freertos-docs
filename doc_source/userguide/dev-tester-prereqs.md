# Prerequisites<a name="dev-tester-prereqs"></a>

This section describes the prerequisites for testing microcontrollers with AWS IoT Device Tester\.

## Download Amazon FreeRTOS<a name="download-afr"></a>

You can download the version of Amazon FreeRTOS that you want to test from [GitHub](https://github.com/aws/amazon-freertos)\. Windows has a path length limitation of 260 characters\. The path structure of Amazon FreeRTOS is many levels deep, so if you are using Windows, keep your file paths under the 260\-character limit\. For example, clone Amazon FreeRTOS to `C:\AFreeRTOS` rather than `C:\Users\username\programs\projects\AmazonFreeRTOS\`\.

## Download IDT for Amazon FreeRTOS<a name="download-dev-tester-afr"></a>

Every version of Amazon FreeRTOS has a corresponding version of IDT for Amazon FreeRTOS for performing qualification tests\. Download the appropriate version of IDT for Amazon FreeRTOS from [Download IDT v1\.5\.1 for Amazon FreeRTOS 201910\.00](dev-test-versions-afr.md)\.

Extract IDT for Amazon FreeRTOS into a location on the file system where you have read and write permissions\. Due to a path length limitation, on Microsoft Windows, extract IDT for Amazon FreeRTOS into a root directory like `C:\` or `D:\`\.

## Create and Configure an AWS Account<a name="config-aws-account"></a>

Follow these steps to create and configure an AWS account, an IAM user, and an IAM policy that grants IDT for Amazon FreeRTOS permission to access resources on your behalf while running tests\.

1. [Create an AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

1. [Create an IAM policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) that grants IDT for Amazon FreeRTOS the permissions it needs to run the tests and collect IDT usage data\. Use the policy templates described in [Permissions Policy Template](#policy-template)\.

1. [Create an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) in your AWS account and attach the policy you just created\.

### Permissions Policy Template<a name="policy-template"></a>

The following is a policy template that grants the permissions required for IDT for Amazon FreeRTOS to run tests\.

**Important**  
The following policy template grants permission to create roles, create policies, and attach policies to roles\. IDT for Amazon FreeRTOS uses these permissions for tests that need to create roles\. Although the policy template does not provide administrator privileges to the user, the permissions can be potentially used to gain administrator access to your AWS account\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": [
        "iam:CreatePolicy",
        "iam:DetachRolePolicy",
        "iam:DeleteRolePolicy",
        "s3:CreateBucket",
        "iam:DeletePolicy",
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:AttachRolePolicy",
        "s3:DeleteBucket",
        "s3:PutBucketVersioning"
      ],
      "Resource": [
        "arn:aws:s3:::idt*",
        "arn:aws:s3:::afr-ota*",
        "arn:aws:iam::*:policy/idt*",
        "arn:aws:iam::*:role/idt*"
      ]
    },
    {
      "Sid": "VisualEditor1",
      "Effect": "Allow",
      "Action": [
        "iot:DeleteCertificate",
        "iot:AttachPolicy",
        "iot:DetachPolicy",
        "s3:DeleteObjectVersion",
        "iot:DeleteOTAUpdate",
        "s3:PutObject",
        "s3:GetObject",
        "iam:PassRole",
        "iot:DeleteStream",
        "iot:DeletePolicy",
        "iot:UpdateCertificate",
        "iot:GetOTAUpdate",
        "s3:DeleteObject",
        "iot:DescribeJobExecution",
        "s3:GetObjectVersion"
      ],
      "Resource": [
        "arn:aws:iot:*:*:thinggroup/idt*",
        "arn:aws:iot:*:*:policy/idt*",
        "arn:aws:iot:*:*:otaupdate/idt*",
        "arn:aws:iot:*:*:thing/idt*",
        "arn:aws:iot:*:*:cert/*",
        "arn:aws:iot:*:*:job/*",
        "arn:aws:iot:*:*:stream/*",
        "arn:aws:iam::*:role/idt*",
        "arn:aws:s3:::afr-ota*/*",
        "arn:aws:s3:::idt*/*",
        "arn:aws:iam:::role/idt*"
      ]
    },
    {
      "Sid": "VisualEditor2",
      "Effect": "Allow",
      "Action": [
        "iot:DetachThingPrincipal",
        "iot:AttachThingPrincipal",
        "s3:ListBucketVersions",
        "iot:CreatePolicy",
        "iam:ListRoles",
        "freertos:ListHardwarePlatforms",
        "signer:DescribeSigningJob",
        "s3:ListBucket",
        "signer:*",
        "iot:DescribeEndpoint",
        "iot:CreateStream",
        "signer:StartSigningJob",
        "s3:ListAllMyBuckets",
        "signer:ListSigningJobs",
        "acm:GetCertificate",
        "acm:ListCertificates",
        "acm:ImportCertificate",
        "freertos:DescribeHardwarePlatform",
        "iot:CreateKeysAndCertificate",
        "iot:CreateCertificateFromCsr",
        "s3:GetBucketLocation",
        "iot:GetRegistrationCode",
        "iot:RegisterCACertificate",
        "iot:RegisterCertificate",
        "iot:UpdateCACertificate",
        "iot:DeleteCACertificate",
        "iot:DeleteCertificate",
        "iot:UpdateCertificate"
      ],
      "Resource": "*"
    },
    {
      "Sid": "VisualEditor3",
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3:::afr*/*",
        "arn:aws:s3:::idt*/*"
      ]
    },
    {
      "Sid": "VisualEditor4",
      "Effect": "Allow",
      "Action": [
        "iot:CreateOTAUpdate",
        "iot:CreateThing",
        "iot:DeleteThing"
      ],
      "Resource": "*"
    },
    {
      "Sid": "VisualEditor5",
      "Effect": "Allow",
      "Action": [
          "execute-api:Invoke"
      ],
      "Resource": [
          "arn:aws:execute-api:*:098862408343:*"
      ]
    }
  ]
}
```

## \(Optional\) Install the AWS Command Line Interface \(CLI\)<a name="install-cli"></a>

You might prefer to use the CLI to perform some operations\. If you don't have the CLI installed, follow the instructions in [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

Configure the CLI for the AWS Region you want to use by running aws configure from a command line\. For information about the AWS Regions that support IDT for Amazon FreeRTOS, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#amazon-freertos-ota-control)\.