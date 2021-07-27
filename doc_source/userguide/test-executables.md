# Create IDT test case executables<a name="test-executables"></a>

You can create and place test case executables in a test suite folder in the following ways:
+ For test suites that use arguments or environment variables from the `test.json` files to determine which tests to run, you can create a single test case executable for the entire test suite, or a test executable for each test group in the test suite\.
+ For a test suite where you want to run specific tests based on specified commands, you create one test case executable for each test case in the test suite\.

As a test writer, you can determine which approach is appropriate for your use case and structure your test case executable accordingly\. Make sure that your provide the correct test case executable path in each `test.json` file, and that the specified executable runs correctly\. 

When all devices are ready for a test case to run, IDT reads the following files:
+ The `test.json` for the selected test case determines the processes to start and the environment variables to set\.
+ The `suite.json` for the test suite determines the environment variables to set\. 

IDT starts the required test executable process based on the commands and arguments specified in the `test.json` file, and passes the required environment variables to the process\. 

## Use the IDT Client SDK<a name="idt-client-sdk"></a>

The IDT Client SDKs let you simplify how you write test logic in your test executable with API commands that you can use interact with IDT and your devices under test\. IDT currently provides the following SDKs: 
+ IDT Client SDK for Python
+ IDT Client SDK for Go

These SDKs are located in the `<device-tester-extract-location>/sdks` folder\. When you create a new test case executable, you must copy the SDK that you want to use to the folder that contains your test case executable and reference the SDK in your code\. This section provides a brief description of the available API commands that you can use in your test case executables\. 

