# Configure settings for test runners<a name="set-config-custom"></a>

To run custom test suites, test runners must configure their settings based on the test suite that they want to run\. Settings are specified based on configuration file templates located in the `<device-tester-extract-location>/configs/` folder\. If required, test runners must also set up AWS credentials that IDT will use to connect to the AWS cloud\. 

As a test writer, you will need to configure these files to [debug your test suite](run-tests-custom.md)\. You must provide instructions to test runners so that they can configure the following settings as needed to run your test suites\. 

## Configure device\.json<a name="device-config-custom"></a>

The `device.json` file contains information about the devices that tests are run on \(for example, IP address, login information, operating system, and CPU architecture\)\. 

Test runners can provide this information using the following template `device.json` file located in the `<device-tester-extract-location>/configs/` folder\.

```
[
    {
        "id": "<pool-id>",
        "sku": "<pool-sku>",
        "features": [
            {
                "name": "<feature-name>",             
                "value": "<feature-value>",                
                "configs": [
                    {
                        "name": "<config-name>",                    
                        "value": "<config-value>"
                    }
                ],
            }
        ],     
        "devices": [
            {
                "id": "<device-id>",              
                "connectivity": {
                    "protocol": "ssh | uart | docker",                   
                    // ssh
                    "ip": "<ip-address>",
                    "port": <port-number>,
                    "auth": {
                        "method": "pki | password",
                        "credentials": {
                            "user": "<user-name>", 
                            // pki
                            "privKeyPath": "/path/to/private/key",
                                         
                            // password
                            "password": "<password>",
                        }
                    },
                    
                    // uart
                    "serialPort": "<serial-port>",
                    
                    // docker
                    "containerId": "<container-id>",
                    "containerUser": "<container-user-name>",
                }
            }
        ]
    }
]
```

All fields that contain values are required as described here:

**`id`**  
A user\-defined alphanumeric ID that uniquely identifies a collection of devices called a *device pool*\. Devices that belong to a pool must have identical hardware\. When you run a suite of tests, devices in the pool are used to parallelize the workload\. Multiple devices are used to run different tests\.

**`sku`**  
An alphanumeric value that uniquely identifies the device under test\. The SKU is used to track qualified devices\.  
If you want to list your board in the AWS Partner Device Catalog, the SKU you specify here must match the SKU that you use in the listing process\.

**`features`**  
Optional\. An array that contains the device's supported features\. Device features are user\-defined values that you configure in your test suite\. You must provide your test runners with information about the feature names and values to include in the `device.json` file\. For example, if you want to test a device that functions as an MQTT server for other devices, then you can configure your test logic to validate specific supported levels for a feature named `MQTT_QOS`\. Test runners provide this feature name and set the feature value to the QOS levels supported by their device\. You can retrieve the provided information from the [IDT context](idt-context.md) with the `devicePool.features` query, or from the [state machine context](idt-state-machine.md#state-machine-context) with the `pool.features` query\.    
**`features.name`**  
The name of the feature\.  
**`features.value`**  
The supported feature values\.  
**`features.configs`**  
Configuration settings, if needed, for the feature\.    
**`features.config.name`**  
The name of the configuration setting\.  
**`features.config.value`**  
The supported setting values\.

**`devices`**  
An array of devices in the pool to be tested\. At least one device is required\.    
**`devices.id`**  
A user\-defined unique identifier for the device being tested\.  
**`connectivity.protocol`**  
The communication protocol used to communicate with this device\. Each device in a pool must use the same protocol\.  
Currently, the only supported values are `ssh` and `uart` for physical devices, and `docker` for Docker containers\.  
**`connectivity.ip`**  
The IP address of the device being tested\.  
This property applies only if `connectivity.protocol` is set to `ssh`\.  
**`connectivity.port`**  
Optional\. The port number to use for SSH connections\.  
The default value is 22\.  
This property applies only if `connectivity.protocol` is set to `ssh`\.  
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
**`connectivity.auth.credentials.password`**  
The password used for signing in to the device being tested\.  
This value applies only if `connectivity.auth.method` is set to `password`\.  
**`connectivity.auth.credentials.privKeyPath`**  
The full path to the private key used to sign in to the device under test\.  
This value applies only if `connectivity.auth.method` is set to `pki`\.  
**`connectivity.auth.credentials.user`**  
The user name for signing in to the device being tested\.  
**`connectivity.serialPort`**  
Optional\. The serial port to which the device is connected\.  
This property applies only if `connectivity.protocol` is set to `uart`\.  
**`connectivity.containerId`**  
The container ID or name of the Docker container being tested\.  
This property applies only if `connectivity.protocol` is set to `docker`\.  
**`connectivity.containerUser`**  
Optional\. The name of the user to user inside the container\. The default value is the user provided in the Dockerfile\.  
The default value is 22\.  
This property applies only if `connectivity.protocol` is set to `docker`\.
To check if test runners configure the incorrect device connection for a test, you can retrieve `pool.Devices[0].Connectivity.Protocol` from the state machine context and compare it to the expected value in a `Choice` state\. If an incorrect protocol is used, then print a message using the `LogMessage` state and transition to the `Fail` state\.  
Alternatively, you can use error handling code to report a test failure for incorrect device types\.

