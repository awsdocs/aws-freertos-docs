# Tutorial: Perform OTA updates on Espressif ESP32 using FreeRTOS Bluetooth Low Energy<a name="ota-updates-esp32-ble"></a>

This tutorial shows you how to update an Espressif ESP32 microcontroller that is connected to an MQTT Bluetooth Low Energy proxy on an Android device\. It updates the device using AWS IoT Over\-the\-air \(OTA\) update jobs\. The device connects to AWS IoT using Amazon Cognito credentials entered in the Android demo app\. An authorized operator initiates the OTA update from the cloud\. When the device connects through the Android demo app, the OTA update is initiated and the firmware is updated on the device\. 

FreeRTOS versions 2019\.06\.00 Major and later include Bluetooth Low Energy MQTT proxy support that can be used for Wi\-Fi provisioning and secure connections to AWS IoT services\. By using the Bluetooth Low Energy feature, you can build low\-power devices that can be paired to a mobile device for connectivity without requiring Wi\-Fi\. Devices can communicate using MQTT by connecting through Android or iOS Bluetooth Low Energy SDKs that use generic access profile \(GAP\) and generic attributes \(GATT\) profiles\.

Here are the steps that we will follow to allow OTA updates over Bluetooth Low Energy:

1. **Configure storage:** Create an Amazon S3 bucket and policies and configure an IAM user that can perform updates\.

1. **Create a code\-signing certificate:** Create a signing certificate and allow the IAM user to sign firmware updates\.

1. **Configure Amazon Cognito authentication:** Create a credential provider, user pool, and application access to the user pool\.

1. **Configure FreeRTOS:** Set up Bluetooth Low Energy, client credentials, and the code\-signing public certificate\.

1. **Configure an Android app:** Set up credential provider, user pool, and deploy the application to an Android device\.

1. **Run the OTA update script:** To initiate an OTA update, use the OTA update script\.

