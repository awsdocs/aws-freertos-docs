# Test to Qualify Your Microcontroller Board for the First Time<a name="qual-steps"></a>

You can use AWS IoT Device Tester to test as you port the Amazon FreeRTOS interfaces\. After you have ported the Amazon FreeRTOS interfaces for your board’s device drivers, you use AWS IoT Device Tester to run the qualification tests on your microcontroller board\. 

## Add Library Porting Layers<a name="add-port-layer"></a>

To add library porting layers for Amazon FreeRTOS device libraries \(TCP/IP, WiFi, and so on\) compatible with your MCU architecture, you must:

1. Implement the `configPRINT_STRING()` method before running AWS IoT Device Tester tests\. AWS IoT Device Tester calls the `configPRINT_STRING()` macro to output test results as human\-readable ASCII strings\. 

1. Port the drivers to implement the Amazon FreeRTOS library's interfaces\. For more information, see the [Amazon FreeRTOS Qualification Developer Guide](https://github.com/aws/amazon-freertos/blob/master/tests/Amazon%20FreeRTOS%20Qualification%20Developer%20Guide.pdf)\.

## Configure Your AWS Credentials<a name="cfg-aws-afr"></a>

You must configure your AWS credentials in the `<devicetester_extract_location>/devicetester_afreertos_[win|mac|linux]/configs/config.json`\. You can specify your credentials in one of two ways:
+ Environment variables
+ Credentials file

### Configuring AWS Credentials with Environment Variables<a name="config-env-vars-afr"></a>

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

### Configuring AWS Credentials with a Credentials File<a name="config-cred-file"></a>

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

### Configuring the AWS Region<a name="cfg-aws-rgn"></a>

AWS IoT Device Tester creates cloud resources in your AWS account\. You can specify the AWS Region to use for testing by setting the `awsRegion` parameter in the config\.json file\. By default, AWS IoT Device Tester uses the us\-west\-2 region\.

## Create a Device Pool in AWS IoT Device Tester<a name="cfg-dt-dp"></a>

Devices to be tested are organized in device pools\. Each device pool consists of one or more devices with identical specifications\. You can configure Device Tester to test a single device in a pool or multiple devices in a pool\. To accelerate the qualification process, AWS IoT Device Tester can test devices with the same specification in parallel\. It uses a round\-robin method to execute a different test group on each device in a device pool\. 

### Configuring AWS IoT Device Tester for Single Device Testing<a name="config-single-dp"></a>

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

### Configuring AWS IoT Device Tester for Multiple Device Testing<a name="config-mult-dp"></a>

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

## Configure Build, Flash, and Test Settings<a name="cfg-dt-ud"></a>

For AWS IoT Device Tester to build and flash test cases on to your board automatically, you must configure AWS IoT Device Tester with command line interfaces of your build and flash tools\. The build and flash settings are configured in the `userdata.json` template file located in the config folder\.

### Configure Settings for Testing One Device<a name="config-userdata-single"></a>

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
The Amazon Resource Name \(ARN\) for a code\-signing certificate uploaded to ACM which your device should not trust\. This is used to test invalid certificate test cases\.  
`compileCodesignerCertificate`  
Set to `true` if the code\-signer signature verification certificate is not provisioned or flashed, so it must be compiled into the project\. AWS IoT Device Tester fetches the trusted certificate from ACM and compiles it into `aws_codesigner_certifiate.h`\.

### Configure Settings for Testing Multiple Devices<a name="config-userdata-mult"></a>

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
The full path to the OTA image created after the build\. For example, `{{testData.sourcePath}}/<relative-path/to/ota/image/from/source/root>`\.  
`deviceFirmwareFileName`  
The full ﬁle path on the MCU device where the OTA ﬁrmware is located\. Some devices do not use this ﬁeld, but you still must provide a value\.  
`awsSignerPlatform`  
The signing algorithm used by AWS Code Signer while creating the OTA update job\. Currently, the possible values for this field are `AmazonFreeRTOS-TI-CC3220SF` and `AmazonFreeRTOS-Default`\.  
`awsSignerCertificateArn`  
The Amazon Resource Name \(ARN\) for the trusted certificate uploaded to AWS Certificate Manager \(ACM\)\. For more information about creating a trusted certificate, see [Creating a Code Signing Certificate](https://docs.aws.amazon.com/freertos/latest/userguide/ota-code-sign-cert.html)\.  
`awsUntrustedSignerCertificateArn`  
The ARN for the code\-signing certificate uploaded to ACM\.  
`compileCodesignerCertificate`  
Set to `true` if the code\-signer signature verification certificate is not provisioned or flashed, so it must be compiled into the project\. AWS IoT Device Tester fetches the trusted certificate from ACM and compiles it into `aws_codesigner_certifiate.h`\.

### AWS IoT Device Tester Variables<a name="dt-vars"></a>

The commands to build your code and flash the device might require connectivity or other information about your devices to run successfully\. AWS IoT Device Tester allows you to reference device information in flash and build commands using [JsonPath](http://goessner.net/articles/JsonPath/)\. By using simple JsonPath expressions, you can fetch the required information as specified in your `device.json` file\.

#### Path Variables<a name="path-variables"></a>

AWS IoT Device Tester defines the following path variables that can be used in command lines and configuration files:

`{{testData.sourcePath}}`  
A variable that expands to the source code path\.

`{{device.connectivity.serialPort}}`  
A variable that expands to the serial port\.

`{{device.identifiers[?(@.name == 'serialNo')].value}}`  
A variable that expands to the serial number of your device\.

#### AWS IoT Device Tester Variables and Concurrent Testing<a name="dt-var-concurrent-testing"></a>

To enable parallel builds of the source code for different test groups, AWS IoT Device Tester copies the source code to a results folder inside the AWS IoT Device Tester extracted folder\. The source code path in your build or flash command must be referenced using the `testdata.sourcePath` variable\. AWS IoT Device Tester replaces this variable with a temporary path of the copied source code\.

##### File Paths and the Windows Operating System<a name="win-filepaths"></a>

If you are running AWS IoT Device Tester on Windows, use forward slashes \(/\) in your file paths in AWS IoT Device Tester config files\. For example, `sourcePath` in `userdata.json` should be represented as `c:/<dir1>/<dir2>`\.