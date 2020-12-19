# Preparing to test your microcontroller board for the first time<a name="qual-steps"></a>

You can use IDT for FreeRTOS to test as you port the FreeRTOS interfaces\. After you have ported the FreeRTOS interfaces for your board’s device drivers, you use AWS IoT Device Tester to run the qualification tests on your microcontroller board\. 

## Add library porting layers<a name="add-port-layer"></a>

 To port FreeRTOS for your device, follow the instructions in the [FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)\.

## Configure your AWS credentials<a name="cfg-aws-afr"></a>

You need to configure your AWS credentials for Device Tester to communicate with the AWS Cloud\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html)\. Valid AWS credentials must be specified in the `devicetester_extract_location/devicetester_afreertos_[win|mac|linux]/configs/config.json` configuration file\.

## Create a device pool in IDT for FreeRTOS<a name="cfg-dt-dp"></a>

Devices to be tested are organized in device pools\. Each device pool consists of one or more identical devices\. You can configure IDT for FreeRTOS to test a single device in a pool or multiple devices in a pool\. To accelerate the qualification process, IDT for FreeRTOS can test devices with the same specifications in parallel\. It uses a round\-robin method to execute a different test group on each device in a device pool\.

You can add one or more devices to a device pool by editing the `devices` section of the `device.json` template in the `configs` folder\.

**Note**  
All devices in the same pool must be of same technical specification and SKU\.