For more information about how the updates work, see [FreeRTOS Over\-the\-Air Updates](freertos-ota-dev.md)\. For additional information about how to set up the Bluetooth Low Energy MQTT proxy functionality, see the post [ Using Bluetooth Low Energy with FreeRTOS on Espressif ESP32](http://aws.amazon.com/blogs/iot/using-bluetooth-low-energy-with-amazon-freertos-on-espressif-esp32/) by Richard Kang\.

## Prerequisites<a name="ota-updates-esp32-ble-prereq"></a>

To perform the steps in this tutorial, you need the following resources:
+ An ESP32 development board\.
+ A MicroUSB to USB A cable\.
+ An AWS account \(the Free Tier is sufficient\)\.
+ An Android phone with Android v 6\.0 or later and Bluetooth version 4\.2 or later\.

On your development computer you need:
+ Sufficient disk space \(\~500 Mb\) for the Xtensa toolchain and FreeRTOS source code and examples\.
+ Android Studio installed\.
+ The [AWS CLI](https://aws.amazon.com/cli/) installed\.
+ Python3 installed\.
+ The [boto3 AWS Software Developer Kit \(SDK\) for Python](https://github.com/boto/boto3)\.

The steps in this tutorial assume that Xtensa toolchain, ESP\-IDF, and FreeRTOS code are installed in the `/esp` directory in your home directory\. You must add `~/esp/xtensa-esp32-elf/bin` to your `$PATH` variable\.

## Step 1: Configure storage<a name="ota-updates-esp32-ble-step1"></a>

1. [Create an Amazon S3 bucket to store your update](dg-ota-bucket.md) with versioning enabled to hold the firmware images\.

1. [Create an OTA Update service role](create-service-role.md) and add the following managed policies to the role:
   + AWSIotLogging
   + AWSIotRuleActions
   + AWSIotThingsRegistration
   + AWSFreeRTOSOTAUpdate

1. [Create an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) that can perform OTA updates\. This user can sign and deploy firmware updates to IoT devices in the account, and has access to do OTA updates on all devices\. Access should be limited to trusted entities\.

1. Follow the steps to [Create an OTA user policy](create-ota-user-policy.md) and attach it to your IAM user\. 

## Step 2: Create the code\-signing certificate<a name="ota-updates-esp32-ble-step2"></a>

1. Create an Amazon S3 bucket with versioning enabled to hold the firmware images\.

1. Create a code\-signing certificate that can be used to sign the firmware\. Note the certificate Amazon Resource Name \(ARN\) when the certificate is imported\.

   ```
   aws acm import-certificate --profile=ota-update-user --certificate file://ecdsasigner.crt --private-key file://ecdsasigner.key
   ```

   Example output:

   ```
   {
   "CertificateArn": "arn:aws:acm:us-east-1:<account>:certificate/<certid>"
   }
   ```

   You'll use the ARN later to create a signing profile\. If you want, you can create the profile now with the following command:

   ```
   aws signer put-signing-profile --profile=ota-update-user --profile-name esp32Profile --signing-material certificateArn=arn:aws:acm:us-east-1:account:certificate/certid --platform AmazonFreeRTOS-Default --signing-parameters certname=/cert.pem
   ```

   Example output:

   ```
   {
   "arn": "arn:aws:signer::<account>:/signing-profiles/esp32Profile"
   }
   ```

## Step 3: Amazon Cognito authentication configuration<a name="ota-updates-esp32-ble-step3"></a>

**Create an AWS IoT policy**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iot/)\.

1. In the upper\-right corner of the console, choose **My Account**\. Under **Account Settings**, make a note of your 12\-digit account ID\.

1. In the left navigation pane, choose **Settings**\. In **Device data endpoint**, make a note of the endpoint value\. The endpoint should be something like `xxxxxxxxxxxxxx.iot.us-west-2.amazonaws.com`\. In this example, the AWS Region is "us\-west\-2"\.

1. In the left navigation pane, choose **Secure**, choose **Policies**, and then choose **Create**\. If you don't have any policies in your account, you will see the message "You don’t have any policies yet" and you can choose **Create a policy**\.

1. Enter a name for your policy, for example, "esp32\_mqtt\_proxy\_iot\_policy"\.

1. In the **Add statements** section, choose **Advanced mode**\. Copy and paste the following JSON into the policy editor window\. Replace `aws-account-id` with your account ID and `aws-region` with your Region \(for example, "us\-west\-2"\)\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "iot:Connect",
         "Resource": "arn:aws:iot:aws-region:aws-account-id:*"
       },
       {
         "Effect": "Allow",
         "Action": "iot:Publish",
         "Resource": "arn:aws:iot:aws-region:aws-account-id:*"
       },
       {
         "Effect": "Allow",
         "Action": "iot:Subscribe",
         "Resource": "arn:aws:iot:aws-region:aws-account-id:*"
       },
       {
         "Effect": "Allow",
         "Action": "iot:Receive",
         "Resource": "arn:aws:iot:aws-region:aws-account-id:*"
       }
     ]
   }
   ```

1. Choose **Create**\.

**Create an AWS IoT thing**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iot/)\.

1. In the left navigation pane, choose **Manage**, and then choose **Things**\.

1. In the top\-right corner, choose **Create**\. If you don't have any things registered in your account, the message "You don’t have any things yet" is displayed and you can choose **Register a thing**\.

1. On the **Creating AWS IoT things** page, choose **Create a single thing**\.

1. On the **Add your device to the thing registry** page, enter a name for your thing \(for example, "esp32\-ble"\)\. Only alphanumeric, hyphen \(\-\), and underscore \(\_\) characters are allowed\. Choose **Next**\.

1. On the **Add a certificate for your thing** page, under **Skip certificate and create thing**, choose **Create thing without certificate**\. Because we use the BLE proxy mobile app that uses an Amazon Cognito credential for authentication and authorization, no device certificate is required\.

**Create an Amazon Cognito App Client**

1. Sign in to the [Amazon Cognito console](https://console.aws.amazon.com/cognito/users/)\.

1. In the top\-right navigation banner, choose **Create a user pool**\.

1. Enter the pool name \(for example, "esp32\_mqtt\_proxy\_user\_pool"\)\.

1. Choose **Review defaults**\.

1. In **App Clients**, choose **Add app client**, and then choose **Add an app client**\. 

1. Enter an app client name \(for example "mqtt\_app\_client"\)\.

1. Make sure **Generate client secret** is selected\.

1. Choose **Create app client**\.

1. Choose **Return to pool details**\.

1. On the **Review** page of the user pool, choose **Create pool**\. You should see a message that says "Your user pool was created successfully\." Make a note of the pool ID\.

1. In the navigation pane, choose **App clients**\.

1. Choose **Show Details**\. Make a note of the app client ID and the app client secret\.

**Create an Amazon Cognito identity pool**

1. Sign in to the [Amazon Cognito console](https://console.aws.amazon.com/cognito/federated)\.

1. Choose **Create new identity pool**\.

1. Enter a name for the identity pool \(for example, "mqtt\_proxy\_identity\_pool"\)\.

1. Expand **Authentication providers**\.

1. Choose the **Cognito** tab\.

1. Enter the user pool ID and app client ID that you noted in previous steps\.

1. Choose **Create Pool**\.

1. On the next page, to create new roles for authenticated and unauthenticated identities, choose **Allow**\.

1. Make a note of the identity pool ID, which is in the format `us-east-1:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`\. 

**Attach an IAM policy to the authenticated identity**

1. Open the [Amazon Cognito console](https://console.aws.amazon.com/cognito/federated)\.

1. Select the identity pool that you just created \(for example, "mqtt\_proxy\_identity\_pool"\)\.

1. Choose **Edit identity pool**\.

1. Make a note of the IAM Role assigned to the authenticated role \(for example, "Cognito\_mqtt\_proxy\_identity\_poolAuth\_Role"\)\.

1. Open the [IAM console](https://console.aws.amazon.com/iam/home)\.

1. In the navigation pane, choose **Roles**\.

1. Search for the role assigned \(for example, "Cognito\_mqtt\_proxy\_identity\_poolAuth\_Role"\), and then select it\.

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
                "iot:Subscribe"
             ],
             "Resource": "*"
          }]
       }
   ```

