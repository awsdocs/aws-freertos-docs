# Understanding Results and Logs<a name="results-logs"></a>

This section describes how to view and interpret IDT result reports and logs\. 

## Viewing Results<a name="view-results"></a>

While running, IDT writes errors to the console, log files, and test reports\. After IDT completes the qualification test suite, it writes a test run summary to the console and generates two test reports\. These reports can be found in `devicetester-extract-location/results/execution-id/`\. Both reports capture the results from the qualification test suite execution\.

The `awsiotdevicetester_report.xml` is the qualification test report that you submit to AWS to list your device in the AWS Partner Device Catalog\. The report contains the following elements:
+ The IDT for FreeRTOS version\.
+ The FreeRTOS version that was tested\.
+ The features of FreeRTOS that are supported by the device based on the tests passed\.
+ The SKU and the device name specified in the `device.json` file\.
+ The features of the device specified in the `device.json` file\.
+ The aggregate summary of test case results\.
+ A breakdown of test case results by libraries that were tested based on the device features \(for example, FullWiFi, FullMQTT, and so on\)\.

The `FRQ_Report.xml` is a report in standard [JUnit XML format](https://llg.cubic.org/docs/junit/)\. You can integrate it into CI/CD platforms like [Jenkins](https://jenkins.io/), [Bamboo](https://www.atlassian.com/software/bamboo), and so on\. The report contains the following elements:
+ An aggregate summary of test case results\.
+ A breakdown of test case results by libraries that were tested based on the device features\.

### Interpreting IDT for FreeRTOS Results<a name="interpreting-results"></a>

The report section in `awsiotdevicetester_report.xml` or `FRQ_Report.xml` lists the results of the tests that are executed\.

The first XML tag `<testsuites>` contains the overall summary of the test execution\. For example:

`<testsuites name="FRQ results" time="5633" tests="184" failures="0" errors="0" disabled="0">`Attributes used in the `<testsuites>` tag

`name`  
The name of the test suite\.

`time`  
The time, in seconds, it took to run the qualification suite\.

`tests`  
The number of test cases executed\.

`failures`  
The number of test cases that were run, but did not pass\.

`errors`  
The number of test cases that IDT for FreeRTOS couldn't execute\.

`disabled`  
This attribute is not used and can be ignored\.

If there are no test case failures or errors, your device meets the technical requirements to run FreeRTOS and can interoperate with AWS IoT services\. If you choose to list your device in the AWS Partner Device Catalog, you can use this report as qualification evidence\.

In the event of test case failures or errors, you can identify the test case that failed by reviewing the `<testsuites>` XML tags\. The `<testsuite>` XML tags inside the `<testsuites>` tag shows the test case result summary for a test group\.

`<testsuite name="FullMQTT" package="" tests="16" failures="0" time="76" disabled="0" errors="0" skipped="0">`

The format is similar to the `<testsuites>` tag, but with an attribute called `skipped` that is not used and can be ignored\. Inside each `<testsuite>` XML tag, there are `<testcase>` tags for each of the test cases that were executed for a test group\. For example:

`<testcase classname="mcu.Full_MQTT" name="AFQP_MQTT_Connect_HappyCase" attempts="1"></testcase>`Attributes used in the `<testcase>` tag

`name`  
The name of the test case\.

`attempts`  
The number of times IDT for FreeRTOS executed the test case\.

When a test fails or an error occurs, `<failure>` or `<error>` tags are added to the `<testcase>` tag with information for troubleshooting\. For example:

`<testcase classname="mcu.Full_MQTT" name="AFQP_MQTT_Connect_HappyCase"> <failure type="Failure">Reason for the test case failure</failure> <error>Reason for the test case execution error</error> </testcase>`

For more information, see [Troubleshooting](dt-afr-troublshooting.md)\.

### Viewing Logs<a name="view-logs"></a>

You can find logs that IDT for FreeRTOS generates from test execution in `devicetester-extract-location/results/execution-id/logs`\. Two sets of logs are generated:

`test_manager.log`  
Contains logs generated from IDT for FreeRTOS \(for example, logs related configuration and report generation\)\.

`test_group_id__test_case_id.log` \(for example, `FullMQTT__Full_MQTT.log`\)  
The log file for a test case, including output from the device under test\. The log file is named according to the test group and test case that was run\.