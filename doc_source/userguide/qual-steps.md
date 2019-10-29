# Preparing to Test Your Microcontroller Board for the First Time<a name="qual-steps"></a>

You can use IDT for Amazon FreeRTOS to test as you port the Amazon FreeRTOS interfaces\. After you have ported the Amazon FreeRTOS interfaces for your board’s device drivers, you use AWS IoT Device Tester to run the qualification tests on your microcontroller board\. 

## Add Library Porting Layers<a name="add-port-layer"></a>

 To port Amazon FreeRTOS for your device, follow the instructions in the [Amazon FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)\.

## Configure Your AWS Credentials<a name="cfg-aws-afr"></a>

You need to configure your AWS credentials for Device Tester to communicate with the AWS cloud\. For more information, see [ Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html)\. Valid AWS credentials must be specified in the `<devicetester_extract_location> /devicetester_afreertos_[win|mac|linux]/configs/config.json` configuration file\.

## Create a Device Pool in IDT for Amazon FreeRTOS<a name="cfg-dt-dp"></a>

Devices to be tested are organized in device pools\. Each device pool consists of one or more identical devices\. You can configure IDT for Amazon FreeRTOS to test a single device in a pool or multiple devices in a pool\. To accelerate the qualification process, IDT for Amazon FreeRTOS can test devices with the same specifications in parallel\. It uses a round\-robin method to execute a different test group on each device in a device pool\.

You can add one or more devices to a device pool by editing the `devices` section of the `device.json` template in the `configs` folder\.

**Note**  
All devices in the same pool must be of same technical specification and SKU\.

