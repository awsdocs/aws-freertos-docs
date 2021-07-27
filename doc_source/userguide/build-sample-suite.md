# Tutorial: Build and run the sample IDT test suite<a name="build-sample-suite"></a>

The AWS IoT Device Tester download includes the source code for a sample test suite\. You can complete this tutorial to build and run the sample test suite to understand how you can use AWS IoT Device Tester for FreeRTOS to run custom test suites\. Although this tutorial uses SSH, it is useful to learn how to use AWS IoT Device Tester with FreeRTOS devices\.

 In this tutorial, you will complete the following steps: 

1. [Build the sample test suite](#build-sample)

1. [Use IDT to run the sample test suite](#run-sample)

## Prerequisites<a name="prereqs-tutorial-sample"></a>

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

## Configure device information for IDT<a name="configure-idt-sample"></a>

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

**`id`**  
A user\-defined unique identifier for your device\.

**`connectivity.ip`**  
The IP address of your device\.

**`connectivity.port`**  
Optional\. The port number to use for SSH connections to your device\.

**`connectivity.auth`**  
Authentication information for the connection\.  
This property applies only if `connectivity.protocol` is set to `ssh`\.    
**`connectivity.auth.method`**  
The authentication method used to access a device over the given connectivity protocol\.  
Supported values are:  
+ `pki`
+ `password`  
**`connectivity.auth.credentials`**  
The credentials used for authentication\.    
**`connectivity.auth.credentials.user`**  
The user name used to sign in to your device\.  
**`connectivity.auth.credentials.privKeyPath`**  
The full path to the private key used to sign in to your device\.  
This value applies only if `connectivity.auth.method` is set to `pki`\.  
**`devices.connectivity.auth.credentials.password`**  
The password used for signing in to your device\.  
This value applies only if `connectivity.auth.method` is set to `password`\.

**Note**  
Specify `privKeyPath` only if `method` is set to `pki`\.  
Specify `password` only if `method` is set to `password`\.

## Build the sample test suite<a name="build-sample"></a>

The `<device-tester-extract-location>/samples/python` folder contains sample configuration files, source code, and the IDT Client SDK that you can combine into a test suite using the provided build scripts\. The following directory tree shows the location of these sample files:

```
<device-tester-extract-location>
├── ...
├── tests
├── samples
│   ├── ...
│   └── python
│       ├── configuration
│       ├── src
│       └── build-scripts
│           ├── build.sh
│           └── build.ps1
└── sdks
    ├── ...
    └── python
        └── idt_client
```

To build the test suite, run the following commands on your host computer:

------
#### [ Windows ]

```
cd <device-tester-extract-location>/samples/python/build-scripts
./build.ps1
```

------
#### [ Linux, macOS, or UNIX ]

```
cd <device-tester-extract-location>/samples/python/build-scripts
./build.sh
```

------

This creates the sample test suite in the `IDTSampleSuitePython_1.0.0` folder within the `<device-tester-extract-location>/tests` folder\. Review the files in the `IDTSampleSuitePython_1.0.0` folder to understand how the sample test suite is structured and see various examples of test case executables and test configuration JSON files\. 

Next step: Use IDT to [run the sample test suite](#run-sample) that you created\.

## Use IDT to run the sample test suite<a name="run-sample"></a>

To run the sample test suite, run the following commands on your host computer: 

```
cd <device-tester-extract-location>/bin
./devicetester_[linux | mac | win_x86-64] run-suite --suite-id IDTSampleSuitePython
```

IDT runs the sample test suite and streams the results to the console\. When the test has finished running, you see the following information:

```
========== Test Summary ==========
Execution Time:         5s
Tests Completed:        4
Tests Passed:           4
Tests Failed:           0
Tests Skipped:          0
----------------------------------
Test Groups:
    sample_group:       PASSED
----------------------------------
Path to AWS IoT Device Tester Report: /path/to/devicetester/results/87e673c6-1226-11eb-9269-8c8590419f30/awsiotdevicetester_report.xml
Path to Test Execution Logs: /path/to/devicetester/results/87e673c6-1226-11eb-9269-8c8590419f30/logs
Path to Aggregated JUnit Report: /path/to/devicetester/results/87e673c6-1226-11eb-9269-8c8590419f30/IDTSampleSuitePython_Report.xml
```

## Troubleshooting<a name="tutorial-troubleshooting-custom"></a>

Use the following information to help resolve any issues with completing the tutorial\.

**Test case does not run successfully**  
If the test does not run successfully, IDT streams the error logs to the console that can help you troubleshoot the test run\. Make sure that you meet all the [prerequisites](#prereqs-tutorial-sample) for this tutorial\.

**Cannot connect to the device under test**

Verify the following:
+ Your `device.json` file contains the correct IP address, port, and authentication information\.
+ You can connect to your device over SSH from your host computer\.