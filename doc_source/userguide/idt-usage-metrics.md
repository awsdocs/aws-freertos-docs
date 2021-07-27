# IDT usage metrics<a name="idt-usage-metrics"></a>

If you provide AWS credentials with required permissions, AWS IoT Device Tester collects and submits usage metrics to AWS\. This is an opt\-in feature and is used to improve IDT functionality\. IDT collects information such as the following: 
+ The AWS account ID used to run IDT
+  The IDT CLI commands used to run tests
+ The test suite that are run
+ The test suites in the *<device\-tester\-extract\-location>* folder
+ The number of devices configured in the device pool
+ Test case names and run times
+ Test result information, such as whether tests passed, failed, encountered errors, or were skipped
+ Product features tested
+ IDT exit behavior, such as unexpected or early exits 

 All of the information that IDT sends is also logged to a `metrics.log` file in the `<device-tester-extract-location>/results/<execution-id>/` folder\. You can view the log file to see the information that was collected during a test run\. This file is generated only if you choose to collect usage metrics\. 

To disable metrics collection, you do not need to take additional action\. Simply do not store your AWS credentials, and if you do have stored AWS credentials, do not configure the `config.jso`n file to access them\.

## Configure your AWS credentials<a name="configure-aws-creds-for-metrics"></a>

If you do not already have an AWS account, you must [create one](#idt-metrics-aws-account)\. If you already have an AWS account, you simply need to [configure the required permissions](#idt-metrics-permissions) for your account that allow IDT to send usage metrics to AWS on your behalf\.

### Step 1: Create an AWS account<a name="idt-metrics-aws-account"></a>

In this step, create and configure an AWS account\. If you already have an AWS account, skip to [Step 2: Configure permissions for IDT](#idt-metrics-permissions)\.<a name="create-aws-account-steps"></a>

1. Open the [AWS home page](https://aws.amazon.com/), and choose **Create an AWS Account**\.
**Note**  
If you've signed in to AWS recently, you might see **Sign In to the Console** instead\.

1. Follow the online instructions\. Part of the sign\-up procedure includes registering a credit card, receiving a text message or phone call, and entering a PIN\.

   For more information, see [How do I create and activate a new Amazon Web Services account?](http://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)

### Step 2: Configure permissions for IDT<a name="idt-metrics-permissions"></a>

In this step, configure the permissions that IDT uses to run tests and collect IDT usage data\. You can use the AWS Management Console or AWS Command Line Interface \(AWS CLI\) to create an IAM policy and a user for IDT, and then attach policies to the user\.
+ [To Configure Permissions for IDT \(Console\)](#idt-metrics-permissions-console)
+ [To Configure Permissions for IDT \(AWS CLI\)](#idt-metrics-permissions-cli)<a name="idt-metrics-permissions-console"></a>

**To configure permissions for IDT \(console\)**

Follow these steps to use the console to configure permissions for IDT for FreeRTOS\.

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam)\.

1. Create a customer managed policy that grants permissions to create roles with specific permissions\. 

   1. In the navigation pane, choose **Policies**, and then choose **Create policy**\.

   1. On the **JSON** tab, replace the placeholder content with the following policy\.

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "iot-device-tester:SendMetrics"
                  ],
                  "Resource": "*"
              }
          ]
      }
      ```

   1. Choose **Review policy**\.

   1. For **Name**, enter **IDTUsageMetricsIAMPermissions**\. Under **Summary**, review the permissions granted by your policy\.

   1. Choose **Create policy**\.

1. Create an IAM user and attach permissions to the user\.

   1. Create an IAM user\. Follow steps 1 through 5 in [Creating IAM users \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console) in the *IAM User Guide*\. If you already created an IAM user, skip to the next step\. 

   1. Attach the permissions to your IAM user:

      1. On the **Set permissions ** page, choose **Attach existing policies to user directly**\.

      1. Search for the **IDTUsageMetricsIAMPermissions** policy that you created in the previous step\. Select the check box\.

   1. Choose **Next: Tags**\.

   1. Choose **Next: Review** to view a summary of your choices\.

   1. Choose **Create user**\.

   1. To view the user's access keys \(access key IDs and secret access keys\), choose **Show** next to the password and access key\. To save the access keys, choose **Download\.csv** and save the file to a secure location\. You use this information later to configure your AWS credentials file\.

<a name="idt-metrics-permissions-cli"></a>

**To configure permissions for IDT \(AWS CLI\)**

Follow these steps to use the AWS CLI to configure permissions for IDT for FreeRTOS\.

1. On your computer, install and configure the AWS CLI if it's not already installed\. Follow the steps in [ Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.
**Note**  
The AWS CLI is an open source tool that you can use to interact with AWS services from your command\-line shell\.

1. Create the following customer managed policy that grants permissions to manage IDT and FreeRTOS roles\.

------
#### [ Linux, macOS, or Unix ]

   ```
   aws iam create-policy --policy-name IDTUsageMetricsIAMPermissions --policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "iot-device-tester:SendMetrics"
               ],
               "Resource": "*"
           }
       ]
   }'
   ```

------
#### [ Windows command prompt ]

   ```
   aws iam create-policy --policy-name IDTUsageMetricsIAMPermissions --policy-document
                                           '{\"Version\": \"2012-10-17\", \"Statement\": [{\"Effect\": \"Allow\", \"Action\": [\"iot-device-tester:SendMetrics\"], \"Resource": \"*\"}]}'
   ```

**Note**  
This step includes a Windows command prompt example because it uses a different JSON syntax than Linux, macOS, or Unix terminal commands\.

------

1. Create an IAM user and attach the permissions required by IDT for FreeRTOS\.

   1. Create an IAM user\. 

      ```
      aws iam create-user --user-name user-name
      ```

   1. Attach the `IDTUsageMetricsIAMPermissions` policy you created to your IAM user\. Replace *user\-name* with your IAM user name and *<account\-id>* in the command with the ID of your AWS account\.

      ```
      aws iam attach-user-policy --user-name user-name --policy-arn arn:aws:iam::<account-id>:policy/IDTFreeRTOSIAMPermissions
      ```

1. Create a secret access key for the user\.

   ```
   aws iam create-access-key --user-name user-name
   ```

   Store the output in a secure location\. You use this information later to configure your AWS credentials file\.

## Provide AWS credentials to IDT<a name="idt-metrics-creds"></a>

To allow IDT to access your AWS credentials and submit metrics to AWS, do the following:

1. Store the AWS credentials for your IAM user as environment variables or in a credentials file:

   1. To use environment variables, run the following command:

      ```
      AWS_ACCESS_KEY_ID=access-key
      AWS_SECRET_ACCESS_KEY=secret-access-key
      ```

   1. To use the credentials file, add the following information to the `.aws/credentials file:`

      ```
      [profile-name]
      aws_access_key_id=access-key
      aws_secret_access_key=secret-access-key
      ```

1. Configure the `auth` section of the `config.json` file\. For more information, see [\(Optional\) Configure config\.json](set-config-custom.md#config-json-custom)\.