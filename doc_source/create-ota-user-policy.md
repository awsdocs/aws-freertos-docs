# Create an OTA User Policy<a name="create-ota-user-policy"></a>

You must grant your IAM user permission to perform over\-the\-air updates\. Your IAM user must have permissions to:
+ Access the S3 bucket where your firmware updates are stored\.
+ Access certificates stored in AWS Certificate Manager\.
+ Access the AWS IoT Streaming service\.
+ Access Amazon FreeRTOS OTA updates\.
+ Access AWS IoT jobs\.
+ Access IAM\.
+ Access Code Signing for AWS IoT\.
+ List Amazon FreeRTOS hardware platforms\.

To grant your IAM user the required permissions, create an OTA user policy and then attach it to your IAM user\. For more information, see [IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)\.<a name="create-ota-user-policy-steps"></a>

**To create an OTA user policy**

1. Open the [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/) console\.

1. In the navigation pane, choose **Users**\.

1. Choose your IAM user from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. Choose **Create policy**\.

1. Choose the **JSON** tab, and copy and paste the following policy document into the policy editor:

   ```
   {
   	"Version":"2012-10-17",
   	"Statement":[
   	{
   		"Effect":"Allow",
   		"Action":[
   			"s3:ListBucket",
   			"s3:ListAllMyBuckets",
   			"s3:CreateBucket",
   			"s3:PutBucketVersioning",
   			"s3:GetBucketLocation",
   			"s3:GetObjectVersion",
   			"acm:ImportCertificate",
   			"acm:ListCertificates",
   			"iot:*",
   			"iam:ListRoles",
   			"freertos:ListHardwarePlatforms",
   			"freertos:DescribeHardwarePlatform"
   		],
   		"Resource":"*"
   	},
   	{
   	"Effect":"Allow",
   	"Action":[
   		"s3:GetObject",
   		"s3:PutObject"
   	],
   	"Resource":"arn:aws:s3:::<example-bucket>/*"
   	},
   	{
   		"Effect":"Allow",
   		"Action":"iam:PassRole",
   		"Resource":"arn:aws:iam::<your-account-id>:role/<role-name>"
   	}
   	]
   }​
   ```

   Replace *<example\-bucket>* with the name of the Amazon S3 bucket where your OTA update firmware image is stored\. Replace *<your\-account\-id>* with your AWS account ID\. You can find your AWS account ID in the upper right of the console\. When you enter your account ID, remove any dashes \(\-\)\. Replace *<role\-name>* with the name of the IAM service role you just created\. 

1. Choose **Review policy**\.

1. Enter a name for your new OTA user policy, and then choose **Create policy**\.<a name="attach-ota-user-policy"></a>

**To attach the OTA user policy to your IAM user**

1. In the IAM console, in the navigation pane, choose **Users**, and then choose your user\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. Search for the OTA user policy you just created and select the check box next to it\.

1. Choose **Next: Review**\.

1. Choose **Add permissions**\.