# Troubleshooting<a name="dt-afr-troublshooting"></a>

We recommend the following workflow for troubleshooting testing an Amazon FreeRTOS device:

1. Read the console output\.

1. Look in the `AFQ_Report.xml` file\.

1. Look in the logs files located under `/results/<uuid>/logs`\.

1. Investigate one of the following problem areas:
   + Device configuration
   + Device interface
   + Device tooling
   + Amazon FreeRTOS source code

## Troubleshooting Device Configuration<a name="troubleshoot-device-config"></a>

When you use AWS IoT Device Tester, you must get the correct configuration files in place before you execute the binary\. If you are getting parsing and configuration errors, your first step should be to locate and use a configuration template appropriate for your environment\.

If you are still having issues, see the debugging following process\.

### Where Do I Look?<a name="where-to-look"></a>

Start by looking in the `AFQ_Report.xml` file in the `/results/<uuid>` directory\. This file contains all of the test cases that were run and error snippets for each failure\. To get all of the execution logs, look under `/results/<uuid>/<test-case-id>.log` for each test group\.

### Parsing Errors<a name="parse-error"></a>

Occasionally, a typo in a JSON configuration can lead to parsing errors\. Most of the time, the issue is a result of omitting a bracket, comma, or quote from your JSON file\. AWS IoT Device Tester performs JSON validation and prints debugging information\. It prints the line where the error occurred, the line number, and the column number of the syntax error\. This information should be enough to help you fix the error, but if you are still having issues locating the error, you can perform validation manually in your IDE, a text editor such as Atom or Sublime, or through an online tool like JSONLint\.

### Required Parameter Missing Error<a name="param-missing"></a>

Because new features are being added to AWS IoT Device Tester, changes to the configuration files might be introduced\. Using an old configuration file might break your configuration\. If this happens, the `<test_case_id>.log` file under `/results/<uuid>/logs` explicitly lists all missing parameters\. AWS IoT Device Tester validates your JSON configuration file schemas to ensure that the latest supported version has been used\.

### Could Not Start Test Error<a name="could-not-start-test"></a>

You might encounter errors that point to failures during test start\. Since there are several possible causes for this, please make sure to check the following areas for correctness:
+ Make sure that the pool name you've included in your execution command actually exists\. This is referenced directly from your device\.json file\.
+ Ensure that the device\(s\) in your pool have correct configuration parameters\.

### Device Interface and Port<a name="device-interface"></a>

This section contains information about the device interfaces AWS IoT Device Tester uses to connect to your devices\.

#### Supported Platforms<a name="platform-differences"></a>

AWS IoT Device Tester supports Linux, macOS, and Windows\. All three platforms have different naming schemes for serial devices that are attached to them:
+ Linux: /dev/tty\*
+ macOS: /dev/tty\.\*
+ Windows: COM\*

To check your device ID:
+ For Linux/macOS, open a terminal and run ls /dev/tty\*\.
+ For Windows, open Device Manager and expand the serial devices group\.

#### Device Interfaces<a name="device-interfaces"></a>

Each embedded device is different, which means that they can have one or more serial ports\. It is common for devices to have two ports when connected to a machine, one being a data port for flashing the device and the other to read output\. It is crucial to set the correct port in your `device.json` file\. Otherwise, flashing or reading output might fail\.

In the case of multiple ports, make sure to use the data port of the device in your `device.json` file\. For example, if you plug in an Espressif WRover device and the two ports assigned to it are /dev/ttyUSB0 and /dev/ttyUSB1, Use `/dev/ttyUSB1` in your `device.json` file\.

For Windows, follow the same logic\.

#### Reading Device Data<a name="reading-device-data"></a>

AWS IoT Device Tester uses individual device build and flash tooling to specify port configuration\. If you are testing your device and don't get output, try the following default settings:
+ Baud rate \- 115200
+ Data Bits \- 8
+ Parity \- None
+ Stop Bits \- 1
+ Flow Control \- None

These settings are handled by AWS IoT Device Tester without any configuration on your end\. However, you can use the same method to manually read device output\. On on Linux or macOS, you can do this with the screen command\. On Windows, you can use a program such as TeraTerm\.

`Screen: screen /dev/cu.usbserial 115200`

`TeraTerm: Use the above-provided settings to set the fields explicitly in the GUI.`

### Development Toolchain Problems<a name="dev-toolchain"></a>

This section discusses problems that can occur with your toolchain\.

#### Code Composer Studio on Ubuntu<a name="ccs-ubuntu"></a>

For TI devices, we recommend that you download and install Code Composer Studio 7\.3\. The newer versions of Ubuntu \(17\.10 and 18\.04\) have a version of the glibc package that is not compatible with Code Composer Studio 7\.*x* versions\. We recommended that you install Code Composer Studio version 8\.2 or later\.

Symptoms of incompatibility might include:
+ Amazon FreeRTOS failing to build or flash to your device\.
+ The Code Composer Studio installer might freeze\.
+ No log output is displayed in the console during the build or flash process\.
+ Build command attempting to launch in GUI mode even when invoked as headless\.

### Amazon FreeRTOS Source Code<a name="afr-src"></a>

The following sections discuss troubleshooting problems with the Amazon FreeRTOS source code\.

#### Code Errata<a name="code-err"></a>

Every Amazon FreeRTOS release is bundled with a document, located under the `/amazon-freertos/tests` directory, that contains all of the errata information for that release\. We recommend that you read through this document before you run any tests\.

The errata document contains an entry for any devices that currently fail tests due to reasons like:
+ The hardware doesn't support a specific feature\.
+ The hardware supports the feature, but Amazon FreeRTOS doesn't support it on the device yet\.
+ The hardware supports the feature, but the underlying software stack doesn't support the hardware \(non\-AFR\)\.

If the errata does not contain information specific to your device, continue the debugging process as outlined in the next section\.

#### Debugging Amazon FreeRTOS<a name="afr-debug"></a>

When a source code error occurs, AWS IoT Device Tester will write debug output to the *<test\-group\-id>*\.log file in the `/results/<uuid>/logs` directory\. Search the file for any instances of errors\. The error will point to a location in the Amazon FreeRTOS source code\. You can then use the line number and file path information in that log to reference the piece of source code that resulted in the error\.

### Logging<a name="dt-logging"></a>

AWS IoT Device Tester logs are placed in a single location\. From the root AWS IoT Device Tester directory, the available files are:
+ `./results/<uuid>`
+ `AFQ_Report.xml`
+ `awsiotdevicetester_report.xml`
+ `/logs/<test_group_id>.log`

The most important logs to look at will be *<test\_group\_id>*\.log and results\.xml\. The latter will contain information about which tests failed with a specific error message\. You can then use the former to dig further into the problem in order to get better context\.

#### Console Errors<a name="err-console"></a>

When AWS IoT Device Tester is run, failures are reported to console with brief messages\. Look in *<test\_group\_id>*\.log to learn more about the error\.

#### Log Errors<a name="err-log"></a>

The *<test\-group\-id>*\.log file is located in the `/results/<uuid>` directory\. Each test execution has a unique test ID that is used to create the *<uuid>* directory\. Individual test group logs are under the *<uuid>* directory\. Use the AWS IoT console to look up the test group that failed and then open the log file for that group in the `/results/<uuid>` directory\. The information in this file includes the full build and flash command output, as well as test execution output and more verbose AWS IoT Device Tester console output\.