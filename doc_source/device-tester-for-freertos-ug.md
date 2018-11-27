# AWS IoT Device Tester for Amazon FreeRTOS User Guide<a name="device-tester-for-freertos-ug"></a>

AWS IoT Device Tester allows you to test that the Amazon FreeRTOS operating system works locally on your device and can communicate with the AWS IoT cloud\. AWS IoT Device Tester checks if the porting layer interfaces for Amazon FreeRTOS libraries function correctly on top of microcontroller board device drivers\. In addition, it performs end\-to\-end tests with AWS IoT Core \(for example, to test if the board is able to send or receive MQTT messages and process correctly\)\. AWS IoT Device Tester for Amazon FreeRTOS uses the test cases published in the [Amazon FreeRTOS GitHub repository](https://github.com/aws/amazon-freertos)\. AWS IoT Device Tester consists of a Test Manager command\-line tool and a set of test cases\.

Test Manager runs on a host computer \(Windows, Mac, or Linux\) that is connected to the device to be tested\. The Test Manager executes test cases and aggregates results\. It also provides a command line interface to manage test execution\. Test cases contain test logic and set up the resources required for tests\.

Test cases are part of the application binary image that is flashed onto your board\. Application binary images include Amazon FreeRTOS, the semiconductor vendor’s ported Amazon FreeRTOS interfaces, and board device drivers\. Test Cases run as embedded applications and verify the ported Amazon FreeRTOS interfaces function correctly on top of the device drivers\.

The following diagram shows the test infrastructure setup:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/devicetester_afr.png)

## Prerequisites<a name="dev-tester-prereqs"></a>

This section describes the prerequisites for testing microcontrollers with AWS IoT Device Tester\.

### Download Amazon FreeRTOS<a name="download-afr"></a>