## \(Optional\) Configure userdata\.json<a name="userdata-config-custom"></a>

The `userdata.json` file contains any additional information that is required by a test suite but is not specified in the `device.json` file\. The format of this file depends on the [`userdata_scheme.json` file](idt-json-config.md#userdata-schema-json) that is defined in the test suite\. If you are a test writer, make sure you provide this information to users who will run the test suites that you write\.

## \(Optional\) Configure resource\.json<a name="resource-config-custom"></a>

The `resource.json` file contains information about any devices that will be used as resource devices\. Resource devices are devices that are required to test certain capabilities of a device under test\. For example, to test a device's Bluetooth capability, you might use a resource device to test that your device can connect to it successfully\. Resource devices are optional, and you can require as many resources devices as you need\. As a test writer, you use the [test\.json file](idt-json-config.md#test-json) to define the resource device features that are required for a test\. Test runners then use the `resource.json` file to provide a pool of resource devices that have the required features\. Make sure you provide this information to users who will run the test suites that you write\. 

Test runners can provide this information using the following template `resource.json` file located in the `<device-tester-extract-location>/configs/` folder\.

```
[
    {
        "id": "<pool-id>",
        "features": [
            {
                "name": "<feature-name>",             
                "version": "<feature-value>",                
                "jobSlots": <job-slots>
            }
        ],     
        "devices": [
            {
                "id": "<device-id>",              
                "connectivity": {
                    "protocol": "ssh | uart | docker",                   
                    // ssh
                    "ip": "<ip-address>",
                    "port": <port-number>,
                    "auth": {
                        "method": "pki | password",
                        "credentials": {
                            "user": "<user-name>", 
                            // pki
                            "privKeyPath": "/path/to/private/key",
                                         
                            // password
                            "password": "<password>",
                        }
                    },
                    
                    // uart
                    "serialPort": "<serial-port>",
                    
                    // docker
                    "containerId": "<container-id>",
                    "containerUser": "<container-user-name>",
                }
            }
        ]
    }
]
```

All fields that contain values are required as described here:

**`id`**  
A user\-defined alphanumeric ID that uniquely identifies a collection of devices called a *device pool*\. Devices that belong to a pool must have identical hardware\. When you run a suite of tests, devices in the pool are used to parallelize the workload\. Multiple devices are used to run different tests\.

**`features`**  
Optional\. An array that contains the device's supported features\. The information required in this field is defined in the [test\.json files](idt-json-config.md#test-json) in the test suite and determines which tests to run and how to run those tests\. If the test suite does not require any features, then this field is not required\.    
**`features.name`**  
The name of the feature\.  
**`features.version`**  
The feature version\.  
**`features.jobSlots`**  
Setting to indicate how many tests can concurrently use the device\. The default value is `1`\.

**`devices`**  <a name="device-array"></a>
An array of devices in the pool to be tested\. At least one device is required\.    
**`devices.id`**  
A user\-defined unique identifier for the device being tested\.  
**`connectivity.protocol`**  
The communication protocol used to communicate with this device\. Each device in a pool must use the same protocol\.  
Currently, the only supported values are `ssh` and `uart` for physical devices, and `docker` for Docker containers\.  
**`connectivity.ip`**  
The IP address of the device being tested\.  
This property applies only if `connectivity.protocol` is set to `ssh`\.  
**`connectivity.port`**  
Optional\. The port number to use for SSH connections\.  
The default value is 22\.  
This property applies only if `connectivity.protocol` is set to `ssh`\.  
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
**`connectivity.auth.credentials.password`**  
The password used for signing in to the device being tested\.  
This value applies only if `connectivity.auth.method` is set to `password`\.  
**`connectivity.auth.credentials.privKeyPath`**  
The full path to the private key used to sign in to the device under test\.  
This value applies only if `connectivity.auth.method` is set to `pki`\.  
**`connectivity.auth.credentials.user`**  
The user name for signing in to the device being tested\.  
**`connectivity.serialPort`**  
Optional\. The serial port to which the device is connected\.  
This property applies only if `connectivity.protocol` is set to `uart`\.  
**`connectivity.containerId`**  
The container ID or name of the Docker container being tested\.  
This property applies only if `connectivity.protocol` is set to `docker`\.  
**`connectivity.containerUser`**  
Optional\. The name of the user to user inside the container\. The default value is the user provided in the Dockerfile\.  
The default value is 22\.  
This property applies only if `connectivity.protocol` is set to `docker`\.

## \(Optional\) Configure config\.json<a name="config-json-custom"></a>

The `config.json` file contains configuration information for IDT\. Typically, test runners will not need to modify this file except to provide their AWS user credentials for IDT, and optionally, an AWS region\. If AWS credentials with required permissions are provided AWS IoT Device Tester collects and submits usage metrics to AWS\. This is an opt\-in feature and is used to improve IDT functionality\. For more information, see [IDT usage metrics](idt-usage-metrics.md)\.

Test runners can configure their AWS credentials in one of the following ways:
+ **Credentials file**

  IDT uses the same credentials file as the AWS CLI\. For more information, see [Configuration and credential files](https://docs.aws.amazon.com/cli/latest/userguide/cli-config-files.html)\.

  The location of the credentials file varies, depending on the operating system you are using:
  + macOS, Linux: `~/.aws/credentials`
  + Windows: `C:\Users\UserName\.aws\credentials`
+ **Environment variables**

  Environment variables are variables maintained by the operating system and used by system commands\. Variables defined during an SSH session are not available after that session is closed\. IDT can use the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables to store AWS credentials

  To set these variables on Linux, macOS, or Unix, use export:

  ```
  export AWS_ACCESS_KEY_ID=<your_access_key_id>
  export AWS_SECRET_ACCESS_KEY=<your_secret_access_key>
  ```

  To set these variables on Windows, use set:

  ```
  set AWS_ACCESS_KEY_ID=<your_access_key_id>
  set AWS_SECRET_ACCESS_KEY=<your_secret_access_key>
  ```

To configure AWS credentials for IDT, test runners edit the `auth` section in the `config.json` file located in the `<device-tester-extract-location>/configs/` folder\.

```
{
    "log": {
        "location": "logs"
    },
    "configFiles": {
        "root": "configs",
        "device": "configs/device.json"
    },
    "testPath": "tests",
    "reportPath": "results",
    "awsRegion": "<region>",
    "auth": {
        "method": "file | environment",
        "credentials": {
            "profile": "<profile-name>"
        }
    }
}
]
```

All fields that contain values are required as described here:

**Note**  
All paths in this file are defined relative to the *<device\-tester\-extract\-location>*\.

**`log.location`**  
The path to the logs folder in the *<device\-tester\-extract\-location>*\.

**`configFiles.root`**  
The path to the folder that contains the configuration files\.

**`configFiles.device`**  
The path to the `device.json` file\.

**`testPath`**  
The path to the folder that contains test suites\.

**`reportPath`**  
The path to the folder that will contain test results after IDT runs a test suite\.

**`awsRegion`**  
Optional\. The AWS region that test suites will use\. If not set, then test suites will use the default region specified in each test suite\.

**`auth.method`**  
The method IDT uses to retrieve AWS credentials\. Supported values are `file` to retrieve credentials from a credentials file, and `environment` to retrieve credentials using environment variables\.

**`auth.credentials.profile`**  
The credentials profile to use from the credentials file\. This property applies only if `auth.method` is set to `file`\.