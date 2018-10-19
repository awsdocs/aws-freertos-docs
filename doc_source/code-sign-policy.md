# Granting Access to Code Signing for Amazon FreeRTOS<a name="code-sign-policy"></a>

In production environments, you should digitally sign your firmware update to ensure the authenticity and integrity of the update\. You can sign your update manually or you can use Code Signing for Amazon FreeRTOS to sign your code\. To use Code Signing for Amazon FreeRTOS, you must grant your IAM user account access to Code Signing for Amazon FreeRTOS\.<a name="grant-user-account-code-signing-permissions"></a>

**To grant your IAM user account permissions for Code Signing for Amazon FreeRTOS**

1. Sign in to the [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create Policy**\.

1. On the **JSON** tab, copy and paste the following JSON document into the policy editor\. This policy allows the IAM user access to all code\-signing operations\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "signer:*"
         ],
         "Resource": [
           "*"
         ]
       }
     ]
   }
   ```

1. Choose **Review policy**\.

1. Enter a policy name and description, and then choose **Create policy**\.

1. In the navigation pane, choose **Users**\.

1. Choose your IAM user account\.

1. On the **Permissions** tab, choose **Add permissions**\.

1. Choose **Attach existing policies directly**, and then select the check box next to the code\-signing policy you just created\.

1. Choose **Next: Review**\.

1. Choose **Add permissions**\.