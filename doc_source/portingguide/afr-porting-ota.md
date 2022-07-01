# Porting the AWS IoT over\-the\-air \(OTA\) update library<a name="afr-porting-ota"></a>

With FreeRTOS over\-the\-air \(OTA\) updates, you can do the following:
+ Deploy new firmware images to a single device, a group of devices, or your entire fleet\.
+ Deploy firmware to devices as they are added to groups, reset, or re\-provisioned\.
+ Verify the authenticity and integrity of new firmware after it is deployed to devices\.
+ Monitor the progress of a deployment\.
+ Debug a failed deployment\.
+ Digitally sign firmware using Code Signing for AWS IoT\.

For more information, see [FreeRTOS Over\-the\-Air Updates](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ota-dev.html) in the *FreeRTOS User Guide* along with the [AWS IoT Over\-the\-air Update Documentation](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/index.html)\.

You can use the OTA update library to integrate OTA functionality into your FreeRTOS applications\. For more information, see [FreeRTOS OTA update Library](https://docs.aws.amazon.com/freertos/latest/userguide/ota-update-library.html) in the *FreeRTOS User Guide*\.

FreeRTOS devices must enforce cryptographic code\-signing verification on the OTA firmware images that they receive\. We recommend the following algorithms:
+ Elliptic\-Curve Digital Signature Algorithm \(ECDSA\)
+ NIST P256 curve
+ SHA\-256 hash

## Prerequisites<a name="porting-prereqs-ota"></a>
+ Complete the instructions in [Setting up your workspace and project for porting](porting-set-up-project.md)\.
+ Create a network transport interface port\.

  For information, see [Porting the Network Transport Interface](afr-porting-network-transport-interface.md)\.
+ Integrate coreMQTT library\. See [ coreMQTT library](https://docs.aws.amazon.com/freertos/latest/userguide/coremqtt.html) in the FreeRTOS User Guide\.
+ Create a bootloader that can support OTA updates\.

## Platform porting<a name="porting-steps-ota"></a>

You must provide an implementation of the OTA portable abstraction layer \(PAL\) to port the OTA library to a new device\. The PAL APIs are defined in the [ ota\_platform\_interface\.h](https://github.com/aws/ota-for-aws-iot-embedded-sdk/blob/main/source/include/ota_platform_interface.h) file for which implementation specific details must be provided\.


| Function name | Description | 
| --- | --- | 
| `otaPal_Abort` | Stops an OTA update\. | 
| `otaPal_CreateFileForRx` | Creates a file to store the received data chunks\. | 
| `otaPal_CloseFile` | Closes the specified file\. This might authenticate the file if you use storage that implements cryptographic protection\. | 
| `otaPal_WriteBlock` | Writes a block of data to the specified file at the given offset\. On success, the function returns the number of bytes written\. Otherwise, the function returns a negative error code\. The block size will always be a power of two and will be aligned\. For more information, see [ OTA library configuration](https://freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_config.html)\. | 
| `otaPal_ActivateNewImage` | Activates or launches the new firmware image\. For some ports, if the device is programmatically reset synchronously, this function will not return\. | 
| `otaPal_SetPlatformImageState` | Does what is required by the platform to accept or reject the most recent OTA firmware image \(or bundle\)\. To implement this function, see the documentation for your board \(platform\) details and architecture\. | 
| `otaPal_GetPlatformImageState` | Gets the state of the OTA update image\. | 

Implement the functions in this table if your device has built\-in support for them\.


| Function name | Description | 
| --- | --- | 
| `otaPal_CheckFileSignature` | Verifies the signature of the specified file\. | 
| `otaPal_ReadAndAssumeCertificate` | Reads the specified signer certificate from the file system and returns it to the caller\. | 
| `otaPal_ResetDevice` | Resets the device\. | 

**Note**  
Make sure that you have a bootloader that can support OTA updates\. For instructions on creating your AWS IoT device bootloader, see [IoT device bootloader](#afr-bootloader)\.

## E2E and PAL tests<a name="porting-steps-testing"></a>

 Run OTA PAL and E2E tests\.

### E2E tests<a name="porting-ota-e2e"></a>

OTA end to end \(E2E\) test is used to verify a device’s OTA capability and to simulate scenarios from reality\. This test will include error handling\.

#### Prerequisites<a name="e2e-prereqs"></a>

To port this test, you need the following:
+ A project with an AWS OTA library integrated in it\. Visit the[ OTA Library Porting Guide ](https://www.freertos.org/Documentation/api-ref/ota-for-aws-iot-embedded-sdk/docs/doxygen/output/html/ota_porting.html) for additional information\.
+ Port the demo application using the OTA library to interact with AWS IoT Core to do the OTA updates\. See [Porting the OTA demo application](#e2e-porting-demo-application)\.
+ Set up the IDT tool\. This runs the OTA E2E host application to build, flash, and monitor the device with different configurations, and validates the OTA library integration\.

#### Porting the OTA demo application<a name="e2e-porting-demo-application"></a>

The OTA E2E test must have an OTA demo application to validate the OTA library integration\. The demo application must have the capacity to perform OTA firmware updates\. You can find the FreeRTOS OTA demo application at [FreeRTOS GitHub](https://github.com/FreeRTOS/FreeRTOS/tree/main/FreeRTOS-Plus/Demo/AWS/Ota_Windows_Simulator) repository\. We recommend that you use the demo application as a reference, and modify it according to your specifications\. 

##### Porting steps<a name="e2e-port-demo"></a>

1. Initialize the OTA agent\.

1. Implement the OTA application callback function\.

1. Create the OTA agent event processing task\.

1. Start the OTA agent\.

1. Monitor the OTA agent statistics\.

1. Shut down the OTA agent\.

Visit [ FreeRTOS OTA over MQTT \- Entry point of the demo ](https://www.freertos.org/ota/ota-mqtt-agent-demo.html#OtaMqttAgentEntryPoint) for detailed instructions\.

##### Configuration<a name="e2e-port-config"></a>

The following configurations are necessary to interact with AWS IoT Core:
+ AWS IoT Core client credentials
  + Set\-up **democonfigROOT\_CA\_PEM** in `Ota_Over_Mqtt_Demo/demo_config.h` with Amazon Trust Services endpoints\. See [AWS server\-authentication](https://docs.aws.amazon.com/iot/latest/developerguide/server-authentication.html) for more details\.
  + Set\-up **democonfigCLIENT\_CERTIFICATE\_PEM** and **democonfigCLIENT\_PRIVATE\_KEY\_PEM** in `Ota_Over_Mqtt_Demo/demo_config.h` with your AWS IoT client credentials\. See [AWS client\-authentication details](https://docs.aws.amazon.com/iot/latest/developerguide/client-authentication.html)to learn about client certificates and private keys\.
+ Application version
+ OTA Control Protocol
+ OTA Data Protocol
+ Code Signing credentials
+ Other OTA library configurations

You can find the preceding information in `demo_config.h` and `ota_config.h` in FreeRTOS OTA demo applications\. Visit [ FreeRTOS OTA over MQTT \- Setting up the device](https://www.freertos.org/ota/ota-mqtt-agent-demo.html#OTABasicDemoClient) for more information\.

##### Build verification<a name="e2e-port-validation"></a>

Run the demo application to run the OTA job\. When it completes successfully, you can continue to run the OTA E2E tests\.

FreeRTOS [OTA demo](https://www.freertos.org/ota/ota-mqtt-agent-demo.html) provides detailed information about setting up an OTA client and an AWS IoT Core OTA job on the FreeRTOS windows simulator\. AWS OTA supports both MQTT and HTTP protocols\. Refer to the following examples for more details:
+ [OTA over MQTT Demo on Windows Simulator](https://github.com/FreeRTOS/FreeRTOS/tree/main/FreeRTOS-Plus/Demo/AWS/Ota_Windows_Simulator/Ota_Over_Mqtt_Demo)
+ [OTA over HTTP Demo on Windows Simulator](https://github.com/FreeRTOS/FreeRTOS/tree/main/FreeRTOS-Plus/Demo/AWS/Ota_Windows_Simulator/Ota_Over_Http_Demo)

#### Running tests with the IDT tool<a name="e2e-idt"></a>

To run the OTA E2E tests, you must use AWS IoT Device Tester \(IDT\) to automate the execution\. See [AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the *FreeRTOS User Guide* for more details\.

##### E2E test cases<a name="e2e-test-cases"></a>


| Test case | Description | 
| --- | --- | 
| `OTAE2EGreaterVersion` | Happy path test for regular OTA updates\. It creates an update with a newer version, which the device updates successfully\. | 
| `OTAE2EBackToBackDownloads` | This test creates 3 consecutive OTA updates\. The device is expected to update 3 consecutive times\. | 
| `OTAE2ERollbackIfUnableToConnectAfterUpdate` | This test verifies that the device rollbacks to the previous firmware if it cannot connect to network with the new firmware\. | 
| `OTAE2ESameVersion` | This test confirms that the device rejects the incoming firmware if the version stays the same\. | 
| `OTAE2EUnsignedImage` | This test verifies that the device rejects an update if the image is not signed\. | 
| `OTAE2EUntrustedCertificate` | This test verifies that the device rejects an update if the firmware is signed with an untrusted certificate\. | 
| `OTAE2EPreviousVersion` | This test verifies that the device rejects an older update version\. | 
| `OTAE2EIncorrectSigningAlgorithm` | Different devices support different signing and hashing algorithms\. This test verifies that the device fails the OTA update if it's created with a non\-supported algorithm\. | 
| `OTAE2EDisconnectResume` | This is the happy path test for the suspend and resume feature\. This test creates an OTA update and starts the update\. It then connects to AWS IoT Core with the same client ID \(thing name\) and credentials\. AWS IoT Core then disconnects the device\. The device is expected to detect that it is disconnected from AWS IoT Core, and after a period of time, move itself to a suspended state and try to reconnect to AWS IoT Core and resume the download\. | 
| `OTAE2EDisconnectCancelUpdate` | This test checks if the device can recover itself if the OTA job gets canceled while it is in a suspended state\. It does the same thing as the `OTAE2EDisconnectResume` test, except that after connecting to AWS IoT Core, which disconnects the device, it cancels the OTA update\. A new update is created\. The device is expected to reconnect to the AWS IoT Core, abort the current update, go back to waiting state, and accept and finish the next update\. | 
| `OTAE2EPresignedUrlExpired` | When an OTA update is created, you can configure the lifetime of the S3 pre\-signed url\. This test verifies that the device is able to perform an OTA, even if it cannot finish the download when the url expires\. The device is expected to request a new job document, which contains a new url to resume the download\. | 
| `OTAE2E2UpdatesCancel1st` | This test creates two OTA updates in a row\. When the device reports that it is downloading the first update, the test force\-cancels the first update\. The device is expected to abort the current update and pick up the second update, and complete it\. | 
| `OTAE2ECancelThenUpdate` | This test creates two OTA updates in a row\. When the device reports that it is downloading the first update, the test force\-cancels the first update\. The device is expected to abort the current update and pick up the second update, then complete it\. | 
| `OTAE2EImageCrashed` | This test checks that the device is able to reject an update when the image crashes\. | 

### PAL tests<a name="porting-ota-pal"></a>

#### Prerequisites<a name="pal-prereqs"></a>

To port the Network Transport Interface tests, you need the following:
+ A project that can build FreeRTOS with a valid FreeRTOS kernel port\.
+ A working implementation of OTA PAL\.

#### Porting<a name="pal-porting"></a>
+ Add [FreeRTOS\-Libraries\-Integration\-Tests](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests) as a submodule into your project\. The location of the submodule in the project must be where it can be built\.
+ Copy `config_template/test_execution_config_template.h` and `config_template/test_param_config_template.h` to a location in the build path, and rename them to `test_execution_config.h` and `test_param_config.h`\.
+ Include relevant files in the build system\. If using `CMake`, `qualification_test.cmake` and `src/ota_pal_tests.cmake` can be used to include relevant files\.
+ Configure the test by implementing the following functions:
  + `SetupOtaPalTestParam()`: defined in `src/ota/ota_pal_test.h`\. The implementation must have the same name and signature as defined in `ota_pal_test.h`\. Currently, you do not need to configure this function\.
+ Implement **UNITY\_OUTPUT\_CHAR** so that test output logs do not interleave with device logs\.
+ Call `RunQualificationTest()` from the application\. The device hardware must be properly initialized, and the network must be connected before the call\.

#### Testing<a name="ota-testing"></a>

This section describes the local testing of the OTA PAL qualification tests\.

##### Enable the test<a name="ota-testing-enabling"></a>

Open `test_execution_config.h` and define **OTA\_PAL\_TEST\_ENABLED** to 1\.

In `test_param_config.h`, update the following options:
+ **OTA\_PAL\_TEST\_CERT\_TYPE**: Select the certificate type used\.
+ **OTA\_PAL\_CERTIFICATE\_FILE**: Path to the device certificate, if applicable\.
+ **OTA\_PAL\_FIRMWARE\_FILE**: Name of the firmware file, if applicable\.
+ **OTA\_PAL\_USE\_FILE\_SYSTEM**: Set to 1 if the OTA PAL uses file system abstraction\.

Build and flash the application using a tool chain of your choice\. When the `RunQualificationTest()` is called, the OTA PAL tests will run\. The test results are output to the serial port\.

### Integrating OTA tasks<a name="integrating-ota"></a>
+ Add OTA agent to your current MQTT demo\.
+ Run OTA End to End \(E2E\) tests with AWS IoT\. This verifies if the integration is working as expected\.

**Note**  
To officially qualify a device for FreeRTOS, you must validate the device's ported source code against OTA PAL and OTA E2E test groups with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the *FreeRTOS User Guide* to set up AWS IoT Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the AWS IoT Device Tester `configs` folder\.

## IoT device bootloader<a name="afr-bootloader"></a>

You must provide your own secure bootloader application\. Make sure that the design and implementation provide proper mitigation to security threats\. Below is the threat modeling for your reference\.

### Threat modeling for the IoT device bootloader<a name="afr-threat-model-for-bootloader"></a>

#### Background<a name="afr-threat-model-for-bootloader-background"></a>

As a working definition, the embedded AWS IoT devices referenced by this threat model are microcontroller\-based products that interact with cloud services\. They may be deployed in consumer, commercial, or industrial settings\. IoT devices may gather data about a user, a patient, a machine, or an environment, and may control anything from light bulbs and door locks to factory machinery\.

Threat modeling is an approach to security from the point of view of a hypothetical adversary\. By considering the adversary's goals and methods, a threat list is created\. Threats are attacks against a resource or asset performed by an adversary\. The list is prioritized and used to identify and create mitigation solutions\. When choosing a mitigation solution, the cost of implementing and maintaining it should be balanced with the real security value it provides\. There are multiple [threat model methodologies](https://en.wikipedia.org/wiki/Threat_model)\. Each is capable of supporting the development of a secure and successful AWS IoT product\.

FreeRTOS offers OTA \(over\-the\-air\) software updates to AWS IoT devices\. The update facility combines cloud services with on\-device software libraries and a partner\-supplied bootloader\. This threat model focuses specifically on threats against the bootloader\.

**Bootloader use cases**
+ Digitally sign and encrypt firmware before deployment\.
+ Deploy new firmware images to a single device, a group of devices, or an entire fleet\.
+ Verify the authenticity and integrity of new firmware after it's deployed to devices\.
+ Devices only run unmodified software from a trusted source\.
+ Devices are resilient to faulty software received through OTA\.

**Data Flow Diagram**

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/bootloader-dataflow-diagram.png)

#### Threats<a name="afr-threat-model-for-bootloader-threats"></a>

Some attacks have multiple mitigation models; for example, a network man\-in\-the\-middle intended to deliver a malicious firmware image is mitigated by verifying trust in both the certificate offered by the TLS server, and the code\-signer certificate of the new firmware image\. To maximize the security of the bootloader, any non\-bootloader mitigation solutions are considered unreliable\. The bootloader should have intrinsic mitigation solutions for each attack\. Having layered mitigation solutions are known as defense\-in\-depth\.

**Threats:**
+ An attacker hijacks the device's connection to the server to deliver a malicious firmware image\.

**Mitigation example**
  + Upon boot, the bootloader verifies the cryptographic signature of the image using a known certificate\. If the verification fails, the bootloader rolls back to the previous image\.
+ An attacker exploits a buffer overflow to introduce malicious behavior to the existing firmware image stored in flash\.

**Mitigation examples**
  + Upon boot, the bootloader verifies, as previously described\. When verification fails with no previous image available, the bootloader halts\.
  + Upon boot, the bootloader verifies, as previously described\. When verification fails with no previous image available, the bootloader enters a fail safe OTA only mode\.
+ An attacker boots the device to a previously stored image, which is exploitable\.

**Mitigation examples**
  + Flash sectors storing the last image are erased upon successful installation and test of a new image\.
  + A fuse is burned with each successful upgrade, and each image refuses to run unless the correct number of fuses have been burned\.
+ An OTA update delivers a faulty or malicious image that bricks the device\.

**Mitigation example**
  + The bootloader starts a hardware watchdog timer that triggers rollback to the previous image\.
+ An attacker patches the bootloader to bypass image verification so the device will accept unsigned images\.

**Mitigation examples**
  + The bootloader is in ROM \(read\-only memory\), and cannot be modified\.
  + The bootloader is in OTP \(one\-time\-programmable memory\), and cannot be modified\.
  + The bootloader is in the secure zone of ARM TrustZone, and cannot be modified\.
+ An attacker replaces the verification certificate so the device will accept malicious images\.

**Mitigation examples**
  + The certificate is in a cryptographic co\-processor, and cannot be modified\.
  + The certificate is in ROM \(or OTP, or secure zone\), and cannot be modified\.

#### Further threat modeling<a name="afr-threat-model-for-bootloader-further"></a>

This threat model considers only the bootloader\. Further threat modeling could improve overall security\. A recommended method is to list the adversary's goals, the assets targeted by those goals, and points of entry to the assets\. A list of threats can be made by considering attacks on the points of entry to gain control of the assets\. The following are lists of examples of goals, assets, and entry points for an IoT device\. These lists are not exhaustive, and are intended to spur further thought\.

**Adversary's goals**
+ Extort money 
+ Ruin reputations 
+ Falsify data 
+ Divert resources 
+ Remotely spy on a target 
+ Gain physical access to a site 
+ Wreak havoc
+ Instill terror 

**Key assets**
+ Private keys 
+ Client certificate 
+ CA root certificates 
+ Security credentials and tokens 
+ Customer's personally identifiable information 
+ Implementations of trade secrets 
+ Sensor data 
+ Cloud analytics data store 
+ Cloud infrastructure 

**Entry points**
+ DHCP response 
+ DNS response 
+ MQTT over TLS 
+ HTTPS response 
+ OTA software image 
+ Other, as dictated by application, for example, USB 
+ Physical access to bus 
+ Decapped IC 