**Topics**
+ [Device interaction](#api-device-interaction)
+ [IDT interaction](#api-idt-interaction)
+ [Host interaction](#api-host-interaction)

### Device interaction<a name="api-device-interaction"></a>

The following commands enable you to communicate with the device under test without having to implement any additional device interaction and connectivity management functions\.

**`ExecuteOnDevice`**  
Allows test suites to run shell commands on a device that support SSH or Docker shell connections\.

**`CopyToDevice`**  
Allows test suites to copy a local file from the host machine that runs IDT to a specified location on a device that supports SSH or Docker shell connections\.

**`ReadFromDevice`**  
Allows test suites to read from the serial port of devices that support UART connections\.

**Note**  
Because IDT does not manage direct connections to devices that are made using device access information from the context, we recommend using these device interaction API commands in your test case executables\. However, if these commands do not meet your test case requirements, then you can retrieve device access information from the IDT context and use it to make a direct connection to the device from the test suite\.   
To make a direct connection, retrieve the information in the `device.connectivity` and the `resource.devices.connectivity` fields for your device under test and for resource devices, respectively\. For more information about using the IDT context, see [Use the IDT context](idt-context.md)\. 

### IDT interaction<a name="api-idt-interaction"></a>

The following commands enable your test suites to communicate with IDT\.

**`PollForNotifications`**  
Allows test suites to check for notifications from IDT\.

**`GetContextValue ` and `GetContextString`**  
Allows test suites to retrieve values from the IDT context\. For more information, see [Use the IDT context](idt-context.md)\.

**`SendResult`**  
Allows test suites to report test case results to IDT\. This command must be called at the end of each test case in a test suite\.

### Host interaction<a name="api-host-interaction"></a>

The following command enable your test suites to communicate with the host machine\.

**`PollForNotifications`**  
Allows test suites to check for notifications from IDT\.

**`GetContextValue ` and `GetContextString`**  
Allows test suites to retrieve values from the IDT context\. For more information, see [Use the IDT context](idt-context.md)\.

**`ExecuteOnHost`**  
Allows test suites to run commands on the local machine and lets IDT manage the test case executable lifecycle\.

## Enable IDT CLI commands<a name="idt-cli-coop"></a>

The `run-suite` command IDT CLI provides several options that let test runner customize test execution\. To allow test runners to use these options to run your custom test suite, you implement support for the IDT CLI\. If you do not implement support, test runners will still be able to run tests, but some CLI options will not function correctly\. To provide an ideal customer experience, we recommend that you implement support for the following arguments for the `run-suite` command in the IDT CLI:

**`timeout-multiplier`**  
Specifies a value greater than 1\.0 that will be applied to all timeouts while running tests\.   
Test runners can use this argument to increase the timeout for the test cases that they want to run\. When a test runner specifies this argument in their `run-suite` command, IDT uses it to calculate the value of the IDT\_TEST\_TIMEOUT environment variable and sets the `config.timeoutMultiplier` field in the IDT context\. To support this argument, you must do the following:  
+ Instead of directly using the timeout value from the `test.json` file, read the IDT\_TEST\_TIMEOUT environment variable to obtain the correctly calculated timeout value\.
+ Retrieve the `config.timeoutMultiplier` value from the IDT context and apply it to long running timeouts\.
For more information about exiting early because of timeout events, see [Specify exit behavior](#test-exec-exiting)\.

**`stop-on-first-failure`**  
Specifies that IDT should stop running all tests if it encounters a failure\.   
When a test runner specifies this argument in their `run-suite` command, IDT will stop running tests as soon as it encounters a failure\. However, if test cases are running in parallel, then this can lead to unexpected results\. To implement support, make sure that if IDT encounters this event, your test logic instructs all running test cases to stop, clean up temporary resources, and report a test result to IDT\. For more information about exiting early on failures, see [Specify exit behavior](#test-exec-exiting)\.

**`group-id` and `test-id`**  
Specifies that IDT should run only the selected test groups or test cases\.   
Test runners can use these arguments with their `run-suite` command to specify the following test execution behavior:   
+ Run all tests inside the specified test groups\.
+ Run a selection of tests from within a specified test group\.
To support these arguments, the state machine for your test suite must include a specific set of `RunTask` and `Choice` states in your state machine\. If you are not using a custom state machine, then the default IDT state machine includes the required states for you and you do not need to take additional action\. However, if you are using a custom state machine, then use [Example state machine: Run user\-selected test groups](idt-state-machine.md#allow-specific-groups) as a sample to add the required states in your state machine\.

For more information about IDT CLI commands, see [Debug and run custom test suites](run-tests-custom.md)\.

## Write event logs<a name="test-exec-logs"></a>

While the test is running, you send data to `stdout` and `stderr` to write event logs and error messages to the console\. For information about the format of console messages, see [Console message format](idt-review-results-logs.md#idt-console-format)\.

When the IDT finishes running the test suite, this information is also available in the `test_manager.log` file located in the `<devicetester-extract-location>/results/<execution-id>/logs` folder\.

You can configure each test case to write the logs from its test run, including logs from the device under test, to the `<group-id>_<test-id>` file located in the `<device-tester-extract-location>/results/execution-id/logs` folder\. To do this, retrieve the path to the log file from the IDT context with the `testData.logFilePath` query, create a file at that path, and write the content that you want to it\. IDT automatically updates the path based on the test case that is running\. If you choose not to create the log file for a test case, then no file is generated for that test case\.

You can also set up your text executable to create additional log files as needed in the `<device-tester-extract-location>/logs` folder\. We recommend that you specify unique prefixes for log file names so your files don't get overwritten\.

## Report results to IDT<a name="test-exec-results"></a>

IDT writes test results to the `awsiotdevicetester_report.xml` and the `suite-name_report.xml` files\. These report files are located in `<device-tester-extract-location>/results/<execution-id>/`\. Both reports capture the results from the test suite execution\. For more information about the schemas that IDT uses for these reports, see [Review IDT test results and logs](idt-review-results-logs.md)

To populate the contents of the `suite-name_report.xml` file, you must use the `SendResult` command to report test results to IDT before the test execution finishes\. If IDT cannot locate the results of a test, it issues an error for the test case\. The following Python excerpt shows the commands to send a test result to IDT:

```
request-variable = SendResultRequest(TestResult(result))
client.send_result(request-variable)
```

If you do not report results through the API, IDT looks for test results in the test artifacts folder\. The path to this folder is stored in the `testData.testArtifactsPath` filed in the IDT context\. In this folder, IDT uses the first alphabetically sorted XML file it locates as the test result\. 

If your test logic produces JUnit XML results, you can write the test results to an XML file in the artifacts folder to directly provide the results to IDT instead of parsing the results and then using the API to submit them to IDT\. 

If you use this method, make sure that your test logic accurately summarizes the test results and format your result file in the same format as the `suite-name_report.xml` file\. IDT does not perform any validation of the data that you provide, with the following exceptions:
+ IDT ignores all properties of the `testsuites` tag\. Instead, it calculates the tag properties from other reported test group results\.
+ At least one `testsuite` tag must exist within `testsuites`\.

Because IDT uses the same artifacts folder for all test cases and does not delete result files between test runs, this method might also lead to erroneous reporting if IDT reads the incorrect file\. We recommend that you use the same name for the generated XML results file across all test cases to overwrite the results for each test case and make sure that the correct results are available for IDT to use\. Although you can use a mixed approach to reporting in your test suite, that is, use an XML result file for some test cases and submit results through the API for others, we do not recommend this approach\.

## Specify exit behavior<a name="test-exec-exiting"></a>

Configure your text executable to always exit with an exit code of 0, even if a test case reports a failure or an error result\. Use non\-zero exit codes only to indicate that a test case did not run or if the test case executable could not communicate any results to IDT\. When IDT receives a non\-zero exit code, it marks the test case has having encountered an error that prevented it from running\.

IDT might request or expect a test case to stop running before it has finished in the following events\. Use this information to configure your test case executable to detect each of these events from the test case:

****Timeout****  
Occurs when a test case runs for longer than the timeout value specified in the `test.json` file\. If the test runner used the `timeout-multiplier` argument to specify a timeout multiplier, then IDT calculates the timeout value with the multiplier\.   
To detect this event, use the IDT\_TEST\_TIMEOUT environment variable\. When a test runner launches a test, IDT sets the value of the IDT\_TEST\_TIMEOUT environment variable to the calculated timeout value \(in seconds\) and passes the variable to the test case executable\. You can read the variable value to set an appropriate timer\.

****Interrupt****  
Occurs when the test runner interrupts IDT\. For example, by pressing Ctrl\+C\.  
Because terminals propagate signals to all child processes, you can simply configure a signal handler in your test cases to detect interrupt signals\.   
Alternatively, you can periodically poll the API to check the value of the `CancellationRequested` boolean in the `PollForNotifications` API response\. When IDT receives an interrupt signal, it sets the value of the `CancellationRequested` boolean to `true`\.

****Stop on first failure****  
Occurs when a test case that is running in parallel with the current test case fails and the test runner used the `stop-on-first-failure` argument to specify that IDT should stop when it encounters any failure\.  
To detect this event, you can periodically poll the API to check the value of the `CancellationRequested` boolean in the `PollForNotifications` API response\. When IDT encounters a failure and is configured to stop on first failure, it sets the value of the `CancellationRequested` boolean to `true`\.

When any of these events occur, IDT waits for 5 minutes for any currently running test cases to finish running\. If all running test cases do not exit within 5 minutes, IDT forces each of their processes to stop\. If IDT has not received test results before the processes end, it will mark the test cases as having timed out\. As a best practice, you should ensure that your test cases perform the following actions when they encounter one of the events:

1. Stop running normal test logic\.

1. Clean up any temporary resources, such as test artifacts on the device under test\.

1. Report a test result to IDT, such as a test failure or an error\. 

1. Exit\.