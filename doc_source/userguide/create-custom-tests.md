# Tutorial: Develop a simple IDT test suite<a name="create-custom-tests"></a>

A test suite combines the following:
+ Test executables that contain the test logic
+ JSON configuration files that describe the test suite

This tutorial shows you how to use IDT for FreeRTOS to develop a Python test suite that contains a single test case\. In this tutorial, you will complete the following steps: 

1. [Create a test suite directory](#test-suite-dir)

1. [Create JSON configuration files](#test-suite-json)

1. [Create the test case executable](#test-suite-exe)

1. [Run the test suite](#run-test-suite)

## Prerequisites<a name="prereqs-tutorial-custom"></a>

To complete this tutorial, you need the following: 
+ 

**Host computer requirements**
  + Latest version of AWS IoT Device Tester
  + [Python](https://www.python.org/downloads/) 3\.7 or later

    To check the version of Python installed on your computer, run the following command:

    ```
    python3 --version
    ```

    On Windows, if using this command returns an error, then use `python --version` instead\. If the returned version number is 3\.7 or greater, then run the following command in a Powershell terminal to set `python3` as an alias for your `python` command\. 

    ```
    Set-Alias -Name "python3" -Value "python"
    ```

    If no version information is returned or if the version number is less than 3\.7, follow the instructions in [Downloading Python](https://wiki.python.org/moin/BeginnersGuide/Download) to install Python 3\.7\+\. For more information, see the [Python documentation](https://docs.python.org)\.
  + [urllib3](https://urllib3.readthedocs.io/en/latest/)

    To verify that `urllib3` is installed correctly, run the following command:

    ```
    python3 -c 'import urllib3'
    ```

    If `urllib3` is not installed, run the following command to install it:

    ```
    python3 -m pip install urllib3
    ```
+ 

**Device requirements**
  + A device with a Linux operating system and a network connection to the same network as your host computer\. 

    We recommend that you use a [Raspberry Pi](https://www.raspberrypi.org/) with Raspberry Pi OS\. Make sure you set up [SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/) on your Raspberry Pi to remotely connect to it\.

## Create a test suite directory<a name="test-suite-dir"></a>

IDT logically separates test cases into test groups within each test suite\. Each test case must be inside a test group\. For this tutorial, create a folder called `MyTestSuite_1.0.0` and create the following directory tree within this folder:

```
MyTestSuite_1.0.0
└── suite
    └── myTestGroup
        └── myTestCase
```

## Create JSON configuration files<a name="test-suite-json"></a>

Your test suite must contain the following required [JSON configuration files](idt-json-config.md):Required JSON files

`suite.json`  
Contains information about the test suite\. See [Configure suite\.json](idt-json-config.md#suite-json)\.

`group.json`  
Contains information about a test group\. You must create a `group.json` file for each test group in your test suite\. See [Configure group\.json](idt-json-config.md#group-json)\.

`test.json`  
Contains information about a test case\. You must create a `test.json` file for each test case in your test suite\. See [Configure test\.json](idt-json-config.md#test-json)\.

1. In the `MyTestSuite_1.0.0/suite` folder, create a `suite.json` file with the following structure:

   ```
   {
       "id": "MyTestSuite_1.0.0",
       "title": "My Test Suite",
       "details": "This is my test suite.",
       "userDataRequired": false
   }
   ```

1. In the `MyTestSuite_1.0.0/myTestGroup` folder, create a `group.json` file with the following structure:

   ```
   {
       "id": "MyTestGroup",
       "title": "My Test Group",
       "details": "This is my test group.",
       "optional": false
   }
   ```

1. In the `MyTestSuite_1.0.0/myTestGroup/myTestCase` folder, create a `test.json` file with the following structure:

   ```
   {
       "id": "MyTestCase",
       "title": "My Test Case",
       "details": "This is my test case.",
       "execution": {
           "timeout": 300000,
           "linux": {
               "cmd": "python3",
               "args": [
                   "myTestCase.py"
               ]
           },
           "mac": {
               "cmd": "python3",
               "args": [
                   "myTestCase.py"
               ]
           },
           "win": {
               "cmd": "python3",
               "args": [
                   "myTestCase.py"
               ]
           }
       }
   }
   ```

The directory tree for your `MyTestSuite_1.0.0` folder should now look like the following:

```
MyTestSuite_1.0.0
└── suite
    ├── suite.json
    └── myTestGroup
        ├── group.json
        └── myTestCase
            └── test.json
```

## Get the IDT client SDK<a name="add-idt-sdk"></a>

You use the [IDT client SDK](test-executables.md#idt-client-sdk) to enable IDT to interact with the device under test and to report test results\. For this tutorial, you will use the Python version of the SDK\. 

From the `<device-tester-extract-location>/sdks/python/` folder, copy the `idt_client` folder to your `MyTestSuite_1.0.0/suite/myTestGroup/myTestCase` folder\. 

To verify that the SDK was successfully copied, run the following command\.

```
cd MyTestSuite_1.0.0/suite/myTestGroup/myTestCase
python3 -c 'import idt_client'
```

## Create the test case executable<a name="test-suite-exe"></a>

Test case executables contain the test logic that you want to run\. A test suite can contain multiple test case executables\. For this tutorial, you will create only one test case executable\.

1. Create the test suite file\.

   In the `MyTestSuite_1.0.0/suite/myTestGroup/myTestCase` folder, create a `myTestCase.py` file with the following content:

   ```
   from idt_client import *
   
   def main():
       # Use the client SDK to communicate with IDT
       client = Client()
   
   if __name__ == "__main__":
       main()
   ```

1. Use client SDK functions to add the following test logic to your `myTestCase.py` file:

   1. Run an SSH command on the device under test\.

      ```
      from idt_client import *
      
      def main():
          # Use the client SDK to communicate with IDT
          client = Client()
          
          # Create an execute on device request
          exec_req = ExecuteOnDeviceRequest(ExecuteOnDeviceCommand("echo 'hello world'"))
          
          # Run the command
          exec_resp = client.execute_on_device(exec_req)
          
          # Print the standard output
          print(exec_resp.stdout)
      
      if __name__ == "__main__":
          main()
      ```

   1. Send the test result to IDT\.

      ```
      from idt_client import *
      
      def main():
          # Use the client SDK to communicate with IDT
          client = Client()
          
          # Create an execute on device request
          exec_req = ExecuteOnDeviceRequest(ExecuteOnDeviceCommand("echo 'hello world'"))
          
          # Run the command
          exec_resp = client.execute_on_device(exec_req)
          
          # Print the standard output
          print(exec_resp.stdout)
      
          # Create a send result request
          sr_req = SendResultRequest(TestResult(passed=True))
           
          # Send the result
          client.send_result(sr_req)
             
      if __name__ == "__main__":
          main()
      ```

## Configure device information for IDT<a name="configure-idt-sample2"></a>

Configure your device information for IDT to run the test\. You must update the `device.json` template located in the `<device-tester-extract-location>/configs` folder with the following information\.

```
[
  {
    "id": "pool",
    "sku": "N/A",
    "devices": [
      {
        "id": "<device-id>",
        "connectivity": {
          "protocol": "ssh",
          "ip": "<ip-address>",
          "port": "<port>",
          "auth": {
            "method": "pki | password",
            "credentials": {
              "user": "<user-name>",
              "privKeyPath": "/path/to/private/key",
              "password": "<password>"
            }
          }
        }
      }
    ]
  }
]
```

In the `devices` object, provide the following information:

`id`  
A user\-defined unique identifier for your device\.

`connectivity.ip`  
The IP address of your device\.

`connectivity.port`  
Optional\. The port number to use for SSH connections to your device\.

`connectivity.auth`  
Authentication information for the connection\.  
This property applies only if `connectivity.protocol` is set to `ssh`\.    
`connectivity.auth.method`  
The authentication method used to access a device over the given connectivity protocol\.  
Supported values are:  
+ `pki`
+ `password`  
`connectivity.auth.credentials`  
The credentials used for authentication\.    
`connectivity.auth.credentials.user`  
The user name used to sign in to your device\.  
`connectivity.auth.credentials.privKeyPath`  
The full path to the private key used to sign in to your device\.  
This value applies only if `connectivity.auth.method` is set to `pki`\.  
`devices.connectivity.auth.credentials.password`  
The password used for signing in to your device\.  
This value applies only if `connectivity.auth.method` is set to `password`\.

**Note**  
Specify `privKeyPath` only if `method` is set to `pki`\.  
Specify `password` only if `method` is set to `password`\.

## Run the test suite<a name="run-test-suite"></a>

After you create your test suite, you want to make sure that it functions as expected\. Complete the following steps to run the test suite with your existing device pool to do so\.

1. Copy your `MyTestSuite_1.0.0` folder into `<device-tester-extract-location>/tests`\.

1. Run the following commands:

   ```
   cd <device-tester-extract-location>/bin
   ./devicetester_[linux | mac | win_x86-64] run-suite --suite-id MyTestSuite
   ```

IDT runs your test suite and streams the results to the console\. When the test has finished running, you see the following information:

```
time="2020-10-19T09:24:47-07:00" level=info msg=Using pool: pool
time="2020-10-19T09:24:47-07:00" level=info msg=Using test suite "MyTestSuite_1.0.0" for execution
time="2020-10-19T09:24:47-07:00" level=info msg=b'hello world\n' suiteId=MyTestSuite groupId=myTestGroup testCaseId=myTestCase deviceId=my-device executionId=9a52f362-1227-11eb-86c9-8c8590419f30
time="2020-10-19T09:24:47-07:00" level=info msg=All tests finished. executionId=9a52f362-1227-11eb-86c9-8c8590419f30
time="2020-10-19T09:24:48-07:00" level=info msg=

========== Test Summary ==========
Execution Time:         1s
Tests Completed:        1
Tests Passed:           1
Tests Failed:           0
Tests Skipped:          0
----------------------------------
Test Groups:
    myTestGroup:        PASSED
----------------------------------
Path to AWS IoT Device Tester Report: /path/to/devicetester/results/9a52f362-1227-11eb-86c9-8c8590419f30/awsiotdevicetester_report.xml
Path to Test Execution Logs: /path/to/devicetester/results/9a52f362-1227-11eb-86c9-8c8590419f30/logs
Path to Aggregated JUnit Report: /path/to/devicetester/results/9a52f362-1227-11eb-86c9-8c8590419f30/MyTestSuite_Report.xml
```

## Troubleshooting<a name="tutorial-troubleshooting"></a>

Use the following information to help resolve any issues with completing the tutorial\.

**Test case does not run successfully**

If the test does not run successfully, IDT streams the error logs to the console that can help you troubleshoot the test run\. Before you check the error logs, verify the following:
+ The IDT client SDK is in the correct folder as described in [this step](#add-idt-sdk)\.
+ You meet all the [prerequisites](#prereqs-tutorial-custom) for this tutorial\.

**Cannot connect to the device under test**

Verify the following:
+ Your `device.json` file contains the correct IP address, port, and authentication information\.
+ You can connect to your device over SSH from your host computer\.