# Permissions Policy Template<a name="policy-template"></a>

The following is a policy template that grants the permissions required for IDT for Amazon FreeRTOS to run tests\.

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
    }
  ]
}
```