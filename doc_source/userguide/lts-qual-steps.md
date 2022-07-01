# Preparing to test your microcontroller board for the first time<a name="lts-qual-steps"></a>

You can use IDT for FreeRTOS to test your implementation of the FreeRTOS libraries\. After you have ported the FreeRTOS libraries for your board’s device drivers, use AWS IoT Device Tester to run the qualification tests on your microcontroller board\. 

## Add library porting layers and implement a FreeRTOS tests repository<a name="lts-add-port-layer"></a>

To port FreeRTOS for your device, see the [FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/porting-guide.html)\. When implementing the FreeRTOS tests repository and porting the FreeRTOS layers, you must provide a `manifest.yml` with paths to each library, including the tests repository\. This file will be in the root directory of your source code\. See [ manifest file instructions](https://docs.aws.amazon.com/freertos/latest/qualificationguide/afq-checklist-manifest-instr.html) for details\.

## Configure your AWS credentials<a name="lts-cfg-aws-afr"></a>

You need to configure your AWS credentials for AWS IoT Device Tester to communicate with the AWS Cloud\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html)\. Valid AWS credentials are specified in the `devicetester_extract_location/devicetester_freertos_[win|mac|linux]/configs/config.json` configuration file\.

```
"auth": {
   "method": "environment"
}

"auth": {
    "method": "file",
    "credentials": {
        "profile": "<your-aws-profile>"
    }
}
```

The `auth` attribute of the `config.json` file has a method field that controls AWS authentication, and can be declared as either file or environment\. Setting the field to environment pulls your AWS credentials from your host machine’s environment variables\. Setting the field to file imports a specified profile from the `.aws/credentials` config file\.

## Create a device pool in IDT for FreeRTOS<a name="lts-cfg-dt-dp"></a>

Devices to be tested are organized in device pools\. Each device pool consists of one or more identical devices\. You can configure IDT for FreeRTOS to test a single device, or multiple devices in a pool\. To accelerate the qualification process, IDT for FreeRTOS can test devices with the same specifications in parallel\. It uses a round\-robin method to execute a different test group on each device in a device pool\.

The `device.json` file has an array in its top level\. Each array attribute is a new device pool\. Each device pool has a devices array attribute, which has multiple devices declared\. In the template, there is a device pool and only one device in that device pool\. You can add one or more devices to a device pool by editing the `devices` section of the `device.json` template in the `configs` folder\.

