# Running the Amazon FreeRTOS Qualification Suite<a name="run-tests"></a>

You use the AWS IoT Device Tester executable to interact with AWS IoT Device Tester\. The following command line shows you how to run the qualification tests for a device pool \(set of identical devices\)\.

devicetester\_*\[linux \| mac \| win\_x86\-64\]* run\-suite \-\-suite\-id *<suite\-id>* \-\-pool\-id *<your\-device\-pool>* \-\-userdata *<userdata\.json>*

The `userdata.json` file should be located in the `<devicetester_extract_location> /devicetester_afreertos_[win|mac|linux]/configs/` directory\.

**Note**  
If you are running AWS IoT Device Tester on Windows, specify the path to the `userdata.json` by using forward slashes \(/\)\.

Use the following command to run a specific test group:

devicetester\_*\[linux \| mac \| win\_x86\-64\]* run\-suite \-\-suite\-id AFQ\_1 \-\-group\-id *<group\-id>* \-\-pool\-id *<pool\-id>* \-\-userdata *<userdata\.json>*

`suite-id` and `pool-id` are optional if you are running a single test suite on a single device pool \(that is, you have only one device pool defined in your `device.json` file\)\.AWS IoT Device Tester command line options

suite\-id  
Optional\. Specifies the test suite to run\.

pool\-id  
Specifies the device pool to test\. If you only have one device pool, you can omit this option\.

## AWS IoT Device Tester Commands<a name="dt-cli"></a>

The AWS IoT Device Tester command supports the following operations:

help  
Lists information about the specified command\.

list\-groups  
Lists the groups in a given suite\.

list\-suites  
Lists the available suites\.

run\-suite  
Runs a suite of tests on a pool of devices\.