To enable parallel builds of the source code for different test groups, IDT for Amazon FreeRTOS copies the source code to a results folder inside the IDT for Amazon FreeRTOS extracted folder\. The source code path in your build or flash command must be referenced using the `testdata.sourcePath` variable\. IDT for Amazon FreeRTOS replaces this variable with a temporary path of the copied source code\. For more information see, [IDT for Amazon FreeRTOS Variables](#dt-vars)\.

The following is an example `device.json` file used to create a device pool with multiple devices\.

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
      "value": "Yes | No"
    },
    {
      "name": "PKCS11",
      "value": "RSA | ECC | Both | No"
	},
    {
      "name": "KeyProvisioning",
      "value": "Import | Onboard | No"
	},
    {
    	"name": "BLE",
    	"value": "Yes | No"
    }
  ],
  "devices": [{
    "id": "<device-id1>",
    "connectivity": {
      "protocol": "uart",
      "serialPort": "<computer_serial_port_1>"
    },
    "secureElementConfig": {
      "publicKeyAsciiHexFilePath": "<absolute-path-to-public-key>"
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

The following attributes are used in the `device.json` file:

`id`  
A user\-defined alphanumeric ID that uniquely identifies a pool of devices\. Devices that belong to a pool must be of the same type\. When a suite of tests is running, devices in the pool are used to parallelize the workload\.

`sku`  
An alphanumeric value that uniquely identifies the board you are testing\. The SKU is used to track qualified boards\.  
If you want to list your board in AWS Partner Device Catalog, the SKU you specify here must match the SKU that you use in the listing process\.

`features`  
An array that contains the device's supported features\. The Device Tester uses this information to select the qualification tests to run\.  
Supported values are:    
`TCP/IP`  
Indicates if your board supports a TCP/IP stack and whether it is supported on\-chip \(MCU\) or offloaded to another module\. TCP/IP is required for qualification\.  
`WIFI`  
Indicates if your board has Wi\-Fi capabilities\.  
`TLS`  
Indicates if your board supports TLS\. TLS is required for qualification\.  
`PKCS11`  
: Indicates the public key cryptography alogirithm the board supports\. Supported values are `ECC`, `RSA`, `Both` and `No`\. PKCS11 is required for qualification\.  
`KeyProvisioning`  
Indicates the method of writing a trusted X\.509 client certificate onto your board\. Valid values are `Import`, `Onboard` and `No`\. Key provisioning is required for qualification\.  
Use `Import` if your board supports private key import\. Use `Onboard` if your board supports on\-board private key generation\. If your board does not support key provisioning, use `No`\.  
If you use `Onboard`, add a `secureElementConfig` element in each of the `device` sections and put the absolute path to the public key file in the `publicKeyAsciiHexFilePath` field\.  
`OTA`  
Indicates if your board supports over\-the\-air \(OTA\) update functionality\.  
`BLE`  
Indicates if your board supports Bluetooth Low Energy \(BLE\)\.

`devices.id`  
A user\-defined unique identifier for the device being tested\.

`devices.connectivity.protocol`  
The communication protocol used to communicate with this device\. Supported value: `uart`\.

`devices.connectivity.serialPort`  
The serial port of the host computer used to connect to the devices being tested\.

`devices.secureElementConfig.PublicKeyAsciiHexFilePath`  
The absolute path to the file that contains the hex bytes public key extracted from onboard private key\.

`identifiers`  
Optional\. An array of arbitrary name\-value pairs\. You can use these values in the build and flash commands described in the next section\.

## Configure Build, Flash, and Test Settings<a name="cfg-dt-ud"></a>

For IDT for Amazon FreeRTOS to build and flash tests on to your board automatically, you must configure IDT to run the build and flash commands for your hardware\. The build and flash command settings are configured in the `userdata.json` template file located in the `config` folder\.

### Configure Settings for Testing Devices<a name="config-settings-device"></a>

Build, flash, and test settings are made in the `userdata.json` file\. The following JSON example shows how you can configure IDT for Amazon FreeRTOS for testing multiple devices:

```
{  
       "sourcePath":"<absolute-path-to/amazon-freertos>",
       "vendorPath":"{{testData.sourcePath}}/vendors/<vendor-name>/boards/<board-name>",
       	
       "buildTool":{  
          "name":"<your-build-tool-name>",
          "version":"<your-build-tool-version>",
          "command":[  
             "<absolute-path-to/build-parallel-script> {{testData.sourcePath}} {{enableTests}}"
          ],
    	  "buildImageInfo" : {
               "testsImageName": "<tests-image-file-name>",
               "demosImageName": "<demos-image-file-name>"
           }

       },
       "flashTool":{  
          "name":"<your-flash-tool-name>",
          "version":"<your-flash-tool-version>",
          "command":[  
             "<absolute-path-to/flash-parallel-script> {{testData.sourcePath}} {{device.connectivity.serialPort}} {{buildImageName}}"
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
       "echoServerConfiguration":{  
          "securePortForSecureSocket":33333,
          "insecurePortForSecureSocket":33334,
          "insecurePortForWifi":33335 

       },
       "otaConfiguration":{  
          "otaFirmwareFilePath":"{{testData.sourcePath}}/<relative-path-to/ota-image-generated-in-build-process>",
          "deviceFirmwareFileName":"<absolute-path-to/ota-image-on-device>",
          "awsSignerPlatform":"AmazonFreeRTOS-Default | AmazonFreeRTOS-TI-CC3220SF",
          "awsSignerCertificateArn":"arn:aws:acm:<region>:<account-id>:certificate:<certificate-id>",
          "awsUntrustedSignerCertificateArn":"arn:aws:acm:<region>:<account-id>:certificate:<certificate-id>",
          "awsSignerCertificateFileName":"<awsSignerCertificate-file-name>",
          "compileCodesignerCertificate":true | false,
          "otaDemoConfigFilePath": "<full path to aws_demo_config.h>"
       },
	   "cmakeConfiguration": {
		    "boardName": "<board-name>",
		    "vendorName": "<vendor-name>",
		    "compilerName": "<compiler-name>",
		    "afrToolchainPath": "</path/to/afr/toolchain>",
		    "cmakeToolchainPath": "</path/to/cmake/toolchain>"
		}
	},
	"flashTool":{  
		"name":"<your-flash-tool-name>",
		"version":"<your-flash-tool-version>",
		"command":[  
			"<absolute-path-to/flash-parallel-script> {{testData.sourcePath}} {{device.connectivity.serialPort}} {{buildImageName}}"
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
		"compileCodesignerCertificate":true | false,
		"otaDemoConfigFilePath": <full-path-to-aws_demo_config.h>
	},
	"cmakeConfiguration": {
		"boardName": "<board-name>",
		"vendorName": "<vendor-name>",
		"compilerName": "<compiler-name>",
		"afrToolchainPath": "</path/to/afr/toolchain>",
		"cmakeToolchainPath": "</path/to/cmake/toolchain>"
	}
}
```

The following lists the attributes used in `userdata.json`:

`sourcePath`  
The path to the root of the ported Amazon FreeRTOS source code\.

`vendorPath`  
The path to the vendor specific Amazon FreeRTOS code\. For serial testing, the `vendorPath` can be set as an absolute path\. For example:  

```
{
	"vendorPath":"C:/<path-to-freertos>/vendors/espressif/boards/<esp32>"
}
```
For parallel testing, the `vendorPath` can be set using the `{{testData.sourcePath}}` place holder\. For example:  

```
{
	"vendorPath":"{{testData.sourcePath}}/vendors/espressif/boards/esp32"
}
```
When running tests in parallel, the `{{testData.sourcePath}}` placeholder must be used in the `vendorPath`, `buildTool`, `flashTool` fields\. When running test with a single device, absolute paths must be used in the `vendorPath`, `buildTool`, `flashTool` fields\.

`buildTool`  
The full path to your build script \(\.bat or \.sh\) that contains the commands to build your source code\. All references to the source code path in the build command must be replaced by the AWS IoT Device Tester variable `{{testdata.sourcePath}}`\.  
+ `buildImageInfo`
  + `testsImageName`: The name of the file produced by the build command when building tests from the `<afr-source>`/tests folder\.
  + `demosImageName`: The name of the file produced by the build command when building tests from the `<afr-source>`/demos folder\.

`buildTool.buildImageInfo.testsImageName`  
The name of the file output by the build command when building tests from the *<afr\-source\-code>*/tests folder\.

`buildTool.buildImageInfo.demosImageName`  
The name of the file output by the build command when building demos from the *<afr\-source\-code>*/demos folder\.

`flashTool`  
Full path to your ﬂash script \(\.sh or \.bat\) that contains the ﬂash commands for your device\. All references to the source code path in the ﬂash command must be replaced by the IDT for Amazon FreeRTOS variable `{{testdata.sourcePath}}`\.

`clientWifiConfig`  
Client Wi\-Fi configuration\. The Wi\-Fi library tests require an MCU board to connect to two access points\. This attribute configures the Wi\-Fi settings for the first access point\. The client Wi\-Fi settings are configured in `<amazon-freertos>/test/include/aws_clientcredential.h.`\. The following macros are set using the values found in `aws_clientcredential.h`\. Some of the Wi\-Fi test cases expect the access point to have some security and not to be open\.  
+ wifi\_ssid: The Wi\-Fi SSID\.
+ wifi\_password: The Wi\-Fi password\.
+ wifiSecurityType: The type of Wi\-Fi security used\.

`testWifiConfig`  
Test Wi\-Fi configuration\. The Wi\-Fi library tests require a board to connect to two access points\. This attribute configures the second access point\. The test Wi\-Fi settings are configured in `<amazon-freertos>/.../wifi/test/aws_test_wifi.h`\.  The following macros are set using the values found in `aws_test_wifi.c`\. Some of the Wi\-Fi test cases expect the access point to have some security and not to be open\.  
If your board does not support Wi\-Fi, you must still include the `clientWifiConfig` and `testWifiConfig` section in your `device.json` file, but you can omit values for these attributes\.
+ testwifiWIFI\_SSID: The Wi\-Fi SSID\.
+ testwifiWIFI\_PASSWORD: The Wi\-Fi password\.
+ testwifiWIFI\_SECURITY: The type of Wi\-Fi security used\. One of the following values:
  + `eWiFiSecurityOpen`
  + `eWiFiSecurityWEP`
  + `eWiFiSecurityWPA`
  + `eWiFiSecurityWPA2`

`echoServerConfiguration`  
The configurable echo server ports for WiFi and secure sockets tests\. This field is optional

`securePortForSecureSocket`  
The port which is used to setup an echo server with TLS for the secure sockets test\. The default value is 33333\. Ensure the port configured is not blocked by a firewall or your corporate network\.

`insecurePortForSecureSocket`  
The port which is used to setup echo server without TLS for the secure sockets test\. The default value used in the test is 33335\. Ensure the port configured is not blocked by a firewall or your corporate network\.

`insecurePortForWiFi`  
The port which is used to setup echo server without TLS for WiFi test\. The default value used in the test is 33335\. Ensure the port configured is not blocked by a firewall or your corporate network\.

`otaConfiguration`  
The OTA configuration\. This field is optional    
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
`otaDemoConfigFilePath`  
The full path to `aws_demo_config.h`, found within `<afr-source>/vendors/vendor/boards/board/ aws_demos/config_files/`\. These files are included in the porting code template provided by Amazon FreeRTOS\.

`cmakeConfiguration`  
CMake configuration \- remove this section if you are not using CMake    
`boardName`  
The name of the board under test\. The board name should be the same as the folder name under *path/to/afr/source/code*/vendors/*<vendor>*/boards/*<board>*\.  
`vendorName`  
The vendor name for the board under test\. The vendor should be the same as the folder name under *path/to/afr/source/code*/vendors/*<vendor>*\.  
`compilerName`  
The compiler name\.  
`afrToolchainPath`  
The fully\-qualified path to the compiler toolchain  
`cmakeToolchainPath`   
The fully\-qualified path to the CMake toolchain\. This field is optional

**Note**  
To execute CMake test cases, you must provide the board name, vendor name, and either the `afrToolchainPath` or `compilerName`\. You may also provide `cmakeToolchainPath` if you have a custom path to the CMake toolchain\.

### IDT for Amazon FreeRTOS Variables<a name="dt-vars"></a>

The commands to build your code and flash the device might require connectivity or other information about your devices to run successfully\. AWS IoT Device Tester allows you to reference device information in flash and build commands using [JsonPath](http://goessner.net/articles/JsonPath/)\. By using simple JsonPath expressions, you can fetch the required information specified in your `device.json` file\.

#### Path Variables<a name="path-variables"></a>

IDT for Amazon FreeRTOS defines the following path variables that can be used in command lines and configuration files:

`{{testData.sourcePath}}`  
Expands to the source code path\. If you use this variable, it must be used in both the flash and build commands\.

`{{device.connectivity.serialPort}}`  
Expands to the serial port\.

`{{device.identifiers[?(@.name == 'serialNo')].value}}`  
Expands to the serial number of your device\.

`{{enableTests}}`  
Integer value indicating whether the build is for tests \(value 1\) or demos \(value 0\)\.

`{{buildImageName}}`  
The file name of the image built by the build command\.