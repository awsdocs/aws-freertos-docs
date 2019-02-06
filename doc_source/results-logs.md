# Results and Logs<a name="results-logs"></a>

This section describes how to view and interpret test results and logs\.

## Viewing Results<a name="view-results"></a>

After AWS IoT Device Tester executes the qualification test suite, it generates two reports for each run of the qualification test suite: `awsiotdevicetester_report.xml` and `AFQ_Report.xml`\. These reports can be found in *<devicetester\-extract\-location>*/results/*<execution\-id>*/\. 

`awsiotdevicetester_report.xml` is the qualification test report that you submit to AWS for listing your device in the AWS Partner Device Catalog\. The report contains the following elements:
+ The AWS IoT Device Tester version\.
+ The Amazon FreeRTOS version that was tested\.
+ The SKU and the device name specified in the `device.json` file\.
+ The features of the device specified in the `device.json` file\.
+ The aggregate summary of test case results\.
+ A breakdown of test case results by libraries that were tested based on the device features \(for example, FullWiFi, FullMQTT, and so on\)\.

 The `AFQ_Report.xml` is a report in standard junit\.xml format, which you can integrate into your exiting CI/CD platforms like Jenkins, Bamboo, and so on\. The report contains the following elements:
+ An aggregate summary of test case results\.
+ A breakdown of test case results by libraries that were tested based on the device features \(for example, FullWiFi, FullMQTT, and so on\)\.

### Interpreting AWS IoT Device Tester Results<a name="interpreting-results"></a>

The report section in `awsiotdevicetester_report.xml` or `AFQ_Report.xml` lists the tests that were run and the results of the tests\.

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

### Viewing Logs<a name="view-logs"></a>

You can find logs that AWS IoT Device Tester generates from test execution in `<devicetester-extract-location>/results/<execution-id>/logs`\. Two sets of logs are generated:

`test_manager.log`  
Contains logs generated from the Test Manager component of AWS IoT Device Tester\. For example, logs related configuration, test sequencing, and report generation are here\.

`<test_group_name>.log (for example, Full_MQTT.log)`  
The logs of the test group, including logs from the device under test\.