**Note**  
All devices in the same pool must be of the same technical specification and SKU\. To enable parallel builds of the source code for different test groups, IDT for FreeRTOS copies the source code to a results folder inside the IDT for FreeRTOS extracted folder\. You must reference the source code path in your build or flash command using the `testdata.sourcePath` variable\. IDT for FreeRTOS replaces this variable with a temporary path of the copied source code\. For more information, see [IDT for FreeRTOS variables](#lts-dt-vars)\.

The following is an example `device.json` file was used to create a device pool with multiple devices\.

```
[
    {
        "id": "pool-id",
        "sku": "sku",
        "features": [
           {
              "name": "Wifi",
              "value": "Yes | No"
           },
           {
              "name": "Cellular",
              "value": "Yes | No"
           },
           {
              "name": "BLE",
              "value": "Yes | No"
          },
          {
             "name": "PKCS11",
             "value": "RSA | ECC | Both"
          },
          {
              "name": "OTA",
              "value": "Yes | No",
              "configs": [
              {
                  "name": "OTADataPlaneProtocol",
                  "value": "MQTT | HTTP | None"
              }
            ]
          },
          {
             "name": "KeyProvisioning",
             "value": "Onboard | Import | Both | No"
          }
        ],
        "devices": [
          {
            "id": "device-id",
            "connectivity": {
              "protocol": "uart",
              "serialPort": "/dev/tty*"
            },
            "secureElementConfig" : {
              "publicKeyAsciiHexFilePath": "absolute-path-to/public-key-txt-file: contains-the-hex-bytes-public-key-extracted-from-onboard-private-key",
              "publiDeviceCertificateArn": "arn:partition:iot:region:account-id:resourcetype:resource:qualifier",
              "secureElementSerialNumber": "secure-element-serialNo-value",
              "preProvisioned"           : "Yes | No"                    
            },         
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

** `id` **  
A user\-defined alphanumeric ID that uniquely identifies a pool of devices\. Devices that belong to a pool must be of the same type\. When a suite of tests is running, devices in the pool are used to parallelize the workload\.

** `sku` **  
An alphanumeric value that uniquely identifies the board you are testing\. The SKU is used to track qualified boards\.  
If you want to list your board in AWS Partner Device Catalog, the SKU you specify here must match the SKU that you use in the listing process\.

** `features` **  
An array that contains the device's supported features\. AWS IoT Device Tester uses this information to select the qualification tests to run\.  
Supported values are:    
** `Wifi` **  
Indicates if your board has Wi\-Fi capabilities\.  
** `Cellular` **  
Indicates if your board has cellular capabilities\.  
** `PKCS11` **  
Indicates the public key cryptography algorithm that the board supports\. PKCS11 is required for qualification\. Supported values are `ECC`, `RSA`, and `Both`\. `Both` indicates the board supports both `ECC` and `RSA`\.  
** `KeyProvisioning` **  
Indicates the method of writing a trusted X\.509 client certificate onto your board\.   

Valid values are `Import`, `Onboard`, `Both` and `No`\. You must use either `Online` or `Both` as the key provisioning method for qualification\.
+ You use `Import` if your board allows only the import of private keys\. Selecting `Import` is not a valid configuration for qualification and should be used only for testing purposes, in particular with PKCS11 test cases\. To qualify your device, you must use `Onboard` or `Both` for key provisioning\.
+ Use `Onboard` if your board supports on\-board private key generation \(for example, if your device has a secure element, or if you prefer to generate your own device key pair and certificate\)\. Make sure you add a `secureElementConfig` element in each of the device sections and put the absolute path to the public key file in the `publicKeyAsciiHexFilePath` field\.
+ Use `Both` if your board supports both importing private keys and on\-board key generation for key provisioning\.
+ Use `No` if your board doesn't support key provisioning\.   
** `OTA` **  
Indicates if your board supports over\-the\-air \(OTA\) update functionality\. The `OtaDataPlaneProtocol` attribute indicates which OTA dataplane protocol the device supports\. OTA with either HTTP or MQTT dataplane protocol is required for qualification\.   
** `BLE` **  
Indicates if your board supports Bluetooth Low Energy \(BLE\)\.

** `devices.id` **  
A user\-defined unique identifier for the device being tested\.

** `devices.connectivity.serialPort` **  
The serial port of the host computer used to connect to the devices being tested\.

** `devices.secureElementConfig.PublicKeyAsciiHexFilePath` **  
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

** `devices.secureElementConfig.PublicDeviceCertificateArn` **  
The ARN of the certificate from your secure element that is uploaded to AWS IoT Core\. For information about uploading your certificate to AWS IoT Core, see [X\.509 client certificates](https://docs.aws.amazon.com/iot/latest/developerguide/x509-client-certs.html) in the *AWS IoT Developer Guide*\. 

** `devices.secureElementConfig.SecureElementSerialNumber` **  
\(Optional\) The serial number of the secure element\. The serial number is optionally used to create device certificates for JITR key provisioning\. Provide this field when the serial number is printed out along with the device public key when you run the FreeRTOS demo/test project\.

** `devices.secureElementConfig.preProvisioned` **  
\(Optional\) Set to "Yes" if the device has a per\-provisioned secure element with locked\-down credentials, that cannot import, create, or destroy objects\. This configuration takes effect only when `features` has `KeyProvisioning` set to **Onboard**, along with `PKCS11` set to **ECC**\. If this attribute is set to **Yes**, you must provide the corresponding pkcs11 labels\.

** `devices.secureElementConfig.pkcs11JITPCodeVerifyRootCertSupport` **  
\(Optional\) Set to **Yes** if the device has support for PKCS11 storage of JITP Certificate, Code Verification Key, and Root Certificate\. If set to **Yes**, you must provide the corresponding pkcs11 labels\.

** `identifiers` **  
\(Optional\) An array of arbitrary name\-value pairs\. You can use these values in the build and flash commands described in the next section\.

## Configure build, flash, and test settings<a name="lts-cfg-dt-ud"></a>

IDT for FreeRTOS builds and flashes tests on to your board automatically\. To enable this, you must configure IDT to run the build and flash commands for your hardware\. The build and flash command settings are configured in the `userdata.json` template file located in the `config` folder\.

### Configure settings for testing devices<a name="lts-config-settings-device"></a>

Build, flash, and test settings are made in the `configs/userdata.json` file\. The following JSON example shows how you can configure IDT for FreeRTOS to test multiple devices:

```
{
    "sourcePath": "</path/to/freertos>",
    "freeRTOSVersion": "<freertos-version>",
    "freeRTOSTestParamConfigPath": "{{testData.sourcePath}}/path/from/source/path/to/test_param_config.h",
    "freeRTOSTestExecutionConfigPath": "{{testData.sourcePath}}/path/from/source/path/to/test_execution_config.h",
    "buildTool": {
        "name": "your-build-tool-name",
        "version": "your-build-tool-version",
        "command": [
            "<build command> -any-additional-flags {{testData.sourcePath}}"
        ]
    },
    "flashTool": {
        "name": "your-flash-tool-name",
        "version": "your-flash-tool-version",
        "command": [
            "<flash command> -any-additional-flags {{testData.sourcePath}} -any-additional-flags {{buildImageName}}"
        ],
        "buildImageInfo": {
            "testsImageName": "aws_tests.bin",
            "demosImageName": "aws_demos.bin"
        }
    },
    "testStartDelayms": 0,
    "echoServerConfiguration": {
      "keyGenerationMethod": "EC | RSA",
      "serverPort": 9000      
    },
    "otaConfiguration": {
        "otaE2EDemoFilePath": "{{testData.sourcePath}}/relative-path-to/ota-demo-bin-file",
        "otaE2EDemoConfigFilePath": "{{testData.sourcePath}}/relative-path-to/ota-demo-config-header-file",
        "otaE2EFirmwareFilePath": "{{testData.sourcePath}}/relative-path-to/ota-image-generated-in-build-process",
        "otaE2EdeviceFirmwareFileName": "ota-image-name-on-device",
        "otaPALCertificateFile": "<path/to/ota/pal/certificate/on/device>",
        "otaPALFirmwareFileName" : "<firmware-file-name>",
        "otaPALUseFileSystem": "Yes | No",
        "codeSigningConfiguration": {
            "signingMethod": "AWS | Custom",
            "signerHashingAlgorithm": "SHA1 | SHA256",
            "signerSigningAlgorithm": "RSA | ECDSA",
            "signerCertificate": "arn:partition:service:region:account-id:resource:qualifier | /absolute-path-to/signer-certificate-file",
            "untrustedSignerCertificate": "arn:partition:service:region:account-id:resourcetype:resource:qualifier",
            "signerCertificateFileName": "signerCertificate-file-name",
            "compileSignerCertificate": true | false,
            // ***********Use signerPlatform if you choose AWS for signingMethod***************
            "signerPlatform": "AmazonFreeRTOS-Default | AmazonFreeRTOS-TI-CC3220SF",
            // ***********Use signCommand if you choose custom for signingMethod***************
            "signCommand": [ 
                "/absolute-path-to/sign.sh {{inputImageFilePath}} {{outputSignatureFilePath}}" 
            ]
         }   
    },
    **********
    This section is used for PKCS #11 labels of private key, public key, device certificate, code verification key, JITP certificate, and root certificate.
    When configuring PKCS11, you set up labels and you must provide the labels of the device certificate, public key, 
    and private key for the key generation type (EC or RSA) it was created with. If your device supports PKCS11 storage of JITP certificate, 
    code verification key, and root certificate, set 'pkcs11JITPCodeVerifyRootCertSupport' to 'Yes' in device.json and provide the corresponding labels.
    **********
    "pkcs11LabelConfiguration":{
        "pkcs11LabelDevicePrivateKeyForTLS": "<device-private-key-label>",
        "pkcs11LabelDevicePublicKeyForTLS": "<device-public-key-label>",
        "pkcs11LabelDeviceCertificateForTLS": "<device-certificate-label>",
        "pkcs11LabelPreProvisionedECDevicePrivateKeyForTLS": "<preprovisioned-ec-device-private-key-label>",
        "pkcs11LabelPreProvisionedECDevicePublicKeyForTLS": "<preprovisioned-ec-device-public-key-label>",
        "pkcs11LabelPreProvisionedECDeviceCertificateForTLS": "<preprovisioned-ec-device-certificate-label>",
        "pkcs11LabelPreProvisionedRSADevicePrivateKeyForTLS": "<preprovisioned-rsa-device-private-key-label>",
        "pkcs11LabelPreProvisionedRSADevicePublicKeyForTLS": "<preprovisioned-rsa-device-public-key-label>",
        "pkcs11LabelPreProvisionedRSADeviceCertificateForTLS": "<preprovisioned-rsa-device-certificate-label>",
        "pkcs11LabelCodeVerifyKey": "<code-verification-key-label>",
        "pkcs11LabelJITPCertificate": "<JITP-certificate-label>",
        "pkcs11LabelRootCertificate": "<root-certificate-label>"
     }   
  }
```

The following lists the attributes used in `userdata.json`:

**  `sourcePath` **  
The path to the root of the ported FreeRTOS source code\.

**  `freeRTOSTestParamConfigPath` **  
The path to `test_param_config.h` file, using the `{{testData.sourcePath}}` variable to make it relative to the source code\.

**  `freeRTOSTestExecutionConfigPath` **  
The path to `test_execution_config.h` file using the `{{testData.sourcePath}}` variable to make it relative to the source code\.

**  `freeRTOSVersion` **  
The version of FreeRTOS used in your implementation\. See [Supported versions of AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/dev-test-versions-afr.html) for the FreeRTOS versions compatible with AWS IoT Device Tester for FreeRTOS\. 

**  `buildTool` **  
The command to build your source code\. All references to the source code path in the build command must be replaced by the AWS IoT Device Tester variable `{{testData.sourcePath}}`\. Use the `{{config.idtRootPath}}` placeholder to reference a build script relative to the AWS IoT Device Tester root path\.

**  `flashTool` **  
The command to flash an image to your device\. All references to the source code path in the ﬂash command must be replaced by the AWS IoT Device Tester variable `{{testData.sourcePath}}`\. Use the `{{config.idtRootPath}}` placeholder to reference a flash script relative to the AWS IoT Device Tester root path\.    
** `buildImageInfo` **    
** `testsImageName` **  
The name of the file produced by the build command when building tests from the `freertos-source/tests` folder\.  
** `demosImageName` **  
The name of the file produced by the build command when building tests from the `freertos-source/demos` folder\.

**  `testStartDelayms` **  
Specifies how many milliseconds the FreeRTOS test runner will wait before starting to run tests\. This can be useful if the device under test begins to output important test information before IDT has a chance to connect and start logging due to network or other latency issues\. This value is applicable to FreeRTOS test groups only, and not to other test groups that do not utilize the FreeRTOS test runner, such as the OTA tests\. If you receive an error related to **expected 10 but received 5**, this field should be set to 5000\.

**  `echoServerConfiguration` **  
The configuration to setup the echo server for the TLS test\. This field is required\.    
** `keyGenerationMethod` **  
The echo server is configured with this option\. The options are EC, or RSA\.  
** `serverPort` **  
The port number on which the echo server runs\.

**  `otaConfiguration` **  
The configuration for OTA PAL and OTA E2E tests\. This field is required\.    
**`otaE2EDemoFilePath`**  
Path to the OTA End to End demo project\.  
**`otaE2EDemoConfigFilePath`**  
Path to the config file that IDT uses to modify the OTA End to End demo\. In most cases, this is the same path as `freeRTOSTestParamConfigPath`, but might differ for you\.  
**`otaE2EFirmwareFilePath`**  
Path to the bin image that IDT uses for the OTA End to End tests\.  
**`otaE2EDeviceFirmwareFileName`**  
The name of the bin image that IDT uses for the OTA End to End tests\.  
** `otaPALCertificateFile` **  
The path to the certificate for OTA PAL test on device\. This is used to verify the signature\. For example, **ecdsa\-sha256\-signer\.crt\.pem**\.  
** `otaPALFirmwareFileName` **  
The file name for some devices that have a hard\-coded name for the firmware image to boot\. We recommend to include this, if applicable\. For example, **dummy\.bin**\.  
** `otaPALUseFileSystem` **  
Some device’s OTA Pal layers will use the file names passed into it for the image and the certificates\. This is because their non\-volatile memory is abstracted by a file system\. Please select `Yes` if this is applicable or select `No`\.  
** `codeSigningConfiguration` **    
** `signingMethod` **  
The code signing method\. Possible values are AWS or Custom\.  
For the Beijing and Ningxia Regions, use Custom\. AWS code signing is not supported in that region\.  
** `signerHashingAlgorithm` **  
The hashing algorithm supported on the device\. Possible values are `SHA1` or `SHA256`\.  
** `signerSigningAlgorithm` **  
The signing algorithm supported on the device\. Possible values are `RSA` or `ECDSA`\.  
** `signerCertificate` **  
The trusted certificate used for OTA\. For the AWS code signing method, use the Amazon Resource Name \(ARN\) for the trusted certificate uploaded to the AWS Certificate Manager\. For the Custom code signing method, use the absolute path to the signer certificate file\. For information about creating a trusted certificate, see [ Create a code\-signing certificate](https://docs.aws.amazon.com/freertos/latest/userguide/ota-code-sign-cert.html)\.  
** `untrustedSignerCertificate` **  
The ARN or filepath for a second certificate used in some OTA tests as an untrusted certificate\. For information about creating a certificate, see [ Create a code\-signing certificate](https://docs.aws.amazon.com//freertos/latest/userguide/ota-code-sign-cert.html)\.  
** `signerCertificateFileName` **  
The file name of the code signing certificate on the device\. This value must match the file name that you provided when you ran the `aws acm import-certificate` command\.  
** `compileSignerCertificate` **  
Boolean value that determines the status of the signature verification certificate\. Valid values are `true` and `false`\.   
Set this value to **true** if the code signer signature verification certificate is not provisioned or flashed\. It must be compiled into the project\. AWS IoT Device Tester fetches the trusted certificate and compiles it into `aws_codesigner_certificate.h`\.   
** `signerPlatform` **  
The signing and hashing algorithm that AWS Code Signer uses while creating the OTA update job\. Currently, the possible values for this field are `AmazonFreeRTOS-TI-CC3220SF` and `AmazonFreeRTOS-Default`\.  
+ Choose `AmazonFreeRTOS-TI-CC3220SF` if `SHA1` and `RSA`\.
+ Choose `AmazonFreeRTOS-Default` if `SHA256` and `ECDSA`\.
+ If you need `SHA256` \| `RSA` or `SHA1` \| `ECDSA` for your configuration, contact us for further support\.
+ Configure `signCommand` if you chose `Custom` for `signingMethod`\.  
** `signCommand` **  
Two placeholders `{{inputImageFilePath}}` and `{{outputSignatureFilePath}}` are required in the command\. `{{inputImageFilePath}}` is the file path of the image built by IDT to be signed\. `{{outputSignatureFilePath}} `is the file path of the signature which will be generated by the script\.

**  `pkcs11LabelConfiguration` **    
** `pkcs11LabelDevicePrivateKeyForTLS` **  
\(Optional\) This label is used for the PKCS \#11 label of the private key\. For devices with onboard and import support of key provisioning, this label is used for testing\. This label may be different than the one defined for the pre\-provisioned case\. If you have key provisioning set to **No** and pre\-provisioned set to **Yes**, in `device.json`, this will be undefined\.  
** `pkcs11LabelDevicePublicKeyForTLS` **  
\(Optional\) This label is used for the PKCS \#11 label of the public key\. For devices with onboard and import support of key provisioning, this label is used for testing\. This label may be different than the one defined for pre\-provisioned case\. If you have key provisioning set to **No** and pre\-provisioned set to **Yes**, in `device.json`, this will be undefined\.  
** `pkcs11LabelDeviceCertificateForTLS` **  
\(Optional\) This label is used for the PKCS \#11 label of the device certificate\. For devices with onboard and import support of key provisioning, this label will be used for testing\. This label may be different than the one defined for pre\-provisioned case\. If you have key provisioning set to **No** and pre\-provisioned set to **Yes**, in `device.json`, this will be undefined\.  
** `pkcs11LabelPreProvisionedECDevicePrivateKeyForTLS` **  
\(Optional\) This label is used for the PKCS \#11 label of the private key\. For devices with secure elements or hardware limitations, this will have a different label to preserve AWS IoT credentials\. If your device supports pre\-provisioning with an EC key, provide this label\. When preProvisioned is set to **Yes** in `device.json`, this label, `pkcs11LabelPreProvisionedRSADevicePrivateKeyForTLS`, or both must be provided\. This label may be different than the one defined for onboard and import cases\.  
** `pkcs11LabelPreProvisionedECDevicePublicKeyForTLS` **  
\(Optional\) This label is used for the PKCS \#11 label of the public key\. For devices with secure elements or hardware limitations, this will have a different label to preserve AWS IoT credentials\. If your device supports pre\-provisioning with an EC key, provide this label\. When preProvisioned is set to **Yes** in `device.json`, this label, `pkcs11LabelPreProvisionedRSADevicePublicKeyForTLS`, or both must be provided\. This label may be different than the one defined for onboard and import cases\.  
** `pkcs11LabelPreProvisionedECDeviceCertificateForTLS` **  
\(Optional\) This label is used for the PKCS \#11 label of the device certificate\. For devices with secure elements or hardware limitations, this will have a different label to preserve AWS IoT credentials\. If your device supports pre\-provisioning with an EC key, provide this label\. When preProvisioned is set to **Yes** in `device.json`, this label, `pkcs11LabelPreProvisionedRSADeviceCertificateForTLS`, or both must be provided\. This label may be different than the one defined for onboard and import cases\.  
** `pkcs11LabelPreProvisionedRSADevicePrivateKeyForTLS` **  
\(Optional\) This label is used for the PKCS \#11 label of the private key\. For devices with secure elements or hardware limitations, this will have a different label to preserve AWS IoT credentials\. If your device supports pre\-provisioning with an RSA key, provide this label\. When preProvisioned is set to **Yes** in `device.json`, this label, `pkcs11LabelPreProvisionedECDevicePrivateKeyForTLS`, or both must be provided\.  
** `pkcs11LabelPreProvisionedRSADevicePublicKeyForTLS` **  
\(Optional\) This label is used for the PKCS \#11 label of the public key\. For devices with secure elements or hardware limitations, this will have a different label to preserve AWS IoT credentials\. If your device supports pre\-provisioning with an RSA key, provide this label\. When preProvisioned is set to **Yes** in `device.json`, this label, `pkcs11LabelPreProvisionedECDevicePublicKeyForTLS`, or both must be provided\.  
** `pkcs11LabelPreProvisionedRSADeviceCertificateForTLS` **  
\(Optional\) This label is used for the PKCS \#11 label of the device certificate\. For devices with secure elements or hardware limitations, this will have a different label to preserve AWS IoT credentials\. If your device supports pre\-provisioning with an RSA key, provide this label\. When preProvisioned is set to **Yes** in `device.json`, this label, `pkcs11LabelPreProvisionedECDeviceCertificateForTLS`, or both must be provided\.  
** `pkcs11LabelCodeVerifyKey` **  
\(Optional\) This label is used for the PKCS \#11 label of the code verification key\. If your device has PKCS \#11 storage support of the JITP certificate, code verification key, and root certificate, provide this label\. When `pkcs11JITPCodeVerifyRootCertSupport` in `device.json` is set to **Yes**, this label must be provided\.  
** `pkcs11LabelJITPCertificate` **  
\(Optional\) This label is used for the PKCS \#11 label of the JITP certificate\. If your device has PKCS \#11 storage support of the JITP certificate, code verification key, and root certificate, provide this label\. When `pkcs11JITPCodeVerifyRootCertSupport` in `device.json` is set to **Yes**, this label must be provided\.

### IDT for FreeRTOS variables<a name="lts-dt-vars"></a>

The commands to build your code and flash the device might require connectivity or other information about your devices to run successfully\. AWS IoT Device Tester allows you to reference device information in flash and build commands using [JsonPath](http://goessner.net/articles/JsonPath/)\. By using simple JsonPath expressions, you can fetch the required information specified in your `device.json` file\.

#### Path variables<a name="path-variables-lts"></a>

IDT for FreeRTOS defines the following path variables that can be used in command lines and configuration files:

** `{{testData.sourcePath}}` **  
Expands to the source code path\. If you use this variable, it must be used in both the flash and build commands\.

** `{{device.connectivity.serialPort}}` **  
Expands to the serial port\.

** `{{device.identifiers[?(@.name == 'serialNo')].value[0]}}` **  
Expands to the serial number of your device\.

** `{{config.idtRootPath}}` **  
Expands to the AWS IoT Device Tester root path\.