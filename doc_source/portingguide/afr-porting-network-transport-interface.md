# Porting the Network Transport Interface<a name="afr-porting-network-transport-interface"></a>

## Integrating the TLS library<a name="afr-porting-tls"></a>

For Transport Layer Security \(TLS\) authentication, use your preferred TLS stack\. We recommend using [Mbed TLS](https://tls.mbed.org/) because it is tested with FreeRTOS libraries\. You can find an example of this at this [GitHub](https://github.com/FreeRTOS/FreeRTOS) repository\.

Regardless of the TLS implementation used by your device, you must implement the underlying transport hooks for TLS stack with TCP/IP stack\. They must support the [TLS cipher suites that are supported by AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/transport-security.html#tls-cipher-suite-support)\.

## Porting the Network Transport Interface library<a name="network-intro"></a>

You must implement a network transport interface to use [coreMQTT](https://www.freertos.org/mqtt/index.html) and [coreHTTP](https://www.freertos.org/http/index.html)\. Network Transport Interface contains function pointers and context data required to send and receive data on a single network connection\. See [Transport Interface](https://www.freertos.org/network-interface.html) for more details\. FreeRTOS provides a set of built\-in network transport interface tests to validate these implementations\. The following section guides you how to set up your project to run these tests\. 

## Prerequisites<a name="prereqs"></a>

To port this test, you need the following:
+ A project with a build system that can build FreeRTOS with a validated FreeRTOS kernel port\.
+ Working implementation of network drivers\.

## Porting<a name="porting-network-transport-interface"></a>
+ Add [ FreeRTOS\-Libraries\-Integration\-Tests](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests) as a submodule into your project\. It doesn’t matter where the submodule is placed in the project, as long as it can be built\.
+ Copy `config_template/test_execution_config_template.h` and `config_template/test_param_config_template.h` to a project location in the build path, and rename them to `test_execution_config.h` and `test_param_config.h`\.
+ Include relevant files into the build system\. If using `CMake`, `qualification_test.cmake` and `src/transport_interface_tests.cmake` are used to include relevant files\.
+ Implement the following functions at an appropriate project location:
+ 
  + A `network connect function`: The signature is defined by `NetworkConnectFunc` in `src/common/network_connection.h`\. This function takes in a pointer to network context, a pointer to host info, and a pointer to network credentials\. It establishes a connection with the server specified in the host info with the provided network credentials\.
  + A `network disconnect function`: The signature is defined by `NetworkDisconnectFunc` in `src/common/network_connection.h`\. This function takes in a pointer to a network context\. It disconnects a previously established connection stored in the network context\.
  + `setupTransportInterfaceTestParam()`: This is defined in `src/transport_interface/transport_interface_tests.h`\. The implementation must have exactly the same name and signature as defined in `transport_interface_tests.h`\. This function takes in a pointer to a *TransportInterfaceTestParam* struct\. It will populate the fields in the *TransportInterfaceTestParam* struct that is used by the transport interface test\.
+ Implement **UNITY\_OUTPUT\_CHAR** so that test output logs do not interleave with device logs\.
+ Call `runQualificationTest()`from the application\. The device hardware must be properly initialized and the network must be connected before the call\.

### Credential management \(on\-device generated key\)<a name="cred-management-key"></a>

When **FORCE\_GENERATE\_NEW\_KEY\_PAIR** in `test_param_config.h` is set to 1, the device application generates a new on\-device key pair and outputs the public key\. The device application uses **ECHO\_SERVER\_ROOT\_CA** and **TRANSPORT\_CLIENT\_CERTIFICATE** as the echo server root CA and client certificate when establishing a TLS connection with the echo server\. IDT sets these parameters during the qualification run\. 

### Credential Management \(importing key\)<a name="cred-management-importing-key"></a>

The device application uses **ECHO\_SERVER\_ROOT\_CA**, **TRANSPORT\_CLIENT\_CERTIFICATE** and **TRANSPORT\_CLIENT\_PRIVATE\_KEY** in `test_param_config.h` as the echo server root CA, client certificate, and client private key when establishing a TLS connection with the echo server\. IDT sets these parameters during the qualification run\.

## Testing<a name="testing-transport-interface"></a>

This section describes how you can locally test the transport interface with the qualification tests\. Alternatively, you can also use IDT to automate the execution\. See [AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the *FreeRTOS User Guide* for details\.

### Enable the test<a name="enabling-test"></a>

Open `test_execution_config.h` and define **TRANSPORT\_INTERFACE\_TEST\_ENABLED** to 1\.

### Set up the echo server for testing<a name="configuring-echo-server-test"></a>

An echo server accessible from the device running the tests is required for local testing\. The echo server must support TLS if the transport interface implementation supports TLS\. If you don’t have one already, [ FreeRTOS\-Libraries\-Integration\-Tests](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests/tree/main/tools/echo_server) GitHub repository has an echo server implementation\.

### Configuring the project for testing<a name="configure-project"></a>

In `test_param_config.h`, update **ECHO\_SERVER\_ENDPOINT** and **ECHO\_SERVER\_PORT** to the endpoint and server setup in the previous step\.

### Setup credentials \(on\-device generated key\)<a name="setup-credentials-device-key"></a>
+ Set **ECHO\_SERVER\_ROOT\_CA** to the server certificate of the echo server\.
+ Set **FORCE\_GENERATE\_NEW\_KEY\_PAIR** to 1 to generate a key pair and get the public key\.
+ Set **FORCE\_GENERATE\_NEW\_KEY\_PAIR** back to 0 after key generation\.
+ User the public key and server key and certificate to generate client certificate\.
+ Set **TRANSPORT\_CLIENT\_CERTIFICATE** to the generated client certificate\.

### Setup credentials \(importing key\)<a name="setup-credentials-import-key"></a>
+ Set **ECHO\_SERVER\_ROOT\_CA** to the server certificate of the echo server\.
+ Set **TRANSPORT\_CLIENT\_CERTIFICATE** to the pre\-generated client certificate\.
+ Set **TRANSPORT\_CLIENT\_PRIVATE\_KEY** to the pre\-generated client private key\.

### Build and flash the application<a name="build-flash"></a>

Build and flash the application using the tool\-chain of your choice\. When `runQualificationTest()` is invoked, the transport interface tests will run\. Test results are outputted to the serial port\.

**Note**  
To officially qualify a device for FreeRTOS, you must validate the device's ported source code against OTA PAL and OTA E2E test groups with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the *FreeRTOS User Guide* to set up AWS IoT Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the AWS IoT Device Tester `configs` folder\.