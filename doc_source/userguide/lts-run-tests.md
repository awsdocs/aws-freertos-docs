# Running the FreeRTOS qualification 2\.0 suite<a name="lts-run-tests"></a>

Use the AWS IoT Device Tester for FreeRTOS executable to interact with IDT for FreeRTOS\. The following command line examples show you how to run the qualification tests for a device pool \(a set of identical devices\)\.

------
#### [ IDT v4\.5\.2 and later ]

```
devicetester_[linux | mac | win] run-suite  \
    --suite-id suite-id  \
    --group-id group-id  \
    --pool-id your-device-pool \
    --test-id test-id  \
    --userdata userdata.json
```

Runs a suite of tests on a pool of devices\. The `userdata.json` file must be located in the `devicetester_extract_location/devicetester_freertos_[win|mac|linux]/configs/` directory\.

**Note**  
If you're running IDT for FreeRTOS on Windows, use forward slashes \(/\) to specify the path to the `userdata.json` file\.

Use the following command to run a specific test group:

```
devicetester_[linux | mac | win] run-suite  \
    --suite-id FRQ_1.99.0  \
    --group-id group-id  \
    --pool-id pool-id  \
    --userdata userdata.json
```

The `suite-id` and `pool-id` parameters are optional if you're running a single test suite on a single device pool \(that is, you have only one device pool defined in your `device.json` file\)\.

Use the following command to run a specific test case in a test group:

```
devicetester_[linux | mac | win_x86-64] run-suite  \
    --group-id group-id  \
    --test-id test-id
```

You can use the `list-test-cases` command to list the test cases in a test group\. IDT for FreeRTOS command line options

**group\-id**  
\(Optional\) The test groups to run, as a comma\-separated list\. If not specified, IDT runs all test groups in the test suite\.

**pool\-id**  
\(Optional\) The device pool to test\. This is required if you define multiple device pools in `device.json`\. If you only have one device pool, you can omit this option\.

**suite\-id**  
\(Optional\) The test suite version to run\. If not specified, IDT uses the latest version in the tests directory on your system\.

**test\-id**  
\(Optional\) The tests to run, as a comma\-separated list\. If specified, `group-id` must specify a single group\.  

**Example**  

```
devicetester_[linux | mac | win_x86-64] run-suite --group-id FreeRTOSVersion --test-id FreeRTOSVersion
```

**h**  
Use the help option to learn more about `run-suite` options\.  

**Example**  

```
devicetester_[linux | mac | win_x86-64] run-suite -h
```

------

## IDT for FreeRTOS commands<a name="lts-dt-cli"></a>

The IDT for FreeRTOS command supports the following operations:

------
#### [ IDT v4\.5\.2 and later ]

** `help` **  
Lists information about the specified command\.

** `list-groups` **  
Lists the groups in a given suite\.

** `list-suites` **  
Lists the available suites\.

** `list-supported-products` **  
Lists the supported products and test suite versions\.

** `list-supported-versions` **  
Lists the FreeRTOS and test suite versions supported by the current IDT version\.

** `list-test-cases` **  
Lists the test cases in a specified group\.

** `run-suite` **  
Runs a suite of tests on a pool of devices\.  
Use the `--suite-id` option to specify a test suite version, or omit it to use the latest version on your system\.  
Use the `--test-id` to run an individual test case\.  

**Example**  

```
devicetester_[linux | mac | win_x86-64] run-suite --group-id FreeRTOSVersion --test-id FreeRTOSVersion
```
Starting in IDT v3\.0\.0, IDT checks online for newer test suites\. For more information, see [Test suite versions](idt-test-suite-versions.md)\.

------