You can download the version of Amazon FreeRTOS that you want to test from [GitHub](https://github.com/aws/amazon-freertos)\. If you are using Windows, you must keep the file path short\. For example, to avoid a Windows limitation with long file paths, clone to `C:\AFreeRTOS` rather than `C:\Users\username\programs\projects\AmazonFreeRTOS\`\.

### Download AWS IoT Device Tester for Amazon FreeRTOS<a name="download-dev-tester-afr"></a>

Every version of Amazon FreeRTOS has a corresponding version of AWS IoT Device Tester for performing qualification tests\. Download the appropriate version of [AWS IoT Device Tester](https://aws.amazon.com/freertos/device-tester)\.

Extract AWS IoT Device Tester into a location on the file system where you have read and write permissions\. Due to a path length limitation, on Microsoft Windows, extract AWS IoT Device Tester into a root directory like C:\\ or D:\\\.

### Create and Configure an AWS Account<a name="config-aws-account"></a>

If you don't have an AWS account, follow the instructions on the [AWS webpage](https://aws.amazon.com) to create one\. Choose **Create an AWS Account** and follow the prompts\.

#### Create an IAM User in Your AWS Account<a name="create-iam-user-afr"></a>

When you create an AWS account, a root user that has access to all resources in your account is created for you\. It is a best practice to create another user for everyday tasks\. To create an IAM user, follow the instructions in [Creating an IAM User in Your AWS Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)\. For more information about the root user, see [The AWS Account Root User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)\.

#### Create and Attach an IAM Policy to Your AWS Account<a name="create-policy-bk-afr"></a>

IAM policies grant your IAM user access to AWS resources\. 

**To create an IAM policy**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, and then choose **Create Policy**\.

1. Select the **JSON** tab and copy and paste the policy template located in [Permissions Policy Template](policy-template.md) the [](policy-template.md) into the editor window\.

1. Choose **Review policy**\.

1. In **Name**, enter a name for your policy\. In **Description**, enter an optional description\. Choose **Create Policy**\.

After you create an IAM policy, you must attach it to your IAM user\.

**To attach an IAM policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\. Find and select your IAM user\.

1. Choose **Add permissions**, and then choose **Attach existing policies directly**\. Find and select your IAM policy, choose **Next: Review**, and then choose **Add Permissions**\.

### Install the AWS Command Line Interface \(CLI\)<a name="install-cli"></a>

 You will need to use the CLI to perform some operations, if you don't have the CLI installed, follow the instructions in [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

## Test to Qualify Your Microcontroller Board for the First Time<a name="qual-steps"></a>

You can use AWS IoT Device Tester to test as you port the Amazon FreeRTOS interfaces\. After you have ported the Amazon FreeRTOS interfaces for your board’s device drivers, you use AWS IoT Device Tester to run the qualification tests on your microcontroller board\. 

### Add Library Porting Layers<a name="add-port-layer"></a>

To add library porting layers for Amazon FreeRTOS device libraries \(TCP/IP, WiFi, and so on\) compatible with your MCU architecture, you must:

1. Implement the `configPRINT_STRING()` method before running AWS IoT Device Tester tests\. AWS IoT Device Tester calls the `configPRINT_STRING()` macro to output test results as human\-readable ASCII strings\. 

1. Port the drivers to implement the Amazon FreeRTOS library's interfaces\. For more information, see the [Amazon FreeRTOS Qualification Developer Guide](https://github.com/aws/amazon-freertos/blob/master/tests/Amazon%20FreeRTOS%20Qualification%20Program%20Developer%20Guide.pdf)\.

### Configure Your AWS Credentials<a name="cfg-aws-afr"></a>

You must configure your AWS credentials in the `<devicetester_extract_location>/devicetester_afreertos_[win|mac|linux]/configs/ config.json`\. You can specify your credentials in one of two ways:
+ Environment variables
+ Credentials file

#### Configuring AWS Credentials with Environment Variables<a name="config-env-vars-afr"></a>

Environment variables are variables maintained by the operating system and used by system commands\. AWS IoT Device Tester can use the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables to store your AWS credentials\. The way you set environment variables depends on the operating system you are running\.

**To set environment variables on Windows**

1. From the Windows 10 desktop, open the **Power User Task Menu**\. To open the menu, move your mouse cursor to the bottom\-left corner of the screen \(the **Start** menu icon\) and right\-click\.

1. From the **Power User Task Menu**, choose **System**, and then choose **Advanced System Settings**\.
**Note**  
In Windows 10, you might need to scroll to **Related settings** and choose **System info**\. In **System**, choose **Advanced system settings**\.

   In **System Properties**, choose the **Advanced** tab, and then choose the **Environment Variables** button\.

1. Under **User variables for <user\-name>**, choose **New** to create an environment variable\. Enter a name and value for each environment variable\. 

**To set environment variables on macOS, Linux, or UNIX**
+ Open `~/.bash_profile` in any text editor and add the following lines: 

  ```
  export AWS_ACCESS_KEY_ID="<your-aws-access-key-id>"
  export AWS_SECRET_ACCESS_KEY="<your-aws-secret-key>"
  ```

  Replace the items in angle brackets \(< & >\) with your AWS access key ID and AWS secret key\.

After you set your environment variables, close your command line window or terminal and reopen it so the new values take effect\.

To configure your AWS credentials using environment variables, set the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`\. In the `config.json`, for `method`, specify `environment`:

```
{
	"awsRegion": "us-west-2",
	"auth": {
		"method": "environment"
	}
}
```

#### Configuring AWS Credentials with a Credentials File<a name="config-cred-file"></a>

Create a credentials file that contains your credentials\. AWS IoT Device Tester uses the same credentials file as the AWS CLI\. For more information, see \. You must also specify a named profile\. For more information, see [Configuration and Credential Files](https://docs.aws.amazon.com/cli/latest/userguide/cli-config-files.html)\. The following is an example JSON snippet that shows how to specify AWS credentials using a credentials file in the config\.json file:

```
{
	"awsRegion": "us-west-2",
	"auth": {
		"method": "file",
		"credentials": {
			"profile": "default"
		}
	}
}
```

#### Configuring the AWS Region<a name="cfg-aws-rgn"></a>

AWS IoT Device Tester creates cloud resources in your AWS account\. You can specify the AWS Region to use for testing by setting the `awsRegion` parameter in the config\.json file\. By default, AWS IoT Device Tester uses the us\-west\-2 region\.

### Create a Device Pool in AWS IoT Device Tester<a name="cfg-dt-dp"></a>

Devices to be tested are organized in device pools\. Each device pool consists of one or more devices with identical specifications\. You can configure Device Tester to test a single device in a pool or multiple devices in a pool\. To accelerate the qualification process, AWS IoT Device Tester can test devices with the same specification in parallel\. It uses a round\-robin method to execute a different test group on each device in a device pool\. 

#### Configuring AWS IoT Device Tester for Single Device Testing<a name="config-single-dp"></a>

You define a device pool by editing the `device.json` file template in the configs folder\. The following is an example `device.json` file used to create a device pool with one device:

```
[
  {
    "id": "<pool-id>",
    "sku": "<sku>",
    "features": [
      {
        "name": "WIFI",
        "value": "Yes | No"
      },
      {
        "name": "OTA",
        "value": "Yes | No"
      },
      {
        "name": "TCP/IP",
        "value": "On-chip | Offloaded | No"
      },
      {
        "name": "TLS",
        "value": "On-chip | Offloaded | No"
      }
    ],
    "devices": [
      {
        "id": "<device-id>",
        "connectivity": {
          "protocol": "uart",
          "serialPort": "<serial-port>"
        },
        "identifiers": [
          {
            "name": "serialNo",
            "value": "<serialNo-value>"
          }
        ]
      }
    ]
  }
]
```

The following list describes the attributes used in the `device.json` file:

`id`  
A user\-defined alphanumeric ID that uniquely identifies a pool of devices\. Devices that belong to a pool must be of the same type\. When a suite of tests is running, devices in the pool are used to parallelize the workload\.

`sku`  
An alphanumeric value that uniquely identifies the board you are testing\. The SKU is used to track qualified boards\.  
If you want to list your board in AWS Partner Device Catalog, the SKU you specify here must match the SKU that you use in the listing process\.

`features`  
An array that contains the device's supported features\. The Device Tester uses this information to select the qualification tests to run\.  
Supported values are:  
+ `TCP/IP`: Indicates if your board supports a TCP/IP stack and whether it is supported on\-chip \(MCU\) or offloaded to another module\.
+ `WIFI`: Indicates if your board has Wi\-Fi capabilities\.
+ `TLS`: Indicates if your board supports TLS and if it is supported on\-chip \(MCU\) or offloaded to another module\.
+ `OTA`: Indicates if your board supports over\-the\-air \(OTA\) update functionality\.

`devices.id`  
A user\-defined unique identifier for the device being tested\.

`devices.connectivity.protocol`  
The communication protocol used to communicate with this device\. Supported value: `uart`\.

`devices.connectivity.serialPort`  
The serial port of the host computer used to connect to the devices being tested\.

`identifiers`  
Optional\. An array of arbitrary name\-value pairs\. You can use these values in the build and flash commands described in the next section\.

#### Configuring AWS IoT Device Tester for Multiple Device Testing<a name="config-mult-dp"></a>

You can add multiple devices by editing the `devices` section of the `device.json` template in the `configs` folder\. For more information about the structure and contents of the `device.json` file, see [Configuring AWS IoT Device Tester for Single Device Testing](#config-single-dp)\.

**Note**  
All devices in the same pool must be of same technical specification and SKU\.

The following is an example `device.json` file used to create a device pool with multiple devices:

```
[{
	"id": "<pool-id>",
	"sku": "<sku>",
	"features": [
		{
			"name": "WIFI",
			"value": "Yes | No"
		},
		{
			"name": "OTA",
			"value": "Yes | No"
		},
		{
			"name": "TCP/IP",
			"value": "On-chip | Offloaded | No"
		},
		{
			"name": "TLS",
			"value": "On-chip | Offloaded | No"
		}
	],
	"devices": [{
		"id": "<device-id1>",
		"connectivity": {
			"protocol": "uart",
			"serialPort": "<computer_serial_port_1>"
		},
		"identifiers": [{
			"name": "serialNo",
			"value": "<serialNo-value>"
		}]
		"id": "<device-id2>",
		"connectivity": {
			"protocol": "uart",
			"serialPort": "<computer_serial_port_2>"
		},
		"identifiers": [{
			"name": "serialNo",
			"value": "<serialNo-value>"
		}]
	}]
}]
```

### Configure Build, Flash, and Test Settings<a name="cfg-dt-ud"></a>

For AWS IoT Device Tester to build and flash test cases on to your board automatically, you must configure AWS IoT Device Tester with command line interfaces of your build and flash tools\. The build and flash settings are configured in the `userdata.json` template file located in the config folder\.

#### Configure Settings for Testing One Device<a name="config-userdata-single"></a>

The `userdata.json` must have the following structure:

```
{  
   "sourcePath":"<path-to-afr-source-code>",
   "buildTool":{  
      "name":"<your-build-tool-name>",
      "version":"<your-build-tool-version>",
      "command":[  
         "<your-build-script>"
      ]
   },
   "flashTool":{  
      "name":"<your-flash-tool-name>",
      "version":"<your-flash-tool-version>",
      "command":[  
         "<your-flash-script>"
      ]
   },
   "clientWifiConfig":{  
      "wifiSSID":"<your-wifi-ssid",
      "wifiPassword":""<your-wifi-password>",
      "wifiSecurityType":"<wifi-security-type>"
   },
   "testWifiConfig":{  
      "wifiSSID":"<your-wifi-ssid",
      "wifiPassword":""<your-wifi-password>",
      "wifiSecurityType":"<wifi-security-type>"
   },
   "otaConfiguration":{  
      "otaFirmwareFilePath":"<path-to-the-device-binary>",
      "deviceFirmwareFileName":"<your-device-firmware-name>.bin",
      "awsSignerPlatform":"AmazonFreeRTOS-Default",
      "awsSignerCertificateArn":"arn:aws:acm:us-east-1:1000000001:certificate/e416379d-f3d6-46f3-868e-8721075ff076",
      "awsUntrustedSignerCertificateArn":"arn:aws:acm:us-east-1:1000000001:certificate/0c81e2c6-f85e-46b1-9ed1-2c404309b210",
      "awsSignerCertificateFileName":"ecdsa-sha256-signer.crt.pem",
      "compileCodesignerCertificate":true
   }
}
```

The following lists the attributes used in the `userdata.json` file:

`sourcePath`  
The path to the root of the ported Amazon FreeRTOS source code\. AWS IoT Device Tester stores the value in the `{{testData.sourcePath}}` variable\.

`buildTool`  
The full path to your build script \(\.bat or \.sh\) that contains the commands to build your source code\.  
If you are using an IDE, you must provide the command line to the IDE to run in headless mode\.

`flashTool`  
Full path to your flash script \(\.sh or \.bat\) that contains the flash commands for your device\.

`clientWifiConfig`  
Client Wi\-Fi configuration\. The Wi\-Fi library tests require an MCU board to connect to two access points\. This attribute configures the Wi\-Fi settings for the first access point\. The client Wi\-Fi settings are configured in `$AFR_HOME/tests/common/include/aws_clientcredential.h.`\. The following macros are set by using the values found in `aws_clientcredential.h`\. Some of the Wi\-Fi test cases expect the access point to have some security and not to be open\.  
+ wifi\_ssid: The Wi\-Fi SSID as a C string\.
+ wifi\_password: The Wi\-Fi password as a C string\.
+ wifiSecurityType: The type of Wi\-Fi security used\.

`testWifiConfig`  
Test Wi\-Fi configuration\. The Wi\-Fi library tests require a board to connect to two access points\. This attribute configures the second access point\. The test Wi\-Fi settings are configured in `$AFR_HOME/tests/common/wifi/aws_test_wifi.c.`\. The following macros are set by using the values found in `aws_test_wifi.c`\. Some of the Wi\-Fi test cases expect the access point to have some security and not to be open\.  
If your board does not support Wi\-Fi, you must still include the `clientWifiConfig` and `testWifiConfig` section in your `device.json` file, but you can omit values for these attributes\.
+ testwifiWIFI\_SSID: The Wi\-Fi SSID as a C string\.
+ testwifiWIFI\_PASSWORD: The Wi\-Fi password as a C string\.
+ testwifiWIFI\_SECURITY: The type of Wi\-Fi security used\. One of the following values:
  + `eWiFiSecurityOpen`
  + `eWiFiSecurityWEP`
  + `eWiFiSecurityWPA`
  + `eWiFiSecurityWPA2`

`otaConfiguration`  
The OTA configuration\.    
`otaFirmwareFilePath`  
The full path to the OTA image created after the build\.  
`deviceFirmwareFileName`  
The full file path on the MCU device where the OTA firmware will be downloaded\. Some devices do not use this field, but you still must provide a value\.  
`awsSignerPlatform`  
The signing algorithm used by AWS Code Signer while creating the OTA update job\. Currently, the possible values for this field are `AmazonFreeRTOS-TI-CC3220SF` and `AmazonFreeRTOS-Default`\.  
`awsSignerCertificateArn`  
The Amazon Resource Name \(ARN\) for the trusted certificate uploaded to AWS Certificate Manager \(ACM\)\. For more information about creating a trusted certificate, see [Creating a Code Signing Certificate](https://docs.aws.amazon.com/freertos/latest/userguide/ota-code-sign-cert.html)\.  
`awsUntrustedSignerCertificateArn`  
The Amazon Resource Name \(ARN\) for a certificate uploaded to ACM which your device should not trust\. This is used to test invalid certificate test cases\.  
`compileCodesignerCertificate`  
Set to `true` if the code\-signer signature verification certificate is not provisioned or flashed, so it must be compiled into the project\. AWS IoT Device Tester fetches the trusted certificate from ACM and compiles it into `aws_codesigner_certifiate.h`\.

#### Configure Settings for Testing Multiple Devices<a name="config-userdata-mult"></a>

Build, flash, and test settings are made in the `userdata.json` file\. The following JSON example shows how you can configure AWS IoT Device Tester for testing multiple devices:

```
{  
       "sourcePath":"<absolute-path-to/amazon-freertos>",
       "buildTool":{  
          "name":"<your-build-tool-name>",
          "version":"<your-build-tool-version>",
          "command":[  
             "<absolute-path-to/build-parallel-script> {{testData.sourcePath}}"
          ]
       },
       "flashTool":{  
          "name":"<your-flash-tool-name>",
          "version":"<your-flash-tool-version>",
          "command":[  
             "<absolute-path-to/flash-parallel-script> {{testData.sourcePath}} {{device.connectivity.serialPort}}"
          ]
       },
       "clientWifiConfig":{  
          "wifiSSID":"<ssid>",
          "wifiPassword":"<password>",
          "wifiSecurityType":"eWiFiSecurityOpen | eWiFiSecurityWEP | eWiFiSecurityWPA | eWiFiSecurityWPA2"
       },
       "testWifiConfig":{  
          "wifiSSID":"<ssid>",
          "wifiPassword":"<password>",
          "wifiSecurityType":"eWiFiSecurityOpen | eWiFiSecurityWEP | eWiFiSecurityWPA | eWiFiSecurityWPA2"
       },
       "otaConfiguration":{  
          "otaFirmwareFilePath":"{{testData.sourcePath}}/<relative-path-to/ota-image-generated-in-build-process>",
          "deviceFirmwareFileName":"<absolute-path-to/ota-image-on-device>",
          "awsSignerPlatform":"AmazonFreeRTOS-Default | AmazonFreeRTOS-TI-CC3220SF",
          "awsSignerCertificateArn":"arn:aws:acm:<region>:<account-id>:certificate:<certificate-id>",
          "awsUntrustedSignerCertificateArn":"arn:aws:acm:<region>:<account-id>:certificate:<certificate-id>",
          "awsSignerCertificateFileName":"<awsSignerCertificate-file-name>",
          "compileCodesignerCertificate":true | false
       }
    }
```

The following lists the attributes used in `userdata.json`:

`sourcePath`  
The path to the root of the ported Amazon FreeRTOS source code\. AWS IoT Device Tester stores the value in the `{{testData.sourcePath}}` variable\.

`buildTool`  
The full path to your build script \(\.bat or \.sh\) that contains the commands to build your source code\. All references to the source code path in the build command must be replaced by the AWS IoT Device Tester variable `{{testdata.sourcePath}}`\.

`flashTool`  
Full path to your ﬂash script \(\.sh or \.bat\) that contains the ﬂash commands for your device\. All references to the source code path in the ﬂash command must be replaced by the AWS IoT Device Tester variable `{{testdata.sourcePath}}`\.

`clientWifiConfig`  
Client Wi\-Fi configuration\. The Wi\-Fi library tests require an MCU board to connect to two access points\. This attribute configures the Wi\-Fi settings for the first access point\. The client Wi\-Fi settings are configured in `$AFR_HOME/tests/common/include/aws_clientcredential.h.`\. The following macros are set using the values found in `aws_clientcredential.h`\. Some of the Wi\-Fi test cases expect the access point to have some security and not to be open\.  
+ wifi\_ssid: The Wi\-Fi SSID as a C string\.
+ wifi\_password: The Wi\-Fi password as a C string\.
+ wifiSecurityType: The type of Wi\-Fi security used\.

`testWifiConfig`  
Test Wi\-Fi configuration\. The Wi\-Fi library tests require a board to connect to two access points\. This attribute configures the second access point\. The test Wi\-Fi settings are configured in `$AFR_HOME/tests/common/wifi/aws_test_wifi.c.`\. The following macros are set using the values found in `aws_test_wifi.c`\. Some of the Wi\-Fi test cases expect the access point to have some security and not to be open\.  
If your board does not support Wi\-Fi, you must still include the `clientWifiConfig` and `testWifiConfig` section in your `device.json` file, but you can omit values for these attributes\.
+ testwifiWIFI\_SSID: The Wi\-Fi SSID as a C string\.
+ testwifiWIFI\_PASSWORD: The Wi\-Fi password as a C string\.
+ testwifiWIFI\_SECURITY: The type of Wi\-Fi security used\. One of the following values:
  + `eWiFiSecurityOpen`
  + `eWiFiSecurityWEP`
  + `eWiFiSecurityWPA`
  + `eWiFiSecurityWPA2`

`otaConfiguration`  
The OTA configuration\.    
`otaFirmwareFilePath`  
The full path to the OTA image created after the build\.  
`deviceFirmwareFileName`  
The name of the OTA firmware file to be downloaded to the board\.  
`awsSignerPlatform`  
The signing algorithm used by AWS Code Signer while creating the OTA update job\. Currently, the possible values for this field are `AmazonFreeRTOS-TI-CC3220SF` and `AmazonFreeRTOS-Default`\.  
`awsSignerCertificateArn`  
The Amazon Resource Name \(ARN\) for the trusted certificate uploaded to AWS Certificate Manager \(ACM\)\. For more information about creating a trusted certificate, see [Creating a Code Signing Certificate](https://docs.aws.amazon.com/freertos/latest/userguide/ota-code-sign-cert.html)\.  
`awsUntrustedSignerCertificateArn`  
The ARN for the code\-signing certificate uploaded to ACM\.  
`compileCodesignerCertificate`  
Set to `true` if the code\-signer signature verification certificate is not provisioned or flashed, so it must be compiled into the project\. AWS IoT Device Tester fetches the trusted certificate from ACM and compiles it into `aws_codesigner_certifiate.h`\.

#### AWS IoT Device Tester Variables<a name="dt-vars"></a>

The commands to build your code and flash the device might require connectivity or other information about your devices to run successfully\. AWS IoT Device Tester allows you to reference device information in flash and build commands using [JsonPath](http://goessner.net/articles/JsonPath/)\. By using simple JsonPath expressions, you can fetch the required information as specified in your `device.json` file\.

##### AWS IoT Device Tester Variables and Concurrent Testing<a name="dt-var-concurrent-testing"></a>

To enable parallel builds of the source code for different test groups, AWS IoT Device Tester copies the source code to a results folder inside the AWS IoT Device Tester extracted folder\. The source code path in your build or flash command must be referenced using the `testdata.sourcePath` variable\. AWS IoT Device Tester replaces this variable with a temporary path of the copied source code\.

##### File Paths and the Windows Operating System<a name="win-filepaths"></a>

If you are running AWS IoT Device Tester on Windows, use forward slashes \(/\) in your file paths in AWS IoT Device Tester config files\. For example, `sourcePath` in `userdata.json` should be represented as `c:/<dir1>/<dir2>`\.

## Running the Amazon FreeRTOS Qualification Suite<a name="run-tests"></a>

You use the AWS IoT Device Tester executable to interact with AWS IoT Device Tester\. The following command line shows you how to run the qualification tests for a device pool \(set of identical devices\)\.

devicetester\_*\[linux \| mac \| win\_x86\-64\]* run\-suite \-\-suite\-id *<suite\-id>* \-\-pool\-id *<your\-device\-pool>* \-\-userdata *<userdata\.json>*

The `userdata.json` file should be located in the `<devicetester_extract_location> /devicetester_afreertos_[win|mac|linux]/configs/` directory\.

**Note**  
If you are running AWS IoT Device Tester on Windows, specify the path to the `userdata.json` by using forward slashes \(/\)\.

Use the following command to run all test groups in a specified suite:

devicetester\_*\[linux \| mac \| win\_x86\-64\]* run\-suite \-\-suite\-id AFQ\_1 \-\-pool\-id *<pool\-id>*

Use the following command to run a specific test group:

devicetester\_*\[linux \| mac \| win\_x86\-64\]* run\-suite \-\-suite\-id AFQ\_1 \-\-group\-id *<group\-id>* \-\-pool\-id *<pool\-id>* *<pool\-id>*

`suite-id` and `pool-id` are optional if you are running a single test suite on a single device pool \(that is, you have only one device pool defined in your `device.json` file\)\.AWS IoT Device Tester command line options

suite\-id  
Optional\. Specifies the test suite to run\.

pool\-id  
Specifies the device pool to test\. If you only have one device pool, you can omit this option\.

### AWS IoT Device Tester Commands<a name="dt-cli"></a>

The AWS IoT Device Tester command supports the following operations:

help  
Lists information about the specified command\.

list\-groups  
Lists the groups in a given suite\.

list\-suites  
Lists the available suites\.

run\-suite  
Runs a suite of tests on a pool of devices\.

## Results and Logs<a name="results-logs"></a>

This section describes how to view and interpret test results and logs\.

### Viewing Results<a name="view-results"></a>

After AWS IoT Device Tester executes the qualification test suite, it generates two reports for each run of the qualification test suite: `awsiotdevicetester_report.xml` and `AFQ_Report.xml`\. These reports can be found in *<devicetester\-extract\-location>*/results/*<execution\-id>*/\. 

`awsiotdevicetester_report.xml` is the qualification test report that you submit to AWS for listing your device in the AWS Partner Device Catalog\. The report contains the following elements:
+ The AWS IoT Device Tester version\.
+ The Amazon FreeRTOS version that was tested\.
+ The SKU and the device name specified in the `device.json` file\.
+ The features of the device specified in the `device.json` file\.
+ The aggregate summary of test case results\.
+ A breakdown of test case results by libraries that were tested based on the device features \(for example, FullWiFi, FullMQTT, and so on\)\.

 The `AFQ_report.xml` is a report in standard junit\.xml format, which you can integrate into your exiting CI/CD platforms like Jenkins, Bamboo, and so on\. The report contains the following elements:
+ An aggregate summary of test case results\.
+ A breakdown of test case results by libraries that were tested based on the device features \(for example, FullWiFi, FullMQTT, and so on\)\.

#### Interpreting AWS IoT Device Tester Results<a name="interpreting-results"></a>

The report section in `awsiotdevicetester_report.xml` or `AFQ_report.xml` lists the tests that were run and the results of the tests\.

The first XML tag `<testsuites>` contains the overall summary of the test execution\. For example:

```
<testsuites name="AFQ results" time="5633" tests="184" failures="0" errors="0" disabled="0">
```Attributes used in the `<testsuites>` tag

`name`  
The name of the test suite\.

`time`  
The time \(in seconds\) it took to run the qualification suite\.

`tests`  
The number of test cases executed\.

`failures`  
The number of test cases that were run, but did not pass\.

`errors`  
The number of test cases that AWS IoT Device Tester couldn't execute\.

`disabled`  
This attribute is not used and can be ignored\.

If there are no test case failures or errors, your device meets the technical requirements to run Amazon FreeRTOS and can interoperate with AWS IoT services\. If you choose to list your device in the AWS Partner Device Catalog, you can use this report as qualification evidence\.

In the case of test case failures or errors, you can identify the test case that failed by reviewing the `<testsuites>` XML tags\. The `<testsuite>` XML tags inside the `<testsuites>` tag shows the test case result summary for a test group\.

```
<testsuite name="FullMQTT" package="" tests="16" failures="0" time="76" disabled="0" errors="0" skipped="0">
```

The format is similar to the `<testsuites>` tag, but with an additional attribute called `skipped` that is not used and can be ignored\. Inside each `<testsuite>` XML tag, there are `<testcase>` tags for each of the test cases that were executed for a test group\. For example:

```
<testcase classname="mcu.Full_MQTT" name="AFQP_MQTT_Connect_HappyCase" attempts="1"></testcase>
```Attributes used in the `<testcase>` tag

`name`  
The name of the test case\.

`attempts`  
The number of times AWS IoT Device Tester executed the test case\.

When a test case fails or an error occurs, `<failure>` or `<error>` tags are added to the `<testcase>` tag with additional information for troubleshooting\. For example:

```
<testcase classname="mcu.Full_MQTT" name="AFQP_MQTT_Connect_HappyCase" attempts="1">
	<failure type="Failure">Reason for the test case failure</failure>
	<error>Reason for the test case execution error</error>
</testcase>
```

#### Viewing Logs<a name="view-logs"></a>

You can find logs that AWS IoT Device Tester generates from test execution in `<devicetester-extract-location>/results/<execution-id>/logs`\. Two sets of logs are generated:

`test_manager.log`  
Contains logs generated from the Test Manager component of AWS IoT Device Tester\. For example, logs related configuration, test sequencing, and report generation are here\.

`<test_group_name>.log (for example, Full_MQTT.log)`  
The logs of the test group, including logs from the device under test\.

## Test for Requalifications<a name="requal-test"></a>

As new versions of AWS IoT Device Tester qualification tests are released, as you update your board\-specific packages or device drivers, you can use AWS IoT Device Tester to test your microcontroller boards\. For subsequent qualifications, make sure that you have the latest versions of Amazon FreeRTOS and AWS IoT Device Tester and run the qualification tests again\.:

## Troubleshooting<a name="dt-afr-troublshooting"></a>

We recommend the following workflow for troubleshooting testing an Amazon FreeRTOS device:

1. Read the console output\.

1. Look in the `AFQ_Report.xml` file\.

1. Look in the logs files located under `/results/<uuid>/logs`\.

1. Investigate one of the following problem areas:
   + Device configuration
   + Device interface
   + Device tooling
   + Amazon FreeRTOS source code

### Troubleshooting Device Configuration<a name="troubleshoot-device-config"></a>

When you use AWS IoT Device Tester, you must get the correct configuration files in place before you execute the binary\. If you are getting parsing and configuration errors, your first step should be to locate and use a configuration template appropriate for your environment\.

If you are still having issues, see the debugging following process\.

#### Where Do I Look?<a name="where-to-look"></a>

Start by looking in the `results.xml` file in the `/results/<uuid>` directory\. This file contains all of the test cases that were run and error snippets for each failure\. To get all of the execution logs, look under `/results/<uuid>/<test-case-id>.log` for each test group\.

#### Parsing Errors<a name="parse-error"></a>

Occasionally, a typo in a JSON configuration can lead to parsing errors\. Most of the time, the issue is a result of omitting a bracket, comma, or quote from your JSON file\. AWS IoT Device Tester performs JSON validation and prints debugging information\. It prints the line where the error occurred, the line number, and the column number of the syntax error\. This information should be enough to help you fix the error, but if you are still having issues locating the error, you can perform validation manually in your IDE, a text editor such as Atom or Sublime, or through an online tool like JSONLint\.

#### Required Parameter Missing Error<a name="param-missing"></a>

Because new features are being added to AWS IoT Device Tester, changes to the configuration files might be introduced\. Using an old configuration file might break your configuration\. If this happens, the `<test_case_id>.log` file under `/results/<uuid>/logs` explicitly lists all missing parameters\. AWS IoT Device Tester validates your JSON configuration file schemas to ensure that the latest supported version has been used\.

#### Could Not Start Test Error<a name="could-not-start-test"></a>

You might encounter errors that point to failures during test start\. Since there are several possible causes for this, please make sure to check the following areas for correctness:
+ Make sure that the pool name you've included in your execution command actually exists\. This is referenced directly from your device\.json file\.
+ Ensure that the device\(s\) in your pool have correct configuration parameters\.

#### Device Interface and Port<a name="device-interface"></a>

This section contains information about the device interfaces AWS IoT Device Tester uses to connect to your devices\.

##### Supported Platforms<a name="platform-differences"></a>

AWS IoT Device Tester supports Linux, macOS, and Windows\. All three platforms have different naming schemes for serial devices that are attached to them:
+ Linux: /dev/tty\*
+ macOS: /dev/tty\.\*
+ Windows: COM\*

To check your device ID:
+ For Linux/macOS, open a terminal and run ls /dev/tty\*\.
+ For Windows, open Device Manager and expand the serial devices group\.

##### Device Interfaces<a name="device-interfaces"></a>

Each embedded device is different, which means that they can have one or more serial ports\. It is common for devices to have two ports when connected to a machine, one being a data port for flashing the device and the other to read output\. It is crucial to set the correct port in your `device.json` file\. Otherwise, flashing or reading output might fail\.

In the case of multiple ports, make sure to use the data port of the device in your `device.json` file\. For example, if you plug in an Espressif WRover device and the two ports assigned to it are /dev/ttyUSB0 and /dev/ttyUSB1, Use `/dev/ttyUSB1` in your `device.json` file\.

For Windows, follow the same logic\.

##### Reading Device Data<a name="reading-device-data"></a>

AWS IoT Device Tester uses individual device build and flash tooling to specify port configuration\. If you are testing your device and don't get output, try the following default settings:
+ Baud rate \- 115200
+ Data Bits \- 8
+ Parity \- None
+ Stop Bits \- 1
+ Flow Control \- None

These settings are handled by AWS IoT Device Tester without any configuration on your end\. However, you can use the same method to manually read device output\. On on Linux or macOS, you can do this with the screen command\. On Windows, you can use a program such as TeraTerm\.

`Screen: screen /dev/cu.usbserial 115200`

`TeraTerm: Use the above-provided settings to set the fields explicitly in the GUI.`

#### Development Toolchain Problems<a name="dev-toolchain"></a>

This section discusses problems that can occur with your toolchain\.

##### Code Composer Studio on Ubuntu<a name="ccs-ubuntu"></a>

For TI devices, we recommend that you download and install Code Composer Studio 7\.3\. The newer versions of Ubuntu \(17\.10 and 18\.04\) have a version of the glibc package that is not compatible with Code Composer Studio 7\.*x* versions\. We recommended that you install Code Composer Studio version 8\.2 or later\.

Symptoms of incompatibility might include:
+ Amazon FreeRTOS failing to build or flash to your device\.
+ The Code Composer Studio installer might freeze\.
+ No log output is displayed in the console during the build or flash process\.
+ Build command attempting to launch in GUI mode even when invoked as headless\.

#### Amazon FreeRTOS Source Code<a name="afr-src"></a>

The following sections discuss troubleshooting problems with the Amazon FreeRTOS source code\.

##### Code Errata<a name="code-err"></a>

Every Amazon FreeRTOS release is bundled with a document, located under the `/amazon-freertos/tests` directory, that contains all of the errata information for that release\. We recommend that you read through this document before you run any tests\.

The errata document contains an entry for any devices that currently fail tests due to reasons like:
+ The hardware doesn't support a specific feature\.
+ The hardware supports the feature, but Amazon FreeRTOS doesn't support it on the device yet\.
+ The hardware supports the feature, but the underlying software stack doesn't support the hardware \(non\-AFR\)\.

If the errata does not contain information specific to your device, continue the debugging process as outlined in the next section\.

##### Debugging Amazon FreeRTOS<a name="afr-debug"></a>

When a source code error occurs, AWS IoT Device Tester will write debug output to the *<test\-group\-id>*\.log file in the `/results/<uuid>/logs` directory\. Search the file for any instances of errors\. The error will point to a location in the Amazon FreeRTOS source code\. You can then use the line number and file path information in that log to reference the piece of source code that resulted in the error\.

#### Logging<a name="dt-logging"></a>

AWS IoT Device Tester logs are placed in a single location\. From the root AWS IoT Device Tester directory, the available files are:
+ `./results/<uuid>`
+ `AFQ_Report.xml`
+ `awsiotdevicetester_report.xml`
+ `/logs/<test_group_id>.log`

The most important logs to look at will be *<test\_group\_id>*\.log and results\.xml\. The latter will contain information about which tests failed with a specific error message\. You can then use the former to dig further into the problem in order to get better context\.

##### Console Errors<a name="err-console"></a>

When AWS IoT Device Tester is run, failures are reported to console with brief messages\. Look in *<test\_group\_id>*\.log to learn more about the error\.

##### Log Errors<a name="err-log"></a>

The *<test\-group\-id>*\.log file is located in the `/results/<uuid>` directory\. Each test execution has a unique test ID that is used to create the *<uuid>* directory\. Individual test group logs are under the *<uuid>* directory\. Use the AWS IoT console to look up the test group that failed and then open the log file for that group in the `/results/<uuid>` directory\. The information in this file includes the full build and flash command output, as well as test execution output and more verbose AWS IoT Device Tester console output\.

##### Path Variables<a name="path-variables"></a>

AWS IoT Device Tester defines the following path variables that can be used in command lines and configuration files:

`{{testData.sourcePath}}`  
A variable that expands to the source code path\.

`{{device.connectivity.serialPort}}`  
A variable that expands to the serial port\.

`{{device.identifiers[?(@.name == 'serialNo')].value}}`  
A variable that expands to the serial number of your device\.

##### <a name="config-files"></a>

The following is an example `userdata.json` file:

```
{  
       "sourcePath":"</path/to/amazon-freertos>",
       "buildTool":{  
          "name":"<TOOL_NAME>",
          "version":"<TOOL_VERSION>",
          "command":[  
             "</path/to/build>.sh {{testData.sourcePath}}"
          ]
       },
       "flashTool":{  
          "name":"<TOOL_NAME>",
          "version":"<TOOL_VERSION>",
          "command":[  
             "</path/to/flash>.sh {{device.connectivity.serialPort}} {{testData.sourcePath}}"
          ]
       },
       "clientWifiConfig":{  
          "wifiSSID":"<SSID1>",
          "wifiPassword":"<PASSWORD>",
          "wifiSecurityType":"eWiFiSecurityWPA2"
       },
       "testWifiConfig":{  
          "wifiSSID":"<SSID2>",
          "wifiPassword":"<PASSWORD>",
          "wifiSecurityType":"eWiFiSecurityWPA2"
       },
       "otaConfiguration":{  
          "otaFirmwareFilePath":"{{testData.sourcePath}}/<relative-path/to/ota-image/from/root/of/afrsourcecode>",
          "deviceFirmwareFileName":"<deviceFirmwareFileName>",
          "awsSignerPlatform":"AmazonFreeRTOS-Default",
          "awsSignerCertificateArn":"arn:aws:acm:<region>:<account-id>:certificate:<certificate-id>",
          "awsUntrustedSignerCertificateArn":"arn:aws:acm:<region>:<account-id>:certificate:<certificate-id>",
          "awsSignerCertificateFileName":"<awsSignerCertificateFileName>",
          "compileCodesignerCertificate":true
       }
    }
```

The following is an example `device.json` file:

```
[
	{
		"id": "<POOL_NAME>",
		"sku": "<armsku>",
		"features": [
		{
			"name": "WIFI",
			"value": "<Yes>"
		},
		{
			"name": "OTA",
			"value": "<Yes>"
		},
		{
			"name": "TCP/IP",
			"value": "Offloaded"
		},
		{
			"name": "TLS",
			"value": "On-chip"
		}
	],
	"devices": [
	{
		"id": "<DEVICE_NAME>",
		"connectivity": {
			"protocol": "uart",
			"serialPort": "/dev/tty<PORT>" OR "/dev/tty.<PORT>"
		},
		"identifiers": [
		{
			"name": "serialNo",
			"value": "<ABCDEZAGHJI>"
		}
		]
	}
	]
}
]
```

On the Windows platform, the userdata and device configuration files are formatted in the same manner\. Pay close attention to the direction of the path\-separator slashes\. We recommend using the forward slash \(/\) because newer versions of Windows support it\. If you are using Windows 7 or earlier, use the back slash \(\\\)\.