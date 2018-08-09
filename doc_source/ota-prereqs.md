# Over\-the\-Air Update Prerequisites<a name="ota-prereqs"></a>

To use Over\-the\-Air updates you need to:
+ Create an S3 bucket to store your firmware update\.
+ Create an OTA service role\.
+ Create an OTA user policy\.
+ Create or purchase a code signing certificate\.
+ if you are using Code Signing for Amazon FreeRTOS, import your code signing key into ACM\.
+ if you are using Code Signing for Amazon FreeRTOS, create a code signing policy\.
+ Download Amazon FreeRTOS with the OTA library for your platform or if you are not using Amazon FreeRTOS, provide your own OTA agent implementation\.

## Create an Amazon S3 Bucket to Store Your Update<a name="dg-ota-bucket"></a>

OTA update files are stored in Amazon S3 buckets\. If you are using Code\-signing for Amazon FreeRTOS the command you use to create a code signing job takes a source bucket \(where the unsigned firmware image is located\) and a destination bucket \(where the signed firmware image is written\)\. You can specify the same bucket for both the source and destination\. The files name are changed to GUIDs so the original files will not be overwritten\.<a name="create-bucket"></a>

**To create an Amazon S3 bucket**

1. Go to the [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

1. Type a bucket name, and then choose **Next**\.

1. On the **Create bucket** page, choose **Versioning**\.

1. Choose **Enable versioning**, and then choose **Save**\.

1. Choose **Next**\.

1. Choose **Next** to accept the default permissions\.

1. Choose **Create bucket**\.

For more information about Amazon S3, see [Amazon Simple Storage Service Console User Guide](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/)\.

## Creating an OTA Update Service Role<a name="create-service-role"></a>

The Over\-the\-Air Update service assumes this role to create and manage OTA update jobs on your behalf\.<a name="create-service-role"></a>

**To create an OTA service role**

1. Sign in to the [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. From the navigation pane, choose **Roles**\.

1. Choose the **Create role** button\.

1. Under **Select type of trusted entity**, choose **AWS Service**\.

1. Choose **IoT** from the list of AWS services\.

1. Choose **Next: Permissions**\.

1. Choose **Next: Review**\.

1. Type a role name and description, and then choose **Create role**\.

For more information about IAM roles, see [IAM Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)\.<a name="add-ota-permissions"></a>

**To add OTA update permissions to your OTA service role**

1. In the search box on the IAM console page, type your role's name and then choose it from the list\.

1. Choose the **Attach policy** button\.

1. In the **Search** box, type **AWSIoTOTAUpdate**, select the check box next to **AWSIoTOTAUpdate** in the list of managed policies, and then choose **Attach policy**\.<a name="add-s3-permissions"></a>

**To add Amazon S3 permissions to your OTA service role**

1. In the search box on the IAM console page, type your role's name and then choose it from the list\.

1. In the lower right, choose **Add inline policy**\.

1. Choose the **JSON** tab\.

1. Copy and paste the following policy document into the text box\. Replace *<example\-bucket>* with the name of your bucket\.

   ```
   {
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
   }
   ```

1. Choose **Review policy**\.

1. Type a name for the policy and choose **Create policy**\.

## Creating an OTA User Policy<a name="create-ota-user-policy"></a>

You must grant your IAM user permission to perform over\-the\-air updates\. Your IAM user must have permissions to:
+ Access the S3 bucket where your firmware updates are stored\.
+ Access certificates stored in AWS Certificate Manager\.
+ Access the Amazon streaming service\.
+ Access the Amazon FreeRTOS OTA Update service\.
+ Access the AWS IoT Jobs service\.
+ Access IAM\.
+ Access the Code Signing for Amazon FreeRTOS service\.

Grant your IAM user the required permissions by creating an OTA user policy and attaching it to your IAM user\. For more information about IAM policies, see [IAM Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)\.<a name="create-ota-user-policy"></a>

**To create an OTA user policy**

1. Open the [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/) console\.

1. In the navigation pane, choose **Users**\.

1. Choose your IAM user from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. Choose the **Create policy** button\.

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
   			"iot:ListThings",
   			"iot:ListThingGroups",
   			"iot:CreateStream",
   			"iot:CreateOTAUpdateJob",
   			"iot:GetOTAUpdateJob",
   			"iot:ListJobs",
   			"iot:ListJobExecutionsForJob",
   			"iot:DescribeJob",
   			"iot:GetJobDocument",
   			"iam:ListRoles",
   			"iot:DescribeStream",
   			"iot:DeleteStream",
   			"iot:DeleteOTAUpdateJob",
   			"iot:DeleteJob",
   			"iot:DeleteJobExecution"
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

   Replace *<example\-bucket>* with the name of the Amazon S3 bucket where you store your OTA update firmware image\. Replace *<your\-account\-id>* with your AWS account ID\. You can find your AWS account ID in the upper\-right corner of the console\. When you enter your account ID, remove any dashes \(\-\)\. Replace *<role\-name>* with the name of the IAM service role you just created\. 

1. Choose **Review policy**\.

1. Type a name for your new OTA user policy, and then choose the **Create policy** button\.<a name="attach-ota-user-policy"></a>

**To attach the OTA user policy to your IAM user**

1. In the IAM console, on the navigation pane, choose **Users**, and then choose your user\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. Search for the OTA user policy you just created and select the check box next to it\.

1. Choose **Next: Review**\.

1. Choose **Add permissions**\.

## Creating a Code Signing Certificate<a name="ota-code-sign-cert"></a>

To digitally sign firmware images, you need a code\-signing certificate and private key\. For testing purposes, you can create a self‐signed certificate and private key\. For production environments, purchase a certificate through a well‐known certificate authority \(CA\)\.

Different platforms require different types of code signing certificates\. Below you will find a section that describes creating code signing certificates for each of the Amazon FreeRTOS qualified platforms\.

### Creating a Code Signing Certificate for the Texas Instruments CC3200SF\-LAUNCHXL<a name="ota-code-sign-cert-ti"></a>

The SimpleLink Wi\-Fi CC3220SF Wireless Microcontroller Launchpad Development Kit supports two certificate chains for firmware code signing:
+ Production \(certificate\-catalog\)

  To use the production certificate chain, you must purchase a commercial code\-signing certificate and use the [TI Uniflash tool](http://www.ti.com/tool/UNIFLASH) to set the board to production mode\.
+ Testing and development \(certificate\-playground\) 

  The playground certificate chain allows you to try out OTA updates with a self‐signed code\-signing certificate\.

Install the [SimpleLink CC3220 SDK version 2\.10\.00\.04](http://www.ti.com/tool/download/SIMPLELINK-CC3220-SDK/2.10.00.04)\. By default, the files you need are located here:

`C:\ti\simplelink_cc32xx_sdk_2_10_00_04\tools\cc32xx_tools\certificate-playground` \(Windows\) 

`/Applications/Ti/simplelink_cc32xx_sdk_2_10_00_04/tools/cc32xx_tools/certificate-playground` \(macOS\)

The certificates in the SimpleLink CC3220 SDK are in DER format\. You need to convert them to PEM format to create a self‐signed code\-signing certificate\.

Follow these steps to create a code\-signing certificate that is linked to the Texas Instruments playground certificate hierarchy and meets AWS Certificate Manager and Code Signing for Amazon FreeRTOS criteria\.

**To create a self‐signed code signing certificate**

1. In your working directory, use the following text to create a file named `cert_config`\. Replace *test\_signer@amazon\.com* with your email address\.

   ```
   [ req ]
   prompt             = no
   distinguished_name = my dn
   
   [ my dn ]
   commonName = test_signer@amazon.com
   
   [ my_exts ]
   keyUsage         = digitalSignature
   extendedKeyUsage = codeSigning
   ```

1. Create a private key and certificate signing request \(CSR\):

   ```
   openssl req -config cert_config -extensions my_exts -nodes -days 365 -newkey rsa:2048 -keyout tisigner.key -out tisigner.csr
   ```

1. Convert the Texas Instruments playground root CA private key from DER format to PEM format\.

   The TI playground root CA private key is located here:

   `C:\ti\simplelink_cc32xx_sdk_2_10_00_04\tools\cc32xx_tools\certificate-playground\dummy-root-ca-cert-key` \(Windows\) 

   `/Applications/Ti/simplelink_cc32xx_sdk_2_10_00_04/tools/cc32xx_tools/certificate-playground/dummy-root-ca-cert-key` \(macOS\)

   ```
   openssl rsa -inform DER -in dummy-root-ca-cert-key -out dummy-root-ca-cert-key.pem
   ```

1. Convert the Texas Instruments playground root CA certificate from DER format to PEM format\.

   The TI playground root certificate is located here:

   `C:\ti\simplelink_cc32xx_sdk_2_10_00_04\tools\cc32xx_tools\certificate-playground/dummy-root-ca-cert` \(Windows\) 

   `/Applications/Ti/simplelink_cc32xx_sdk_2_10_00_04/tools/cc32xx_tools/certificate-playground/dummy-root-ca-cert` \(macOS\)

   ```
   openssl x509 -inform DER -in dummy-root-ca-cert -out dummy-root-ca-cert.pem
   ```

1. Sign the CSR with the Texas Instruments root CA:

   ```
   openssl x509 -extfile cert_config -extensions my_exts  -req -days 365 -in tisigner.csr -CA dummy-root-ca-cert.pem -CAkey dummy-root-ca-cert-key.pem -set_serial 01 -out tisigner.crt.pem -sha1
   ```

1. Convert your code\-signing certificate \(tisigner\.crt\.pem\) to DER format: 

   ```
   openssl x509 -in tisigner.crt.pem -out tisigner.crt.der -outform DER
   ```
**Note**  
You write the `tisigner.crt.der` certificate onto the TI development board later\.

1. Import the code\-signing certificate, private key, and certificate chain into AWS Certificate Manager:

   ```
   aws acm import-certificate --certificate file://tisigner.crt.pem --private-key file://tisigner.key --certificate-chain file://dummy-root-ca-cert.pem
   ```

   This command displays an ARN for your certificate\. You need this ARN when you create an OTA update job\.
**Note**  
This step assumes you are going to use the Code Signing for Amazon FreeRTOS service to sign your firmware images\. Although the use of the service is recommended, you can sign your firmware images manually\.

### Creating a Code Signing Certificate for the Microchip Curiosity PIC32MZEF<a name="ota-code-sign-cert-mchip"></a>

The Microchip Curiosity PIC32MZEF supports a self\-signed SHA256 with ECDSA code\-signing certificate\.

1. In your working directory use the following text to create a file named cert\_config\. Replace test\_signer@amazon\.com with your email address:

   ```
   [ req ]
   prompt             = no
   distinguished_name = my_dn
   					
   [ my_dn ]
   commonName = test_signer@amazon.com
   					
   [ my_exts ]
   keyUsage         = digitalSignature
   extendedKeyUsage = codeSigning
   ```

1. Create an ECDSA code\-signing private key:

   ```
   openssl genpkey -algorithm EC -pkeyopt ec_paramgen_curve:P-256 -pkeyopt ec_param_enc:named_curve -outform PEM -out ecdsasigner.key
   ```

1. Create an ECDSA code\-signing certificate:

   ```
   openssl req -new -x509 -config cert_config -extensions my_exts -nodes -days 365 -key ecdsasigner.key -out ecdsasigner.crt
   ```

1. Import the code\-signing certificate, private key, and certificate chain into AWS Certificate Manager:

   ```
   aws acm import-certificate --certificate file://ecdsasigner.crt --private-key file://ecdsasigner.crt 
   ```

   This command displays an ARN for your certificate\. You need this ARN when you create an OTA update job\.
**Note**  
This step assumes you are going to use the Code Signing for Amazon FreeRTOS service to sign your firmware images\. Although the use of the service is recommended, you can sign your firmware images manually\.

### Creating a Code Signing Certificate for the Amazon FreeRTOS Windows Simulator<a name="ota-code-sign-cert-win"></a>

The Amazon FreeRTOS Windows simulator requires a code\-signing certificate with an RSA\-2048 bit key and SHA\-256 hash to perform OTA updates\. If you don't have a code\-signing certificate you can create one using the following steps:

1. In your working directory use the following text to create a file named cert\_config\. Replace test\_signer@amazon\.com with your email address:

   ```
   [ req ]
   prompt             = no
   distinguished_name = my_dn
   					
   [ my_dn ]
   commonName = test_signer@amazon.com
   					
   [ my_exts ]
   keyUsage         = digitalSignature
   extendedKeyUsage = codeSigning
   ```

1. Create a code\-signing private key and certificate:

   ```
   openssl req -x509 -config cert_config -extensions my_exts -nodes -days 365 -newkey rsa:2048 -keyout rsasigner.key -out rsasigner.crt
   ```

1. Import the code\-signing certificate, private key, and certificate chain into AWS Certificate Manager:

   ```
   aws acm import-certificate --certificate file://rsasigner.crt --private-key file://rsasigner.key
   ```

   This command displays an ARN for your certificate\. You need this ARN when you create an OTA update job\.
**Note**  
This step assumes you are going to use the Code Signing for Amazon FreeRTOS service to sign your firmware images\. Although the use of the service is recommended, you can sign your firmware images manually\.

### Creating a Code Signing Certificate for Custom Hardware<a name="ota-code-sign-cert-other"></a>

Using an appropriate toolset, create a self\-signed certificate and private key for your hardware\.

After creating your code signing certificate, import it into ACM:

```
aws acm import-certificate --certificate file://code-sign.crt --private-key file://code-sign.key
```

The output from this command displays an ARN for your certificate\. You need this ARN when you create an OTA update job\.

ACM requires certificates to use specific algorithms and key sizes\. For more information see [Prerequisites for Importing Certificates](http://docs.aws.amazon.com/acm/latest/userguide/import-certificate-prerequisites.html)\. For more information about ACM, see [Importing Certificates into AWS Certificate Manager](https://docs.aws.amazon.com/acm/latest/userguide/import-certificate.html)\.

You will need to copy, paste, and format the contents of your code signing certificate and private key into the aws\_codesign\_keys\.h file that is part of the Amazon FreeRTOS code you will download later\.

## Granting Access to the Code Signing for Amazon FreeRTOS Service<a name="code-sign-policy"></a>

In production environments, you should digitally sign your firmware update to ensure the authenticity and integrity of the update\. You can sign your update manually or use the Code Signing for Amazon FreeRTOS service to sign your code\. To use the Code Signing for Amazon FreeRTOS, you must grant your IAM user account access to the Code Signing for Amazon FreeRTOS service\.<a name="grant-user-account-code-signing-permissions"></a>

**To grant your IAM user account permissions for Code Signing for Amazon FreeRTOS**

1. Sign in to the [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create Policy**\.

1. On the **JSON** tab, copy and paste the following JSON document into the policy editor\. This policy allows the IAM user access to all code signing operations\.

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

1. Type a policy name and description, and then choose **Create policy**\.

1. In the navigation pane, choose **Users**\.

1. Choose your IAM user account\.

1. On the **Permissions** tab, choose **Add permissions**\.

1. Choose **Attach existing policies directly**, and then select the check box next to the code signing policy you just created\.

1. Choose **Next: Review**\.

1. Choose **Add permissions**\.

## Download Amazon FreeRTOS with the OTA Library<a name="ota-download-freertos"></a>

### Download and Build Amazon FreeRTOS for the Texas Instruments CC3200SF\-LAUNCHXL<a name="download-ota-ti"></a><a name="download-demo"></a>

**To download Amazon FreeRTOS and the OTA demo code**

1. Browse to the AWS IoT console and from the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. From the list of software configurations, choose **Connect to AWS IoT \- TI**\. Choose the configuration name, not the **Download** link\.
**Note**  
The OTA Updates feature is currently in beta\.

1. Under **Libraries**, choose **Add another library**, and then choose **OTA Updates**\.

1. Under **Demo Projects**, choose **OTA Updates**\.

1. Under **Name required**, type **Connect\-to\-IoT\-OTA\-TI**, and then choose **Create and download**\.

Save the zip file containing Amazon FreeRTOS and the OTA demo code to your computer\. <a name="build-demo"></a>

**To build the demo application**

1. Extract the \.zip file\.

1. Follow the instructions in [Getting Started with Amazon FreeRTOS](freertos-getting-started.md), to import the `aws_demos` project into Code Composer Studio, configure your AWS IoT endpoint, your Wi\-Fi SSID and password, and a private key and certificate for your board\.

1. Open the project in Code Composer Studio and build it to make sure it compiles without errors\.

1. Start a terminal emulator and connect to your board using the following settings:
   + Baud rate: 115200
   + Data bits: 8
   + Parity: None
   + Stop bits: 1

1. Run the project on your board to make sure it can connect to Wi\-Fi and the AWS IoT MQTT message broker\.

When run, the terminal emulator should display text like the following:

```
0 0 [Tmr Svc] Simple Link task created
Device came up in Station mode
1 374 [Tmr Svc] Starting key provisioning...
2 374 [Tmr Svc] Write root certificate...
3 474 [Tmr Svc] Write device private key...
4 574 [Tmr Svc] Write device certificate...
SL Disconnect...
5 666 [Tmr Svc] Key provisioning done...
Device came up in Station mode
Device disconnected from the AP on an ERROR..!! 
[WLAN EVENT] STA Connected to the AP: Guest , BSSID: 11:22:a1:b2:c3:d4
[NETAPP EVENT] IP acquired by the device

Device has connected to Guest
Device IP Address is 111.222.3.44 

6 3718 [OTA] OTA demo version 0.9.0
7 3719 [OTA] Creating MQTT Client...
8 3719 [OTA] Connecting to broker...
9 3719 [OTA] Sending command to MQTT task.
10 3719 [MQTT] Received message 10000 from queue.
11 4798 [MQTT] MQTT Connect was accepted. Connection established.
12 4798 [MQTT] Notifying task.
13 4799 [OTA] Command sent to MQTT task passed.
14 4799 [OTA] Connected to broker.
15 4800 [OTA Task] Sending command to MQTT task.
16 70 27690 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
71 28690 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
72 29690 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
```

### Download and Build Amazon FreeRTOS for the Microchip Curiosity PIC32MZEF<a name="download-ota-mchip"></a><a name="mch-dowload-demo"></a>

**To download the Amazon FreeRTOS OTA demo code**

1. Browse to the AWS IoT console and from the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. From the list of software configurations, choose **Connect to AWS IoT \- Microchip**\. Choose the configuration name, not the **Download** link\.
**Note**  
The OTA Updates feature is currently in beta\.

1. Under **Libraries**, choose **Add another library**, and then choose **OTA Updates**\.

1. Under **Demo projects** choose **OTA Update**\.

1. Under **Name required** type a name for your custom Amazon FreeRTOS software configuration\.

1. Choose **Create and download**\.<a name="mch-build-demo"></a>

**To build the OTA update demo application**

1. Extract the \.zip file you just downloaded\.

1. Follow the instructions in [Getting Started with Amazon FreeRTOS](freertos-getting-started.md) to import the `aws_demos` project into the MPLAB X IDE, configure your AWS IoT endpoint, your Wi\-Fi SSID and password, and a private key and certificate for your board\.

1. Open `aws_demos/lib/aws/ota/aws_codesign_keys.h`\.

1. Paste the contents of your code signing certificate into the `static const char signingcredentialSIGNING_CERTIFICATE_PEM` variable\. Following the same format as aws\_clientcredential\_keys\.h, each line must end with the new line character \('\\n'\) and be surrounded by quotes\.

   For example, your certificate should look similar to the following:

   ```
   "-----BEGIN CERTIFICATE-----\n"
   "MIIBXTCCAQOgAwIBAgIJAM4DeybZcTwKMAoGCCqGSM49BAMCMCExHzAdBgNVBAMM\n"
   "FnRlc3Rf62lnbmVyQGFtYXpvbi5jb20wHhcNMTcxMTAzMTkxODM1WhcNMTgxMTAz\n"
   "MTkxODM2WjAhMR8wHQYDVQBBZZZ0ZXN0X3NpZ25lckBhbWF6b24uY29tMFkwEwYH\n"
   "KoZIzj0CAQYIKoZIzj0DAQcDQgAERavZfvwL1X+E4dIF7dbkVMUn4IrJ1CAsFkc8\n"
   "gZxPzn683H40XMKltDZPEwr9ng78w9+QYQg7ygnr2stz8yhh06MkMCIwCwYDVR0P\n"
   "BAQDAgeAMBMGA1UdJQQMMAoGCCsGAQUFBwMDMAoGCCqGSM49BAMCA0gAMEUCIF0R\n"
   "r5cb7rEUNtWOvGd05MacrgOABfSoVYvBOK9fP63WAqt5h3BaS123coKSGg84twlq\n"
   "TkO/pV/xEmyZmZdV+HxV/OM=\n"
   "-----END CERTIFICATE-----\n";
   ```

1. Paste the contents of code signing private key into the `static const char signingcredentialSIGNING_PRIVATE_KEY_PEM` variable\. Following the same format as aws\_clientcredential\_keys\.h, each line must end with the new line character \('\\n'\) and be surrounded by quotes\.

1. Open the project in MPLAB X IDE and build it to make sure it compiles without errors\.

1. Start a terminal emulator and connect to your board using the following settings:
   + Baud rate: 115200
   + Data bits: 8
   + Parity: None
   + Stop bits: 1

1. Run the project on your board to make sure it can connect to Wi\-Fi and the AWS IoT MQTT message broker\.

When you run the project, the MPLAB X IDE should open an output window\. Make sure the ICD4 tab is selected and you should see the following output\.

```
*****************************************************

 
Connecting to MPLAB ICD 4...

Currently loaded versions:
Application version............01.02.16
Boot version...................01.00.00
FPGA version...................01.00.00
Script version.................00.02.18
Script build number............fd44437f19
Application build number.......0123456789

Target voltage detected
Target device PIC32MZ2048EFM100 found.
Device Id Revision = 0xA1
Serial Number:
Num0 = ec4f6d3c
Num1 = 6b845410


Erasing...

The following memory area(s) will be programmed:
program memory: start address = 0x1d000000, end address = 0x1d08f7ff
configuration memory
boot config memory

Programming/Verify complete

Running
```

The terminal emulator should display text like the following:

```
AWS Validate: no valid signature in descr: 0xbd000000
AWS Validate: no valid signature in descr: 0xbd100000


>AWS Launch:  No Map performed. Running directly from address: 0x9d000020?
AWS Launch:  wait for app at: 0x9d000020
WILC1000: Initializing...
0 0 

>[None] Seed for randomizer: 1172751941
1 0 [None] Random numbers: 00004272 00003B34 00000602 00002DE3
Chip ID 1503a0

[spi_cmd_rsp][356][nmi spi]: Failed cmd response read, bus error...

[spi_read_reg][1086][nmi spi]: Failed cmd response, read reg (0000108c)...

[spi_read_reg][1116]Reset and retry 10 108c

Firmware ver. : 4.2.1

Min driver ver : 4.2.1

Curr driver ver: 4.2.1

WILC1000: Initialization successful!

Start Wi-Fi Connection...
Wi-Fi Connected
2 7219 [IP-task] vDHCPProcess: offer c0a804beip
3 7230 [IP-task] vDHCPProcess: offer c0a804beip
4 7230 [IP-task] 

IP Address: 192.168.4.190
5 7230 [IP-task] Subnet Mask: 255.255.240.0
6 7230 [IP-task] Gateway Address: 192.168.0.1
7 7230 [IP-task] DNS Server Address: 208.67.222.222


8 7232 [OTA] OTA demo version 0.9.0
9 7232 [OTA] Creating MQTT Client...
10 7232 [OTA] Connecting to broker...
11 7232 [OTA] Sending command to MQTT task.
12 7232 [MQTT] Received message 10000 from queue.
13 8501 [IP-task] Socket sending wakeup to MQTT task.
14 10207 [MQTT] Received message 0 from queue.
15 10256 [IP-task] Socket sending wakeup to MQTT task.
16 10256 [MQTT] Received message 0 from queue.
17 10256 [MQTT] MQTT Connect was accepted. Connection established.
18 10256 [MQTT] Notifying task.
19 10257 [OTA] Command sent to MQTT task passed.
20 10257 [OTA] Connected to broker.
21 10258 [OTA Task] Sending command to MQTT task.
22 10258 [MQTT] Received message 20000 from queue.
23 10306 [IP-task] Socket sending wakeup to MQTT task.
24 10306 [MQTT] Received message 0 from queue.
25 10306 [MQTT] MQTT Subscribe was accepted. Subscribed.
26 10306 [MQTT] Notifying task.
27 10307 [OTA Task] Command sent to MQTT task passed.
28 10307 [OTA Task] [OTA] Subscribed to topic: $aws/things/Microchip/jobs/$next/get/accepted

29 10307 [OTA Task] Sending command to MQTT task.
30 10307 [MQTT] Received message 30000 from queue.
31 10336 [IP-task] Socket sending wakeup to MQTT task.
32 10336 [MQTT] Received message 0 from queue.
33 10336 [MQTT] MQTT Subscribe was accepted. Subscribed.
34 10336 [MQTT] Notifying task.
35 10336 [OTA Task] Command sent to MQTT task passed.
36 10336 [OTA Task] [OTA] Subscribed to topic: $aws/things/Microchip/jobs/notify-next

37 10336 [OTA Task] [OTA] Check For Update #0
38 10336 [OTA Task] Sending command to MQTT task.
39 10336 [MQTT] Received message 40000 from queue.
40 10366 [IP-task] Socket sending wakeup to MQTT task.
41 10366 [MQTT] Received message 0 from queue.
42 10366 [MQTT] MQTT Publish was successful.
43 10366 [MQTT] Notifying task.
44 10366 [OTA Task] Command sent to MQTT task passed.
45 10376 [IP-task] Socket sending wakeup to MQTT task.
46 10376 [MQTT] Received message 0 from queue.
47 10376 [OTA Task] [OTA] Set job doc parameter [ clientToken: 0:Microchip ]
48 10376 [OTA Task] [OTA] Missing job parameter: execution
49 10376 [OTA Task] [OTA] Missing job parameter: jobId
50 10376 [OTA Task] [OTA] Missing job parameter: jobDocument
51 10378 [OTA Task] [OTA] Missing job parameter: ts_ota
52 10378 [OTA Task] [OTA] Missing job parameter: files
53 10378 [OTA Task] [OTA] Missing job parameter: streamname
54 10378 [OTA Task] [OTA] Missing job parameter: certfile
55 10378 [OTA Task] [OTA] Missing job parameter: filepath
56 10378 [OTA Task] [OTA] Missing job parameter: filesize
57 10378 [OTA Task] [OTA] Missing job parameter: sig-sha256-ecdsa
58 10378 [OTA Task] [OTA] Missing job parameter: fileid
59 10378 [OTA Task] [OTA] Missing job parameter: attr
60 10378 [OTA Task] [OTA] Returned buffer to MQTT Client.
61 11367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
62 12367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
63 13367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
64 14367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
65 15367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
66 16367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
```

This output shows the Microchip Curiosity PIC32MZEF is able to connect to AWS IoT and subscribe to the MQTT topics required for OTA Updates\. The `Missing job parameter` messages are expected because there are no OTA update jobs pending\.

### Download and Build Amazon FreeRTOS for a Custom Hardware Platform<a name="download-freertos-to-port"></a>

Download the Amazon FreeRTOS source from [GitHub](https://github.com/aws/amazon-freertos)\. Create a project in your IDE including all necessary sources and libraries\.

Build and run the project to make sure it can connect to AWS IoT\.

For more information about porting Amazon FreeRTOS to a new platform, see [Amazon FreeRTOS Porting Guide](porting-guide.md)\.