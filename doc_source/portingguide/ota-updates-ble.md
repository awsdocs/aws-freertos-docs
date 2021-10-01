# Perform Over the Air Updates using Bluetooth Low Energy<a name="ota-updates-ble"></a>

## Introduction<a name="ota-updates-ble-intro"></a>

You can use the FreeRTOS Bluetooth Low Energy \(BLE\) and Over the Air Updates \(OTA\) libraries to perform a firmware update to a microcontroller device over Bluetooth Low Energy via an MQTT proxy running on a companion device\. The update is performed using AWS IoT OTA jobs\. The companion device connects to AWS IoT using Amazon Cognito credentials entered in a demo app\. An authorized operator initiates the OTA update from the cloud\. When the device connects through the demo app, the OTA update is initiated and the firmware is updated on the device\.

## Prerequisites<a name="ota-updates-ble-prereqs"></a>

1. Port the Over The Air Updates agent to each microcontroller device:
   + Follow the steps in [Platform porting](afr-porting-ota.md#porting-steps-ota) and [IoT device bootloader](afr-porting-ota.md#afr-bootloader)\.
   + After porting, follow the steps in [Testing](afr-porting-ota.md#porting-testing-ota) to verify that the OTA PAL tests pass\.

1. Port the Bluetooth Low Energy library to each microcontroller:
   + Follow the steps in [Porting the Bluetooth Low Energy library](afr-porting-ble.md)\.

1. Set up an AWS account if you don't already have one \(the Free Tier is sufficient\)\.

1. You must have access to an Android phone as the companion device with Android v 6\.0 or later and Bluetooth version 4\.2 or later\.

1. Set up your development platform with:
   + Android Studio\.
   + The AWS CLI installed\.
   + Python3 installed\.
   + The boto3 AWS Software Developer Kit \(SDK\) for Python\.

## Setup<a name="ota-updates-ble-setup"></a>

The following is an overview of the steps required for setup\. You can skip steps 1 and 2 if you have already done these to test Over The Air Updates over WiFi or Ethernet\.

1. Configure storage— Create an S3 bucket and policies and configure an IAM user that can perform updates\.

1. Create a code\-signing certificate— Create a signing certificate and allow the IAM user to sign firmware updates\.

1. Configure Amazon Cognito authentication— Create a credential provider, user pool, and application access to the user pool\.

1. Configure Amazon FreeRTOS— Set up Bluetooth Low Energy, client credentials, and the code\-signing public certificate\.

1. Configure an Android app— Set up the credential provider and user pool, then deploy the application to an Android device\.

### Step 1: Configure storage<a name="ota-updates-ble-config-storage"></a>

You can skip these steps by launching the [AWS CloudFormation template](https://github.com/aws-samples/amazon-freertos-ota-scripts/blob/master/templates/ota-ble.cform.json)\.

1. [Create an S3 bucket](https://docs.aws.amazon.com/freertos/latest/userguide/dg-ota-bucket.html) with versioning enabled to hold the firmware images\.

1. [Create an OTA update service role](https://docs.aws.amazon.com/freertos/latest/userguide/create-service-role.html) and add the following managed policies to the role:
   + AWSIotLogging
   + AWSIotRuleActions
   + AWSIotThingsRegistration
   + AWSFreeRTOSOTAUpdate

1. [Add an inline policy](https://docs.aws.amazon.com/freertos/latest/userguide/create-ota-user-policy.html) to the role to allow it to perform AWS IoT actions and allow access to the S3 bucket that you created\.

1. [Create an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) that can perform OTA updates\. This user can sign and deploy firmware updates to AWS IoT devices in the account, and has access to do OTA updates on all devices\. Access should be limited to trusted entities\.

1. [Attach permissions with an OTA user policy](https://docs.aws.amazon.com/freertos/latest/userguide/create-ota-user-policy.html)\.

1. [Create an inline policy](https://docs.aws.amazon.com/freertos/latest/userguide/create-ota-user-policy.html) that allows the IAM user you created to sign the firmware\.

### Step 2: Create the code\-signing certificate<a name="ota-updates-ble-code-sign"></a>

[Create a code\-signing certificate](https://docs.aws.amazon.com/freertos/latest/userguide/ota-code-sign-cert-esp.html) that can be used to sign the firmware\. Note the certificate ARN when the certificate is imported\.

```
aws acm import-certificate --profile=ota-update-user --certificate file://ecdsasigner.crt --private-key file://ecdsasigner.key
{
"CertificateArn": "arn:aws:acm:region:account:certificate/certid"
}
```

The ARN will be used later to create a signing profile\. If desired, the profile can be created using the following command at this point:

```
aws signer put-signing-profile --profile=ota-update-user --profile-name myOTAProfile --signing-material certificateArn=arn:aws:acm:region:account:certificate/certid --platform AmazonFreeRTOS-Default --signing-parameters certname=/cert.pem
{
"arn": "arn:aws:signer::account:/signing-profiles/myOTAProfile"
}
```

### Step 3: Configure the Amazon Cognito Authentication<a name="ota-updates-ble-cognito"></a>

#### AWS IoT configuration<a name="ota-updates-ble-cognito-iot-config"></a>

First, set up the AWS IoT thing and policy\. Because we are using the MQTT proxy on the Android phone which will be authenticated using Amazon Cognito, the device does not need the AWS IoT certificates\.

Before you create the AWS IoT Policy, you need to know your AWS region and AWS account number\.

**Step 3a: Create an AWS IoT Policy**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iot/)\.

1. In the upper\-right corner, choose your account and under **My Account** make a note of your 12\-digit account ID\.

1. In the left navigation pane, choose **Settings**\. Under **Device data endpoint**, make a note of the endpoint value\. The endpoint should be something like "*xxxxxxxxxxxxxx*\.iot\.*us\-west\-2*\.amazonaws\.com"\. In this example, the AWS region is "us\-west\-2"\.

1. In the left navigation pane, choose **Secure**, choose **Policies**, and then choose **Create**\.

1. If you do not have a policy created in your account, you will see the message "You don’t have any policies yet" and you should choose **Create a policy**\.

1. Enter a name for your policy \(for example, "mqtt\_proxy\_iot\_policy"\)\.

1. In the **Add statements** section, choose **Advanced mode**\. Copy and paste the following JSON into the policy editor window\. Replace "aws\-account\-id" with your account ID \(step 2\)\. Replace "aws\-region" with your region, for example "us\-west\-2" \(step 3\)\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
         { 
             "Effect": "Allow", 
             "Action": "iot:Connect",
             "Resource": "arn:aws:iot:region:account-id:*"
         },
         {
             "Effect": "Allow",
             "Action": "iot:Publish",
             "Resource": "arn:aws:iot:region:account-id:*"
         },
         {
             "Effect": "Allow",
             "Action": "iot:Subscribe",
             "Resource": "arn:aws:iot:region:account-id:*"
         },
         {
             "Effect": "Allow",
             "Action": "iot:Receive",
             "Resource": "arn:aws:iot:region:account-id:*"
         }
      ]
   }
   ```

1. Choose **Create**\.

**Step 3b: Create an AWS IoT thing**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iot/)\.

1. In the left navigation pane, choose **Manage**, and then choose **Things**\. In the top\-right corner, choose **Create**\.

1. If you do not have any IoT things registered in your account, the message "You don’t have any things yet" is displayed and you should choose **Register a thing**\.

1. On the **Creating AWS IoT things** page, choose **Create a single thing**\.

1. On the **Add your device to the thing registry** page, enter a name for your thing \(for example, "my\_thing"\)\. Only alphanumeric characters, hyphen \("\-"\) and underscore \("\_"\) are allowed\. Choose **Next**\.

1. On the **Add a certificate for your thing** page, under **Skip certificate and create thing**, choose **Create thing without certificate**\.

   Because we are using the BLE proxy mobile app that uses an Amazon Cognito credential for authentication and authorization, no device certificate is required\.

#### Amazon Cognito configuration<a name="ota-updates-ble-cognito-config"></a>

Amazon Cognito is required for authentication of the MQTT proxy mobile app\. An IAM policy is attached to the authenticated identity to allow the principal to attach the AWS IoT policy to the credential\.

**Step 3c: Set up an Amazon Cognito user pool**

1. Sign in to the [Amazon Cognito console](https://console.aws.amazon.com/cognito/users/)\. 

1. In the right top navigation area, choose **Create a user pool**\.

1. Enter the pool name \(for example, "mqtt\_proxy\_user\_pool"\)\.

1. Choose **Review defaults**\.

1. Next to **App Clients**, click **Add app client\.\.\.**, and then choose **Add an app client**\.

1. Enter the app client name \(for example "mqtt\_app\_client"\)\.

1. Make sure **Generate client secret** is selected\.

1. Choose **Create app client**\.

1. Choose **Return to pool details**\.

1. On the **Review** page of the user pool, choose **Create pool**\. 

1. You should see a message that says "Your user pool was created successfully\."

1. Make a note of the "Pool ID"\.

1. In the left navigation pane, choose **App clients**\.

1. Click **Show Details**\.

1. Make a note of the "App client ID" and the "App client secret"\.

**Step 3d: Amazon Cognito Identity Pool**

1. Sign in to the [Amazon Cognito console](https://console.aws.amazon.com/cognito/federated)\.

1. Choose **Create new identity pool**\.

1. Enter a name for the identity pool \(for example, "mqtt\_proxy\_identity\_pool"\)\.

1. Expand **Authentication providers**\.

1. Choose the **Cognito** tab\.

1. Enter the **User pool ID** and **App client ID** that you saved from "Step 3c: Set up an Amazon Cognito user pool"\.

1. Choose **Create Pool**\.

1. On the next page, choose **Allow** to create new roles for authenticated and unauthenticated identities\.

1. Make a note of the **Identity pool ID**, which has the format "us\-east\-1:xxxxxxxx\-xxxx\-xxxx\-xxxx\-xxxxxxxxxxxx"\.

Next, we attach an IAM policy to the authenticated identity so that the credential can attach the IoT policy to it\.

**Step 3e: Attach IAM policy to the authenticated identity**

1. Open the [Amazon Cognito console](https://console.aws.amazon.com/cognito/federated)\.

1. Choose the identity pool that you just created \(for example, "mqtt\_proxy\_identity\_pool"\)\.

1. Choose **Edit identity pool**\.

1. Make a note of the IAM Role assigned to the Authenticated role \(for example, "Cognito\_mqtt\_proxy\_identity\_poolAuth\_Role"\)\.

1. Open the [IAM console](https://console.aws.amazon.com/iam/home)\.

1. In the navigation pane, choose **Roles**\.

1. Search for the role \(for example, "Cognito\_mqtt\_proxy\_identity\_poolAuth\_Role"\), and then choose it\.

1. Choose **Add inline policy**, and then choose **JSON**\.

1. Enter the following policy:

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
         {
             "Effect": "Allow",
             "Action": [
                 "iot:AttachPolicy",
                 "iot:AttachPrincipalPolicy",
                 "iot:Connect",
                 "iot:Publish",
                 "iot:Subscribe",
                 "iot:Receive"
             ],
             "Resource": "*"
         }
     ]
   }
   ```

1. Choose **Review Policy**\.

1. Enter a policy name \(for example, "mqttProxyCognitoPolicy"\)\.

1. Choose **Create policy**\.

### Step 4: Configure FreeRTOS<a name="ota-updates-ble-configure"></a>

Enable the OTA update demo as follows:

**Note**  
Only one demo can be enabled at a time in the Demo Runner\.

****

1. Open `vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h`\. 
   + Define `CONFIG_OTA_UPDATE_DEMO_ENABLED`\.
   + Change `democonfigNETWORK_TYPES` to `AWSIOT_NETWORK_TYPE_BLE`\.

1. Open `demos/include/aws_clientcredential.h`:
   + Change the endpoint URL in `clientcredentialMQTT_BROKER_ENDPOINT[]`\.
   + Change the thing name to the name of your thing \(for example, "my\_thing"\) in `clientcredentialIOT_THING_NAME`\.
**Note**  
Certificates don't have to be added when you use Amazon Cognito credentials\.

1. Open `vendors/vendor/boards/board/aws_demos/common/config_files/aws_iot_network_config.h`\. 
   + Change `configSUPPORTED_NETWORKS` and `configENABLED_NETWORKS` to only include `AWSIOT_NETWORK_TYPE_BLE`\.

1. Open `ota_demo_config.h`:
   + Change `otapalconfigCODE_SIGNING_CERTIFICATE` to refer to the certificate to be used to sign the firmware binary file\.

   The application should start up and print the demo version:

   ```
   11 13498 [iot_thread] [INFO ][DEMO][134980] Successfully initialized the demo. Network type for the demo: 2
   12 13498 [iot_thread] [INFO ][MQTT][134980] MQTT library successfully initialized.
   13 13498 [iot_thread] OTA demo version 0.9.20
   14 13498 [iot_thread] Creating MQTT Client...
   ```

### Step 5: Configure the Android app<a name="ota-updates-ble-config-android"></a>

Download the Android Bluetooth Low Energy SDK and a sample app from the [amazon\-freertos\-ble\-android\-sdk](https://github.com/aws/amazon-freertos-ble-android-sdk) GitHub repo\.

Make the following changes:

****

1. Modify the file `app/src/main/res/raw/awsconfiguration.json`:

   Fill in the "PoolId", "Region", "AppClientId", and "AppClientSecret" fields using the instructions in the following sample JSON\.

   ```
   {
     "UserAgent": "MobileHub/1.0",
     "Version": "1.0",
     "CredentialsProvider": {
         "CognitoIdentity": {
             "Default": {
                 "PoolId": "Cognito->Manage Identity Pools->Federated Identities->mqtt_proxy_identity_pool->Edit Identity Pool->Identity Pool ID",
                 "Region": "Your region (for example us-east-1)"
             }
         }
     },
   
     "IdentityManager": {
         "Default": {}
     },
   
     "CognitoUserPool": {
         "Default": {
             "PoolId": "Cognito-> Manage User Pools -> mqtt_proxy_user_pool -> General Settings -> PoolId",
             "AppClientId": "Cognito-> Manage User Pools -> mqtt_proxy_user_pool -> General Settings -> App clients ->Show Details",
             "AppClientSecret": "Cognito-> Manage User Pools -> mqtt_proxy_user_pool -> General Settings -> App clients ->Show Details",
             "Region": "Your region (for example us-east-1)"
         }
     }
   }
   ```

1. Modify the file `app/src/main/java/software/amazon/freertos/DemoConstants.java`:
   + Specify the policy name \(for example, "mqtt\_proxy\_iot\_policy"\) that you created earlier\.
   + Set the Region \(for example, "us\-east\-1"\)\.

1. Build and install the demo app:

   1. In Android Studio, choose **Build**, **Make Module app**\.

   1. Choose **Run**, **Run app**\. You can go to the logcat window pane in Android Studio to monitor log messages\.

   1. On the Android device, create an account from the login screen\.

   1. Create a user\. If a user already exists, enter the user's credentials\.

   1. Allow the FreeRTOS Demo to access the device's location\.

   1. Scan for Bluetooth Low Energy devices\.

   1. Move the slider for the device found to **On**\.

   1. Press **'y'** on the serial port debug console\.

   1. Choose **Pair & Connect**\.

The **More…** link becomes active after the connection\. You should see the connection state change to **BLE\_CONNECTED** in the Android device logcat when the connection is complete:

```
2019-06-06 20:11:32.160 23484-23497/software.amazon.freertos.demo I/FRD: BLE connection state changed: 0; new state: BLE_CONNECTED
```

Before the messages can be transmitted, the FreeRTOS device and the Android device must negotiate the MTU\. You should see the following printout in logcat:

```
2019-06-06 20:11:46.720 23484-23497/software.amazon.freertos.demo I/FRD: onMTUChanged : 512 status: Success
```

The device connects to the app and starts sending MQTT messages using the MQTT proxy\. To confirm that the device can communicate, make sure that you can see the MQTT\_CONTROL characteristic data value change to 01:

```
2019-06-06 20:12:28.752 23484-23496/software.amazon.freertos.demo D/FRD: <-<-<- Writing to characteristic: MQTT_CONTROL with data: 01
2019-06-06 20:12:28.839 23484-23496/software.amazon.freertos.demo D/FRD: onCharacteristicWrite for:
```

#### Complete pairing on microcontroller console<a name="ota-updates-ble-config-android-pairing"></a>

You will be prompted to press 'y' on the console when the device is paired with the Android device\. The demo will not function until this step is performed\.

```
E (135538) BT_GATT: GATT_INSUF_AUTHENTICATION: MITM Required
W (135638) BT_L2CAP: l2cble_start_conn_update, the last connection update command still pending.
E (135908) BT_SMP: Value for numeric comparison = 391840
15 13588 [InputTask] Numeric comparison:391840
16 13589 [InputTask] Press 'y' to confirm
17 14078 [InputTask] Key accepted
W (146348) BT_SMP: FOR LE SC LTK IS USED INSTEAD OF STK
18 16298 [iot_thread] Connecting to broker...
19 16298 [iot_thread] [INFO ][MQTT][162980] Establishing new MQTT connection.
20 16298 [iot_thread] [INFO ][MQTT][162980] (MQTT connection 0x3ffd5754, CONNECT operation 0x3ffd586c) Waiting for operation completion.
21 16446 [iot_thread] [INFO ][MQTT][164450] (MQTT connection 0x3ffd5754, CONNECT operation 0x3ffd586c) Wait complete with result SUCCESS.
22 16446 [iot_thread] [INFO ][MQTT][164460] New MQTT connection 0x3ffc0ccc established.
23 16446 [iot_thread] Connected to broker.
```

## Testing<a name="ota-updates-ble-testing"></a>

1. To install the prerequisites, run the following commands:

   ```
   pip3 install boto3
   pip3 install pathlib
   ```

1. Download the [ python](https://github.com/aws-samples/amazon-freertos-ota-scripts/blob/master/scripts/start_ota.py) script\.

1. Bump up the FreeRTOS application version in `demos/include/aws_application_version.h` and build a new binary file\.

1. To get help, run the following command in a terminal window:

   ```
   python3 start_ota.py -h
   ```

   ```
   usage: start_ota.py [-h] --profile PROFILE [--region REGION]
                       [--account ACCOUNT] [--devicetype DEVICETYPE] --name NAME
                       --role ROLE --s3bucket S3BUCKET --otasigningprofile
                       OTASIGNINGPROFILE --signingcertificateid
                       SIGNINGCERTIFICATEID [--codelocation CODELOCATION]
   Script to start OTA update
   optional arguments:
   -h, --help            show this help message and exit
   --profile PROFILE     Profile name created using aws configure
   --region REGION       Region
   --account ACCOUNT     Account ID
   --devicetype DEVICETYPE thing|group
   --name NAME           Name of thing/group
   --role ROLE           Role for OTA updates
   --s3bucket S3BUCKET   S3 bucket to store firmware updates
   --otasigningprofile OTASIGNINGPROFILE
                         Signing profile to be created or used
   --signingcertificateid SIGNINGCERTIFICATEID
                         certificate id (not arn) to be used
   --codelocation CODELOCATION
                         base folder location (can be relative)
   ```

1. If you used the provided AWS CloudFormation template to create resources, here's an example run:

   ```
   python3 start_ota_stream.py --profile otausercf --name my_thing --role ota_ble_iot_role-sample --s3bucket afr-ble-ota-update-bucket-sample --otasigningprofile abcd --signingcertificateid certificateid
   ```

## Validation<a name="ota-updates-ble-validation"></a>

1. From the console, the logs show that the OTA update has started and the file block download is in progress:

   ```
   38 2462 [OTA Task] [prvParseJobDoc] Job was accepted. Attempting to start transfer.
   ---
   49 2867 [OTA Task] [prvIngestDataBlock] Received file block 1, size 1024
   50 2867 [OTA Task] [prvIngestDataBlock] Remaining: 1290
   51 2894 [OTA Task] [prvIngestDataBlock] Received file block 2, size 1024
   52 2894 [OTA Task] [prvIngestDataBlock] Remaining: 1289
   53 2921 [OTA Task] [prvIngestDataBlock] Received file block 3, size 1024
   54 2921 [OTA Task] [prvIngestDataBlock] Remaining: 1288
   55 2952 [OTA Task] [prvIngestDataBlock] Received file block 4, size 1024
   56 2953 [OTA Task] [prvIngestDataBlock] Remaining: 1287
   57 2959 [iot_thread] State: Active  Received: 5   Queued: 5   Processed: 5   Dropped: 0
   ```

1. When the OTA update is complete, the device restarts with the updated firmware, connects again to the Android app, and then performs a self test\.

1. If the self test succeeds, the updated firmware is marked as active and you should see the updated version in the console:

   ```
   13 13498 [iot_thread] OTA demo version 0.9.21
   ```

## References<a name="ota-updates-ble-reference"></a>

1. [ AWS Blog on OTA updates over Bluetooth Low Energy](https://aws.amazon.com/blogs/iot/perform-ota-firmware-updates-on-espressif-esp32-devices-using-amazon-freertos-bluetooth-low-energy-mqtt-proxy/)\.