To enable parallel builds of the source code for different test groups, IDT for FreeRTOS copies the source code to a results folder inside the IDT for FreeRTOS extracted folder\. The source code path in your build or flash command must be referenced using either the `testdata.sourcePath` or `sdkPath` variable\. IDT for FreeRTOS replaces this variable with a temporary path of the copied source code\. For more information see, [IDT for FreeRTOS variables](#dt-vars)\.

The following is an example `device.json` file used to create a device pool with multiple devices\.

```
[
    {
        "id": "pool-id",
        "sku": "sku",
        "features": [
            {
                "name": "WIFI",
                "value": "Yes | No"
            },
            {
                "name": "Cellular",
                "value": "Yes | No"
            },
            {
                "name": "OTA",
                "value": "Yes | No",
                "configs": [
                    {
                        "name": "OTADataPlaneProtocol",
                        "value": "HTTP | MQTT | Both"
                    }
                ]
            },
            {
                "name": "BLE",
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
            }
        ],

        "devices": [
          {
            "id": "device-id",
            "connectivity": {
              "protocol": "uart",
              "serialPort": "/dev/tty*"
            },
            ***********Remove the section below if the device does not support onboard key generation***************
            "secureElementConfig" : {
              "publicKeyAsciiHexFilePath": "absolute-path-to/public-key-txt-file: contains-the-hex-bytes-public-key-extracted-from-onboard-private-key",
              "secureElementSerialNumber": "secure-element-serialNo-value"
            },
            **********************************************************************************************************
            "identifiers": [
              {
                "name": "serialNo",
                "value": "serialNo-value"
              }
            ]
          }
        ]
    }
]
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
Indicates if your board has Wi\-Fi capabilities\. Must be set to `No` if `Cellular` is set to `Yes`\.  
`Cellular`  
Indicates if your board has cellular capabilities\. Must be set to `No` if `WIFI` is set to `Yes`\. When this feature is set to `Yes`, the FullSecureSockets test will be executed using AWS t2\.micro EC2 instances and this may incur additional costs to your account\. For more information, see [Amazon EC2 pricing](https://aws.amazon.com/ec2/pricing/)\.  
`TLS`  
Indicates if your board supports TLS\. TLS is required for qualification\.  
`PKCS11`  
Indicates the public key cryptography algorithm that the board supports\. PKCS11 is required for qualification\. Supported values are `ECC`, `RSA`, `Both` and `No`\. `Both` indicates the board supports both the `ECC` and `RSA` algorithms\.  
`KeyProvisioning`  
Indicates the method of writing a trusted X\.509 client certificate onto your board\. Valid values are `Import`, `Onboard` and `No`\. Key provisioning is required for qualification\.  
+ Use `Import` if your board allows the import of private keys\. IDT will create a private key and build this to the FreeRTOS source code\.
+ Use `Onboard` if your board supports on\-board private key generation \(for example, if your device has a secure element, or if you prefer to generate your own device key pair and certificate\)\. Make sure you add a `secureElementConfig` element in each of the device sections and put the absolute path to the public key file in the `publicKeyAsciiHexFilePath` field\.
+ Use `No` if your board does not support key provisioning\.   
`OTA`  
Indicates if your board supports over\-the\-air \(OTA\) update functionality\. The `OtaDataPlaneProtocol` attribute indicates which OTA dataplane protocol the device supports\. The attribute is ignored if the OTA feature is not supported by the device\. When `"Both"` is selected, the OTA test execution time is prolonged due to running both MQTT, HTTP, and mixed tests\.  
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
Example format:   

```
3059 3013 0607 2a86 48ce 3d02 0106 082a
8648 ce3d 0301 0703 4200 04cd 6569 ceb8
1bb9 1e72 339f e8cf 60ef 0f9f b473 33ac
6f19 1813 6999 3fa0 c293 5fae 08f1 1ad0
41b7 345c e746 1046 228e 5a5f d787 d571
dcb2 4e8d 75b3 2586 e2cc 0c
```
If your public key is in \.der format, you can hex encode the public key directly to generate the hex file\.  
Example command for \.der public key to generate hex file:  

```
xxd -p pubkey.der > outFile
```
If your public key is in \.pem format, you can extract the base64 encoded part, decode it into binary format, and then hex encode it to generate the hex file\.  
For example, use these commands to generate a hex file for a \.pem public key:  

1. Take out the base64 encoded part of the key \(strip the header and footer \) and store it in a file, for example name it `base64key`, run this command to convert it to \.der format:

   ```
   base64 —decode base64key > pubkey.der
   ```

1. Run the `xxd` command to convert it to hex format\.

   ```
   xxd -p pubkey.der > outFile
   ```

`devices.secureElementConfig.SecureElementSerialNumber`  
\(Optional\) The serial number of the secure element\. Provide this field when the serial number is printed out along with the device public key when you run the FreeRTOS demo/test project\.

`identifiers`  
\(Optional\) An array of arbitrary name\-value pairs\. You can use these values in the build and flash commands described in the next section\.

## Configure build, flash, and test settings<a name="cfg-dt-ud"></a>

For IDT for FreeRTOS to build and flash tests on to your board automatically, you must configure IDT to run the build and flash commands for your hardware\. The build and flash command settings are configured in the `userdata.json` template file located in the `config` folder\.

### Configure settings for testing devices<a name="config-settings-device"></a>

Build, flash, and test settings are made in the `configs/userdata.json` file\. We support Echo Server configuration by loading both the client and server certificates and keys in the `customPath`\. For more information, see [Setting up an echo server](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-echo-server.html) in the *FreeRTOS Porting Guide*\. The following JSON example shows how you can configure IDT for FreeRTOS to test multiple devices:

```
{
    "sourcePath": "/absolute-path-to/freertos",
    "vendorPath": "{{testData.sourcePath}}/vendors/vendor-name/boards/board-name",
    // ***********The sdkConfiguration block below is needed if you are not using the default, unmodified FreeRTOS repo. 
    // In other words, if you are using the default, unmodified FreeRTOS repo then remove this block***************
    "sdkConfiguration": {
        "name": "sdk-name",
        "version": "sdk-version",
        "path": "/absolute-path-to/sdk"
    },
    "buildTool": {
        "name": "your-build-tool-name",
        "version": "your-build-tool-version",
        "command": [
            "/absolute-path-to/build-parallel.sh {{testData.sourcePath}} {{enableTests}}"
        ]
    },
    "flashTool": {
        "name": "your-flash-tool-name",
        "version": "your-flash-tool-version",
        "command": [
            "/absolute-path-to/flash-parallel.sh {{testData.sourcePath}} {{device.connectivity.serialPort}} {{buildImageName}}"
        ],
        "buildImageInfo" : {
            "testsImageName": "tests-image-name",
            "demosImageName": "demos-image-name"
        }
    },
    "clientWifiConfig": {
        "wifiSSID": "ssid",
        "wifiPassword": "password",
        "wifiSecurityType": "eWiFiSecurityOpen | eWiFiSecurityWEP | eWiFiSecurityWPA | eWiFiSecurityWPA2 | eWiFiSecurityWPA3"
    },
    "testWifiConfig": {
        "wifiSSID": "ssid",
        "wifiPassword": "password",
        "wifiSecurityType": "eWiFiSecurityOpen | eWiFiSecurityWEP | eWiFiSecurityWPA | eWiFiSecurityWPA2 | eWiFiSecurityWPA3"
    },
    //**********
    //This section is used to start echo server based on server certificate generation method,
    //When certificateGenerationMethod is set as Automatic specify the eccCurveFormat to generate certifcate and key based on curve format,
    //When certificateGenerationMethod is set as Custom specify the certificatePath and PrivateKeyPath to be used to start echo server
    //**********
    "echoServerCertificateConfiguration": {
      "certificateGenerationMethod": "Automatic | Custom",
      "customPath": {
          "clientCertificatePath":"/path/to/clientCertificate",
          "clientPrivateKeyPath": "/path/to/clientPrivateKey",
          "serverCertificatePath":"/path/to/serverCertificate",
          "serverPrivateKeyPath": "/path/to/serverPrivateKey"
      },
    "eccCurveFormat": "P224 | P256 | P384 | P521"
    },
    "echoServerConfiguration": {
        "securePortForSecureSocket": 33333, // Secure tcp port used by SecureSocket test. Default value is 33333. Ensure that the port configured isn't blocked by the firewall or your corporate network
        "insecurePortForSecureSocket": 33334, // Insecure tcp port used by SecureSocket test. Default value is 33334. Ensure that the port configured isn't blocked by the firewall or your corporate network
        "insecurePortForWiFi": 33335 // Insecure tcp port used by Wi-Fi test. Default value is 33335. Ensure that the port configured isn't blocked by the firewall or your corporate network
    },
    "otaConfiguration": {
        "otaFirmwareFilePath": "{{testData.sourcePath}}/relative-path-to/ota-image-generated-in-build-process",
        "deviceFirmwareFileName": "ota-image-name-on-device",
        "otaDemoConfigFilePath": "{{testData.sourcePath}}/relative-path-to/ota-demo-config-header-file",
        "codeSigningConfiguration": {
            "signingMethod": "AWS | Custom",
            "signerHashingAlgorithm": "SHA1 | SHA256",
            "signerSigningAlgorithm": "RSA | ECDSA",
            "signerCertificate": "arn:partition:service:region:account-id:resource:qualifier | /absolute-path-to/signer-certificate-file",
            "signerCertificateFileName": "signerCertificate-file-name",
            "compileSignerCertificate": boolean,
            // ***********Use signerPlatform if you choose aws for signingMethod***************
            "signerPlatform": "AmazonFreeRTOS-Default | AmazonFreeRTOS-TI-CC3220SF",
            "untrustedSignerCertificate": "arn:partition:service:region:account-id:resourcetype:resource:qualifier",
            // ***********Use signCommand if you choose custom for signingMethod***************
            "signCommand": [
                "/absolute-path-to/sign.sh {{inputImageFilePath}} {{outputSignatureFilePath}}"
            ]
        }
    },
    // ***********Remove the section below if you're not configuring CMake***************
    "cmakeConfiguration": {
        "boardName": "board-name",
        "vendorName": "vendor-name",
        "compilerName": "compiler-name",
        "frToolchainPath": "/path/to/freertos/toolchain",
        "cmakeToolchainPath": "/path/to/cmake/toolchain"
    },
    "freertosFileConfiguration": {
        "required": [
            {
                "configName": "pkcs11Config",
                "filePath": "{{testData.sourcePath}}/vendors/vendor-name/boards/board-path/aws_tests/config_files/core_pkcs11_config.h"
            },
            {
                "configName": "pkcs11TestConfig",
                "filePath": "{{testData.sourcePath}}/vendors/vendor-name/boards/board-path/aws_tests/config_files/iot_test_pkcs11_config.h"
            }
        ],
        "optional": [
            {
                "configName": "otaAgentTestsConfig",
                "filePath": "{{testData.sourcePath}}/vendors/vendor-name/boards/board-path/aws_tests/config_files/aws_ota_agent_config.h"
            },
            {
                "configName": "otaAgentDemosConfig",
                "filePath": "{{testData.sourcePath}}/vendors/vendor-name/boards/board-path/aws_demos/config_files/aws_ota_agent_config.h"
            }
        ]
    }
}
```

The following lists the attributes used in `userdata.json`:

`sourcePath`  
The path to the root of the ported FreeRTOS source code\. For parallel testing with an SDK, the `sourcePath` can be set using the `{{userData.sdkConfiguration.path}}` place holder\. For example:   

```
{ "sourcePath":"{{userData.sdkConfiguration.path}}/freertos" }
```

`vendorPath`  
The path to the vendor specific FreeRTOS code\. For serial testing, the `vendorPath` can be set as an absolute path\. For example:  

```
{ "vendorPath":"C:/path-to-freertos/vendors/espressif/boards/esp32" }
```
For parallel testing, the `vendorPath` can be set using the `{{testData.sourcePath}}` place holder\. For example:  

```
{ "vendorPath":"{{testData.sourcePath}}/vendors/espressif/boards/esp32" }
```
The `vendorPath` variable is only necessary when running without an SDK, it can be removed otherwise\.  
When running tests in parallel without an SDK, the `{{testData.sourcePath}}` placeholder must be used in the `vendorPath`, `buildTool`, `flashTool` fields\. When running test with a single device, absolute paths must be used in the `vendorPath`, `buildTool`, `flashTool` fields\. When running with an SDK, the `{{sdkPath}}` placeholder must be used in the `sourcePath`, `buildTool`, and `flashTool` commands\.

`sdkConfiguration`  
If you are qualifying FreeRTOS with any modifications to files and folder structure beyond what is required for porting, then you will need to configure your SDK information in this block\. If you're not qualifying with a ported FreeRTOS inside of an SDK, then you should omit this block entirely\.    
`sdkConfiguration.name`  
The name of the SDK you're using with FreeRTOS\. If you're not using an SDK, then the entire `sdkConfiguration` block should be omitted\.  
`sdkConfiguration.version`  
The version of the SDK you're using with FreeRTOS\. If you're not using an SDK, then the entire `sdkConfiguration` block should be omitted\.  
`sdkConfiguration.path`  
The absolute path to your SDK directory that contains your FreeRTOS code\. If you're not using an SDK, then the entire `sdkConfiguration` block should be omitted\.

`buildTool`  
The full path to your build script \(\.bat or \.sh\) that contains the commands to build your source code\. All references to the source code path in the build command must be replaced by the AWS IoT Device Tester variable `{{testdata.sourcePath}}` and references to the SDK path should be replaced by `{{sdkPath}}`\.    
`buildImageInfo`    
`testsImageName`  
The name of the file produced by the build command when building tests from the `freertos-source/tests` folder\.  
`demosImageName`  
The name of the file produced by the build command when building tests from the `freertos-source/demos` folder\.

`flashTool`  
Full path to your ﬂash script \(\.sh or \.bat\) that contains the ﬂash commands for your device\. All references to the source code path in the ﬂash command must be replaced by the IDT for FreeRTOS variable `{{testdata.sourcePath}}` and all references to your SDK path must be replaced by the IDT for FreeRTOS variable `{{sdkPath}}`\.

`clientWifiConfig`  
The client Wi\-Fi configuration\. The Wi\-Fi library tests require an MCU board to connect to two access points\. \(The two access points can be the same\.\) This attribute configures the Wi\-Fi settings for the first access point\. Some of the Wi\-Fi test cases expect the access point to have some security and not to be open\. Please make sure both access points are on the same subnet as the host computer running IDT\.    
`wifi_ssid`  
The Wi\-Fi SSID\.  
`wifi_password`  
The Wi\-Fi password\.  
`wifiSecurityType`  
The type of Wi\-Fi security used\. One of the values:  
+ `eWiFiSecurityOpen`
+ `eWiFiSecurityWEP`
+ `eWiFiSecurityWPA`
+ `eWiFiSecurityWPA2`
+ `eWiFiSecurityWPA3`
If your board does not support Wi\-Fi, you must still include the `clientWifiConfig` section in your `device.json` file, but you can omit values for these attributes\.

`testWifiConfig`  
The test Wi\-Fi configuration\. The Wi\-Fi library tests require an MCU board to connect to two access points\. \(The two access points can be the same\.\) This attribute configures the Wi\-Fi setting for the second access point\. Some of the Wi\-Fi test cases expect the access point to have some security and not to be open\. Please make sure both access points are on the same subnet as the host computer running IDT\.    
`wifiSSID`  
The Wi\-Fi SSID\.  
`wifiPassword`  
The Wi\-Fi password\.  
`wifiSecurityType`  
The type of Wi\-Fi security used\. One of the values:  
+ `eWiFiSecurityOpen`
+ `eWiFiSecurityWEP`
+ `eWiFiSecurityWPA`
+ `eWiFiSecurityWPA2`
+ `eWiFiSecurityWPA3`
If your board does not support Wi\-Fi, you must still include the `testWifiConfig` section in your `device.json` file, but you can omit values for these attributes\.

`echoServerCertificateConfiguration`  
The configurable echo server certificate generation placeholder for secure socket tests\. This field is required\.    
`certificateGenerationMethod`  
Specifies whether the server certificate is generated automatically or provided manually\.  
`customPath`  
If `certificateGenerationMethod` is "Custom", `certificatePath` and `privateKeyPath` are required\.    
`certificatePath`  
Specifies the filepath for the server certificate\.  
`privateKeyPath`  
Specifies the filepath for the private key\.  
`eccCurveFormat`  
Specifies the curve format supported by the board\. Required when `PKCS11` is set to "ecc" in `device.json`\. Valid values are "P224", "P256", "P384", or "P521"\.

`echoServerConfiguration`  
The configurable echo server ports for WiFi and secure sockets tests\. This field is optional\.    
`securePortForSecureSocket`  
The port which is used to setup an echo server with TLS for the secure sockets test\. The default value is 33333\. Ensure the port configured is not blocked by a firewall or your corporate network\.  
`insecurePortForSecureSocket`  
The port which is used to setup echo server without TLS for the secure sockets test\. The default value used in the test is 33334\. Ensure the port configured is not blocked by a firewall or your corporate network\.  
`insecurePortForWiFi`  
The port which is used to setup echo server without TLS for WiFi test\. The default value used in the test is 33335\. Ensure the port configured is not blocked by a firewall or your corporate network\.

`otaConfiguration`  
The OTA configuration\. \[Optional\]    
`otaFirmwareFilePath`  
The full path to the OTA image created after the build\. For example, `{{testData.sourcePath}}/relative-path/to/ota/image/from/source/root`\.  
`deviceFirmwareFileName`  
The full ﬁle path on the MCU device where the OTA ﬁrmware is located\. Some devices do not use this ﬁeld, but you still must provide a value\.  
`otaDemoConfigFilePath`  
The full path to `aws_demo_config.h`, found in `afr-source/vendors/vendor/boards/board/aws_demos/config_files/`\. These files are included in the porting code template that FreeRTOS provides\.   
`codeSigningConfiguration`  
The code signing configuration\.  
`signingMethod`  
The code signing method\. Possible values are `AWS` or `Custom`\.  
For the Beijing and Ningxia Regions, use `Custom`\. `AWS` code signing isn't supported in these Regions\.  
`signerHashingAlgorithm`  
The hashing algorithm supported on the device\. Possible values are `SHA1` or `SHA256`\.   
`signerSigningAlgorithm`  
The signing algorithm supported on the device\. Possible values are `RSA` or `ECDSA`\.  
`signerCertificate`  
The trusted certificate used for OTA\.  
For AWS code signing method, use the Amazon Resource Name \(ARN\) for the trusted certificate uploaded to the AWS Certificate Manager\.  
For Custom code signing method, use the absolute path to the signer certificate file\.  
For more information about creating a trusted certificate, see [Create a code\-signing certificate](ota-code-sign-cert.md)\.   
`signerCertificateFileName`  
The location of the code signing certificate on the device\.  
`compileSignerCertificate`  
Set to `true` if the code signer signature verification certificate isn't provisioned or flashed, so it must be compiled into the project\. AWS IoT Device Tester fetches the trusted certificate and compiles it into `aws_codesigner_certifiate.h`\.  
`untrustedSignerCertificateArn`  
The ARN for the code\-signing certificate uploaded to ACM\.  
`signerPlatform`  
The signing and hashing algorithm that AWS Code Signer uses while creating the OTA update job\. Currently, the possible values for this field are `AmazonFreeRTOS-TI-CC3220SF` and `AmazonFreeRTOS-Default`\.   
+ Choose `AmazonFreeRTOS-TI-CC3220SF` if `SHA1` and `RSA`\. 
+ Choose `AmazonFreeRTOS-Default` if `SHA256` and `ECDSA`\.
If you need `SHA256` \| `RSA` or `SHA1` \| `ECDSA` for your configuration, contact us for further support\.  
Configure `signCommand` if you chose `Custom` for `signingMethod`\.  
`signCommand`  
The command used to perform custom code signing\. You can find the template in the `/configs/script_templates` directory\.   
Two placeholders `{{inputImageFilePath}}` and `{{outputSignatureFilePath}}` are required in the command\. `{{inputImageFilePath}}` is the file path of the image built by IDT to be signed\. `{{outputSignatureFilePath}}` is the file path of the signature which will be generated by the script\.  
`otaDemoConfigFilePath`  
The full path to `aws_demo_config.h`, found within `afr-source/vendors/vendor/boards/board/aws_demos/config_files/`\. These files are included in the porting code template provided by FreeRTOS\.

`cmakeConfiguration`  
CMake configuration \[Optional\]  
To execute CMake test cases, you must provide the board name, vendor name, and either the `frToolchainPath` or `compilerName`\. You may also provide the `cmakeToolchainPath` if you have a custom path to the CMake toolchain\.  
`boardName`  
The name of the board under test\. The board name should be the same as the folder name under `path/to/afr/source/code/vendors/vendor/boards/board`\.  
`vendorName`  
The vendor name for the board under test\. The vendor should be the same as the folder name under `path/to/afr/source/code/vendors/vendor`\.  
`compilerName`  
The compiler name\.  
`frToolchainPath`  
The fully\-qualified path to the compiler toolchain  
`cmakeToolchainPath`   
The fully\-qualified path to the CMake toolchain\. This field is optional

`freertosFileConfiguration`  
The configuration of the FreeRTOS files that IDT modifies before running tests\.    
`required`  
This section specifies required tests whose config files you have moved, for example, PKCS11, TLS, and so on\.    
`configName`  
The name of the test that is being configured\.  
`filePath`  
The absolute path to the configuration files within the `freertos` repo\. Use the `{{testData.sourcePath}}` variable to define the path\.  
`optional`  
This section specifies optional tests whose config files you have moved, for example OTA, WiFi, and so on\.    
`configName`  
The name of the test that is being configured\.  
`filePath`  
The absolute path to the configuration files within the `freertos` repo\. Use the `{{testData.sourcePath}}` variable to define the path\.

**Note**  
To execute CMake test cases, you must provide the board name, vendor name, and either the `afrToolchainPath` or `compilerName`\. You may also provide `cmakeToolchainPath` if you have a custom path to the CMake toolchain\.

### IDT for FreeRTOS variables<a name="dt-vars"></a>

The commands to build your code and flash the device might require connectivity or other information about your devices to run successfully\. AWS IoT Device Tester allows you to reference device information in flash and build commands using [JsonPath](http://goessner.net/articles/JsonPath/)\. By using simple JsonPath expressions, you can fetch the required information specified in your `device.json` file\.

#### Path variables<a name="path-variables"></a>

IDT for FreeRTOS defines the following path variables that can be used in command lines and configuration files:

`{{testData.sourcePath}}`  
Expands to the source code path\. If you use this variable, it must be used in both the flash and build commands\.

`{{sdkPath}}`  
Expands to the value in your `userData.sdkConfiguration.path` when used in the build and flash commands\.

`{{device.connectivity.serialPort}}`  
Expands to the serial port\.

`{{device.identifiers[?(@.name == 'serialNo')].value}}`  
Expands to the serial number of your device\.

`{{enableTests}}`  
Integer value indicating whether the build is for tests \(value 1\) or demos \(value 0\)\.

`{{buildImageName}}`  
The file name of the image built by the build command\.

`{{otaCodeSignerPemFile}}`  
PEM file for the OTA code signer\.