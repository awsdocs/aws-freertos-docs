# Debug and run custom test suites<a name="run-tests-custom"></a>

After the [required configuration](set-config-custom.md) is set, IDT can run your test suite\. The runtime of the full test suite depends on the hardware and the composition of the test suite\. For reference, it takes approximately 30 minutes to complete the full FreeRTOS qualification test suite on a Raspberry Pi 3B\.

As you write your test suite, you can use IDT to run the test suite in debug mode to check your code before you run it or provide it to test runners\.

## Run IDT in debug mode<a name="idt-debug-mode"></a>

Because test suites depend on IDT to interact with devices, provide the context, and receive results, you cannot simply debug your test suites in an IDE without any IDT interaction\. To do so, the IDT CLI provides the `debug-test-suite` command that lets you run IDT in debug mode\. Run the following command to view the available options for `debug-test-suite`:

```
devicetester_[linux | mac | win_x86-64] debug-test-suite -h
```

When you run IDT in debug mode, IDT does not actually launch the test suite or run the state machine; instead, it interacts with your IDE to responds to requests made from the test suite running in the IDE and prints the logs to the console\. IDT does not time out and waits to exit until manually interrupted\. In debug mode, IDT also does not run the state machine and will not generate any report files\. To debug your test suite, you must use your IDE to provide some information that IDT usually obtains from the configuration JSON files\. Make sure you provide the following information:
+ Environment variables and arguments for each test\. IDT will not read this information from `test.json` or `suite.json`\.
+ Arguments to select resource devices\. IDT will not read this information from `test.json`\.

To debug your test suites, complete the following steps:

1.  Create the setting configuration files that are required to run the test suite\. For example, if your test suite requires the `device.json`, `resource.json`, and `user data.json`, make sure you configure all of them as needed\. 

1. Run the following command to place IDT in debug mode and select any devices that are required to run the test\.

   ```
   devicetester_[linux | mac | win_x86-64] debug-test-suite [options]
   ```

   After you run this command, IDT waits for requests from the test suite and then responds to them\. IDT also generates the environment variables that are required for the case process for the IDT Client SDK\. 

1. In your IDE, use the `run` or `debug` configuration to do the following:

   1. Set the values of the IDT\-generated environment variables\.

   1. Set the value of any environment variables or arguments that you specified in your `test.json` and `suite.json` file\.

   1. Set breakpoints as needed\.

1. Run the test suite in your IDE\. 

   You can debug and re\-run the test suite as many times as needed\. IDT does not time out in debug mode\.

1.  After you complete debugging, interrupt IDT to exit debug mode\.

## IDT CLI commands to run tests<a name="idt-cli-commands"></a>

The following section describes the IDT CLI commands:

------
#### [ IDT v4\.0\.0 ]

`help`  <a name="idt-command-help"></a>
Lists information about the specified command\.

`list-groups`  <a name="idt-command-list-groups"></a>
Lists the groups in a given test suite\.

`list-suites`  <a name="idt-command-list-suites"></a>
Lists the available test suites\.

`list-supported-products`  
Lists the supported products for your version of IDT, in this case FreeRTOS versions, and FreeRTOS qualification test suite versions available for the current IDT version\.

`list-test-cases`  
Lists the test cases in a given test group\. The following option is supported:  
+ `group-id`\. The test group to search for\. This option is required and must specify a single group\.

`run-suite`  
Runs a suite of tests on a pool of devices\. The following are some commonly used options:  
+ `suite-id`\. The test suite version to run\. If not specified, IDT uses the latest version in the `tests` folder\.
+ `group-id`\. The test groups to run, as a comma\-separated list\. If not specified, IDT runs all test groups in the test suite\.
+ `test-id`\. The test cases to run, as a comma\-separated list\. When specified, `group-id` must specify a single group\.
+ `pool-id`\. The device pool to test\. Test runners must specify a pool if they have multiple device pools defined in your `device.json` file\.
+ `timeout-multiplier`\. Configures IDT to modify the test execution timeout specified in the `test.json` file for a test with a user\-defined multiplier\.
+ `stop-on-first-failure`\. Configures IDT to stop execution on the first failure\. This option should be used with `group-id` to debug the specified test groups\.
+ `userdata`\. Sets the file that contains user data information required to run the test suite\. This is required only if `userdataRequired` is set to true in the `suite.json` file for the test suite\.
For more information about `run-suite` options, use the `help` option:  

```
devicetester_[linux | mac | win_x86-64] run-suite -h
```

`debug-test-suite`  
Run the test suite in debug mode\. For more information, see [Run IDT in debug mode](#idt-debug-mode)\.

------