1. Choose **Review Policy**\.

1. Enter a policy name \(for example, "mqttProxyCognitoPolicy"\)\.

1. Choose **Create policy**\.

## Step 4: Configure Amazon FreeRTOS<a name="ota-updates-esp32-ble-step4"></a>

1. Download the latest version of the Amazon FreeRTOS code from the [FreeRTOS console](https://console.aws.amazon.com/freertos) or the [FreeRTOS GitHub repo](https://github.com/aws/amazon-freertos)\.

1. To enable the OTA update demo, follow the steps in [Getting started with the Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT](getting_started_espressif.md)\. 

1. Make these additional modifications in the following files: 

   1. Open `vendors/espressif/boards/esp32/aws_demos/config_files/aws_demo_config.h` and define `CONFIG_OTA_UPDATE_DEMO_ENABLED`\.

   1. Open `vendors/espressif/boards/esp32/aws_demos/common/config_files/aws_demo_config.h` and change `democonfigNETWORK_TYPES` to `AWSIOT_NETWORK_TYPE_BLE`\.

   1. Open `demos/include/aws_clientcredential.h` and enter your endpoint URL for `clientcredentialMQTT_BROKER_ENDPOINT`\.

      Enter your thing name for `clientcredentialIOT_THING_NAME` \(for example, "esp32\-ble"\)\. Certificates don't have to be added when you use Amazon Cognito credentials\.

   1. Open `vendors/espressif/boards/esp32/aws_demos/config_files/aws_iot_network_config.h` and change `configSUPPORTED_NETWORKS` and `configENABLED_NETWORKS` to include only `AWSIOT_NETWORK_TYPE_BLE`\.

   1. Open the `vendors/vendor/boards/board/aws_demos/config_files/ota_demo_config.h` file, and enter your certificate\.

      ```
      #define otapalconfigCODE_SIGNING_CERTIFICATE [] = "your-certificate-key";
      ```

   The application should start up and print the demo version:

   ```
   11 13498 [iot_thread] [INFO ][DEMO][134980] Successfully initialized the demo. Network type for the demo: 2
   12 13498 [iot_thread] [INFO ][MQTT][134980] MQTT library successfully initialized.
   13 13498 [iot_thread] OTA demo version 0.9.20
   14 13498 [iot_thread] Creating MQTT Client...
   ```

## Step 5: Configure an Android app<a name="ota-updates-esp32-ble-step5"></a>

1. Download the Android Bluetooth Low Energy SDK and a sample app from the [amazon\-freertos\-ble\-android\-sdk](https://github.com/aws/amazon-freertos-ble-android-sdk) GitHub repo\.

1. Open the file `app/src/main/res/raw/awsconfiguration.json` and fill in the Pool Id, Region, AppClientId, and AppClientSecret using the instructions in the following JSON sample\.

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
         "PoolId": "Cognito-> Manage User Pools -> esp32_mqtt_proxy_user_pool -> General Settings -> PoolId",
         "AppClientId": "Cognito-> Manage User Pools -> esp32_mqtt_proxy_user_pool -> General Settings -> App clients ->Show Details",
         "AppClientSecret": "Cognito-> Manage User Pools -> esp32_mqtt_proxy_user_pool -> General Settings -> App clients ->Show Details",
         "Region": "Your region (for example us-east-1)"
       }
     }
   }
   ```

1. Open `app/src/main/java/software/amazon/freertos/DemoConstants.java` and enter the policy name that you created earlier \(for example, *esp32\_mqtt\_proxy\_iot\_policy*\) and also the Region \(for example, *us\-east\-1*\)\.

1. Build and install the demo app\.

   1. In Android Studio, choose **Build**, then **Make Module app**\.

   1. Choose **Run**, then **Run app**\. You can go to the logcat window pane in Android Studio to monitor log messages\.

   1. On the Android device, create an account from the login screen\.

   1. Create a user\. If a user already exists, enter the credentials\.

   1. Allow the Amazon FreeRTOS Demo to access the device’s location\.

   1. Scan for Bluetooth Low Energy devices\.

   1. Move the slider for the device found to **On**\.

   1. Press **y** on the serial port debug console for the ESP32\.

   1. Choose **Pair & Connect**\.

1. The **More\.\.\.** link becomes active after the connection is established\. The connection state should change to "BLE\_CONNECTED" in the Android device logcat when the connection is complete:

   ```
   2019-06-06 20:11:32.160 23484-23497/software.amazon.freertos.demo I/FRD: BLE connection state changed: 0; new state: BLE_CONNECTED
   ```

1. Before the messages can be transmitted, the Amazon FreeRTOS device and the Android device negotiate the MTU\. You should see the following output in logcat:

   ```
   2019-06-06 20:11:46.720 23484-23497/software.amazon.freertos.demo I/FRD: onMTUChanged : 512 status: Success
   ```

1. The device connects to the app and starts sending MQTT messages using the MQTT proxy\. To confirm that the device can communicate, make sure the MQTT\_CONTROL characteristic data value changes to 01:

   ```
   2019-06-06 20:12:28.752 23484-23496/software.amazon.freertos.demo D/FRD: <-<-<- Writing to characteristic: MQTT_CONTROL with data: 01
   2019-06-06 20:12:28.839 23484-23496/software.amazon.freertos.demo D/FRD: onCharacteristicWrite for: MQTT_CONTROL; status: Success; value: 01
   ```

1. When the devices are paired, you will see a prompt on the ESP32 console\. To enable BLE, press **y**\. The demo won't function until you perform this step\. 

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

## Step 6: Run the OTA update script<a name="ota-updates-esp32-ble-step6"></a>

1. To install the prerequisites, run the following commands:

   ```
   pip3 install boto3
   ```

   ```
   pip3 install pathlib
   ```

1. Increment the FreeRTOS application version in `demos/include/aws_application_version.h`\.

1. Build a new \.bin file\.

1. Download the python script [ start\_ota\.py](https://github.com/aws-samples/amazon-freertos-ota-scripts/blob/master/scripts/start_ota.py)\. To see the help contents for the script, run the following command in a terminal window:

   ```
   python3 start_ota.py -h
   ```

   You should see something like the following:

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

1. If you used the provided AWS CloudFormation template to create resources, run the following command:

   ```
   python3 start_ota_stream.py --profile otausercf --name esp32-ble --role ota_ble_iot_role-sample --s3bucket afr-ble-ota-update-bucket-sample --otasigningprofile abcd --signingcertificateid certificateid
   ```

   You should see the update start in the ESP32 debug console: 

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

1. When the OTA update is complete, the device restarts as required by the OTA update process\. It then tries to connect using the updated firmware\. If the upgrade has succeeded, the updated firmware is marked as active and you should see the updated version in the console:

   ```
   13 13498 [iot_thread] OTA demo version 0.9.21
   ```