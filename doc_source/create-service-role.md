# Create an OTA Update Service Role<a name="create-service-role"></a>

The OTA Update service assumes this role to create and manage OTA update jobs on your behalf\.<a name="create-service-role-steps"></a>

**To create an OTA service role**

1. Sign in to the [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. From the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Under **Select type of trusted entity**, choose **AWS Service**\.

1. Choose **IoT** from the list of AWS services\.

1. Under **Select your use case**, choose **IoT**\.

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. Type a role name and description, and then choose **Create role**\.

For more information about IAM roles, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)\.<a name="add-ota-permissions"></a>

**To add OTA update permissions to your OTA service role**

1. In the search box on the IAM console page, enter the name of your role, and then choose it from the list\.

1. Choose **Attach policies**\.

1. In the **Search** box, enter **AmazonFreeRTOSOTAUpdate**, check **AmazonFreeRTOSOTAUpdate** from the list of filtered policies, and then choose **Attach policy** to attach the policy to your service role\.

1. Follow the same instructions from the previous step to attach the **AWSIoTThingsRegistration** policy to your service role\.<a name="add-iam-permissions"></a>

**To add the required IAM permissions to your OTA service role**

1. In the search box on the IAM console page, enter the name of your role and then choose it from the list\.

1. Choose **Add inline policy**\.

1. Choose the **JSON** tab\.

1. Copy and paste the following policy document into the text box:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
         {
               "Effect": "Allow",
               "Action": [
                   "iam:GetRole",
                   "iam:PassRole"
               ],
               "Resource": "arn:aws:iam::<your_account_id>:role/<your_role_name>"
         }
       ]
   }
   ```

   Make sure that you replace *<your\_account\_id>* with your AWS account ID, and *<your\_role\_name>* with the name of the OTA service role\.

1. Choose **Review policy**\.

1. Enter a name for the policy, and then choose **Create policy**\.<a name="add-s3-permissions"></a>

**To add the required Amazon S3 permissions to your OTA service role**

1. In the search box on the IAM console page, enter the name of your role and then choose it from the list\.

1. Choose **Add inline policy**\.

1. Choose the **JSON** tab\.

1. Copy and paste the following policy document into the box:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "s3:GetObjectVersion",
                   "s3:GetObject",
                   "s3:PutObject"
               ],
               "Resource": "arn:aws:s3:::<example-bucket>/*"
           }
       ]
   }
   ```

   This policy grants your OTA service role permission to read Amazon S3 objects\. Make sure that you replace *<example\-bucket>* with the name of your bucket\.

1. Choose **Review policy**\.

1. Enter a name for the policy, and then choose **Create policy**\.