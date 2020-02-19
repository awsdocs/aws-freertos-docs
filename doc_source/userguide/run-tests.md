# Running the FreeRTOS Qualification Suite<a name="run-tests"></a>

You use the IDT for FreeRTOS executable to interact with IDT for FreeRTOS\. The following command line shows you how to run the qualification tests for a device pool \(a set of identical devices\)\.

devicetester\_*\[linux \| mac \| win\]* run\-suite \-\-suite\-id *<suite\-id>* \-\-pool\-id *<your\-device\-pool>* \-\-userdata *<userdata\.json>*

The `userdata.json` file should be located in the `<devicetester_extract_location> /devicetester_afreertos_[win|mac|linux]/configs/` directory\.

**Note**  
If you are running IDT for FreeRTOS on Windows, use forward slashes \(/\) to specify the path to the `userdata.json` file\.

Use the following command to run a specific test group:

devicetester\_*\[linux \| mac \| win\]* run\-suite \-\-suite\-id AFQ\_1 \-\-group\-id *<group\-id>* \-\-pool\-id *<pool\-id>* \-\-userdata *<userdata\.json>*

`suite-id` and `pool-id` are optional if you are running a single test suite on a single device pool \(that is, you have only one device pool defined in your `device.json` file\)\.IDT for FreeRTOS command line options

suite\-id  
Optional\. Specifies the test suite to run\.

pool\-id  
Specifies the device pool to test\. If you only have one device pool, you can omit this option\.

## IDT for FreeRTOS Commands<a name="dt-cli"></a>

The IDT for FreeRTOS command supports the following operations:

help  
Lists information about the specified command\.

list\-groups  
Lists the groups in a given suite\.

list\-suites  
Lists the available suites\.

run\-suite  
Runs a suite of tests on a pool of devices\.