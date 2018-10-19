# Creating an OTA Update Service Role<a name="create-service-role"></a>

The OTA Update service assumes this role to create and manage OTA update jobs on your behalf\.<a name="create-service-role"></a>

**To create an OTA service role**

1. Sign in to the [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. From the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Under **Select type of trusted entity**, choose **AWS Service**\.

1. Choose **IoT** from the list of AWS services\.

1. Under **Select your use case**, choose **IoT allows IoT to call AWS services on your behalf**\.

1. Choose **Next: Permissions**\.

1. Choose **Next: Review**\.

1. Type a role name and description, and then choose **Create role**\.

For more information about IAM roles, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)\.<a name="add-ota-permissions"></a>

**To add OTA update permissions to your OTA service role**

1. In the search box on the IAM console page, enter the name of your role, and then choose it from the list\.

1. Choose **Attach policy**\.

1. In the **Search** box, enter **AmazonFreeRTOSOTAUpdate**\. In the list of managed policies, select **AmazonFreeRTOSOTAUpdate** , and then choose **Attach policy**\.<a name="add--permissions"></a>

**To add the required permissions to your OTA service role**

1. In the search box on the IAM console page, enter the name of your role and then choose it from the list\.

1. In the lower right, choose **Add inline policy**\.

1. Choose the **JSON** tab\.

1. Copy and paste the following policy document into the text box\. Replace *<example\-bucket>* with the name of your bucket\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "iam:PassRole",
               "Resource":
               "arn:aws:iam::<your_account_id>:role/<your_role_name>"
           }
       ]
   }
   ```

   If you provide your own bucket name, use the following policy to grant your service role access to your bucket:

   ```
   	"Version": "2012-10-17",
   	"Statement": [
   		{
   			"Effect": "Allow",
   			"Action": [
   				"s3:GetObjectVersion",
   				"s3:GetObject"
   			],
   			"Resource": "arn:aws:s3:::<example-bucket>/*"
   		}
   	]
   ```

1. Choose **Review policy**\.

1. Enter a name for the policy and then choose **Create policy**\.