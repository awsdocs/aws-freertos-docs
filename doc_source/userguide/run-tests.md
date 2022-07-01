# Running the FreeRTOS qualification suite<a name="run-tests"></a>

You use the AWS IoT Device Tester for FreeRTOS executable to interact with IDT for FreeRTOS\. The following command line examples show you how to run the qualification tests for a device pool \(a set of identical devices\)\.

------
#### [ IDT v3\.0\.0 and later ]

```
devicetester_[linux | mac | win] run-suite  \
    --suite-id suite-id  \
    --group-id group-id  \
    --pool-id your-device-pool \
    --test-id test-id  \
    --upgrade-test-suite y|n  \
    --update-idt y|n  \
    --update-managed-policy y|n  \
    --userdata userdata.json
```

Runs a suite of tests on a pool of devices\. The `userdata.json` file must be located in the `devicetester_extract_location/devicetester_afreertos_[win|mac|linux]/configs/` directory\.

**Note**  
If you're running IDT for FreeRTOS on Windows, use forward slashes \(/\) to specify the path to the `userdata.json` file\.

Use the following command to run a specific test group:

```
devicetester_[linux | mac | win] run-suite  \
    --suite-id FRQ_1.0.1  \
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
Starting in IDT v3\.0\.0, IDT checks online for newer test suites\. For more information, see [Test suite versions](idt-test-suite-versions.md)\.

**test\-id**  
\(Optional\) The tests to run, as a comma\-separated list\. If specified, `group-id` must specify a single group\.  

**Example**  

```
devicetester_[linux | mac | win_x86-64] run-suite --group-id mqtt --test-id mqtt_test
```

**update\-idt**  
\(Optional\) If this parameter is not set and a newer IDT version is available, you will be prompted to update IDT\. If this parameter is set to `Y`, IDT will stop test execution if it detects that a newer version is available\. If this parameter is set to `N`, IDT will continue the test execution\.

**update\-managed\-policy**  
\(Optional\) If this parameter is not used and IDT detects that your managed policy isn't up\-to\-date, you will be prompted to update your managed policy\. If this parameter is set to `Y`, IDT will stop test execution if it detects that your managed policy isn't up\-to\-date\. If this parameter is set to `N`, IDT will continue the test execution\.

**upgrade\-test\-suite**  
\(Optional\) If not used, and a newer test suite version is available, you're prompted to download it\. To hide the prompt, specify `y` to always download the latest test suite, or `n` to use the test suite specified or the latest version on your system\.  

**Example**  
To always download and use the latest test suite, use the following command\.  

```
devicetester_[linux | mac | win_x86-64] run-suite --userdata userdata file --group-id group ID --upgrade-test-suite y
```
To use the latest test suite on your system, use the following command\.  

```
devicetester_[linux | mac | win_x86-64] run-suite --userdata userdata file --group-id group ID --upgrade-test-suite n
```

**h**  
Use the help option to learn more about `run-suite` options\.  

**Example**  

```
devicetester_[linux | mac | win_x86-64] run-suite -h
```

------
#### [ IDT v1\.7\.0 and earlier ]

```
devicetester_[linux | mac | win] run-suite  \
    --suite-id suite-id  \
    --pool-id your-device-pool  \
    --userdata userdata.json
```

The `userdata.json` file should be located in the `devicetester_extract_location/devicetester_afreertos_[win|mac|linux]/configs/` directory\.

**Note**  
If you are running IDT for FreeRTOS on Windows, use forward slashes \(/\) to specify the path to the `userdata.json` file\.

Use the following command to run a specific test group\.

```
devicetester_[linux | mac | win] run-suite  \
    --suite-id FRQ_1 --group-id group-id  \
    --pool-id pool-id  \
    --userdata userdata.json
```

`suite-id` and `pool-id` are optional if you are running a single test suite on a single device pool \(that is, you have only one device pool defined in your `device.json` file\)\.IDT for FreeRTOS command line options

**group\-id**  
\(Optional\) Specifies the test group\.

**pool\-id**  
Specifies the device pool to test\. If you only have one device pool, you can omit this option\.

**suite\-id**  
\(Optional\) Specifies the test suite to run\.

------

## IDT for FreeRTOS commands<a name="dt-cli-frq"></a>

The IDT for FreeRTOS command supports the following operations:

------
#### [ IDT v3\.0\.0 and later ]

**`help`**  
Lists information about the specified command\.

**`list-groups`**  
Lists the groups in a given suite\.

**`list-suites`**  
Lists the available suites\.

**`list-supported-products`**  
Lists the supported products and test suite versions\.

**`list-supported-versions`**  
Lists the FreeRTOS and test suite versions supported by the current IDT version\.

**`list-test-cases`**  
Lists the test cases in a specified group\.

**`run-suite`**  
Runs a suite of tests on a pool of devices\.  
Use the `--suite-id` option to specify a test suite version, or omit it to use the latest version on your system\.  
Use the `--test-id` to run an individual test case\.  

**Example**  

```
devicetester_[linux | mac | win_x86-64] run-suite --group-id mqtt --test-id mqtt_test
```
For a complete list of options see [Running the FreeRTOS qualification suite](#run-tests)\.   
Starting in IDT v3\.0\.0, IDT checks online for newer test suites\. For more information, see [Test suite versions](idt-test-suite-versions.md)\.

------
#### [ IDT v1\.7\.0 and earlier ]

**`help`**  
Lists information about the specified command\.

**`list-groups`**  
Lists the groups in a given suite\.

**`list-suites`**  
Lists the available suites\.

**`run-suite`**  
Runs a suite of tests on a pool of devices\.

------