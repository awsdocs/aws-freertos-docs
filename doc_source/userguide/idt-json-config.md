# Create IDT test suite configuration files<a name="idt-json-config"></a>

This section describes the formats in which you create JSON configuration files that you include when you write a custom test suite\.Required JSON files

`suite.json`  
Contains information about the test suite\. See [Configure suite\.json](#suite-json)\.

`group.json`  
Contains information about a test group\. You must create a `group.json` file for each test group in your test suite\. See [Configure group\.json](#group-json)\.

`test.json`  
Contains information about a test case\. You must create a `test.json` file for each test case in your test suite\. See [Configure test\.json](#test-json)\.Optional JSON files

`state_machine.json`  
Defines how tests are run when IDT runs the test suite\. See [Configure state\_machine\.json](#state-machine-json)\.

`userdata_schema.json`  
Defines the schema for the [`userdata.json` file](set-config-custom.md#userdata-config-custom) that test runners can include in their setting configuration\. The `userdata.json` file is used for any additional configuration information that is required to run the test but is not present in the `device.json` file\. See [Configure userdata\_schema\.json](#userdata-schema-json)\.

JSON configuration files are placed in your `<custom-test-suite-folder>` as shown here\.

```
<custom-test-suite-folder>
└── suite
    ├── suite.json
    ├── state_machine.json
    ├── userdata_schema.json
    ├── <test-group-folder>
        ├── group.json
        ├── <test-case-folder>
            └── test.json
```

## Configure suite\.json<a name="suite-json"></a>

The `suite.json` file sets environment variables and determines whether user data is required to run the test suite\. Use the following template to configure your `<custom-test-suite-folder>/suite/suite.json` file: 

```
{
    "id": "<suite-name>_<suite-version>",
    "title": "<suite-title>",
    "details": "<suite-details>",
    "userDataRequired": true | false,
    "environmentVariables": [
        {
            "key": "<name>",
            "value": "<value>",
        },
        ...
        {
            "key": "<name>",
            "value": "<value>",
        }
    ]
}
```

All fields that contain values are required as described here:

`id`  
A unique user\-defined ID for the test suite\. The value of `id` must match the name of the test suite folder in which the `suite.json` file is located\. The suite name and suite version must also meet the following requirements:   
+ `<suite-name>` cannot contain underscores\.
+ `<suite-version>` is denoted as `x.x.x`, where `x` is a number\.
The ID is shown in IDT\-generated test reports\.

`title`  
A user\-defined name for the product or feature being tested by this test suite\. The name is displayed in the IDT CLI for test runners\.

`details`  
A short description of the purpose of the test suite\.

`userDataRequired`  
Defines whether test runners need to include custom information in a `userdata.json` file\. If you set this value to `true`, you must also include the [`userdata_schema.json` file](#userdata-schema-json) in your test suite folder\.

`environmentVariables`  
Optional\. An array of environment variables to set for this test suite\.    
`environmentVariables.key`  
The name of the environment variable\.  
`environmentVariables.value`  
The value of the environment variable\.

## Configure group\.json<a name="group-json"></a>

The `group.json` file defines whether a test group is required or optional\. Use the following template to configure your `<custom-test-suite-folder>/suite/<test-group>/group.json` file: 

```
{
    "id": "<group-id>",
    "title": "<group-title>",
    "details": "<group-details>",
    "optional": true | false,
}
```

All fields that contain values are required as described here:

`id`  
A unique user\-defined ID for the test group\. The value of `id` must match the name of the test group folder in which the `group.json` file is located\. The ID is used in IDT\-generated test reports\.

`title`  
A descriptive name for the test group\. The name is displayed in the IDT CLI for test runners\.

`details`  
A short description of the purpose of the test group\.

`optional`  
Optional\. Set to `true` to display this test group as an optional group after IDT finishes running required tests\. Default value is `false`\.

## Configure test\.json<a name="test-json"></a>

The `test.json` file determines the test case executables and the environment variables that are used by a test case\. For more information about creating test case executables, see [Create IDT test case executables](test-executables.md)\.

Use the following template to configure your `<custom-test-suite-folder>/suite/<test-group>/<test-case>/test.json` file: 

```
{
    "id": "<test-id>",
    "title": "<test-title>",
    "details": "<test-details>",
    "requireDUT": true | false,
    "requiredResources": [
        {
            "name": "<resource-name>",
            "features": [
                {
                    "name": "<feature-name>",
                    "version": "<feature-version>",
                    "jobSlots": <job-slots>
                }
            ]
        }
    ],
    "execution": {
        "timeout": <timeout>,
        "mac": {
            "cmd": "/path/to/executable",
            "args": [
                "<argument>"
            ],
        },
        "linux": {
            "cmd": "/path/to/executable",
            "args": [
                "<argument>"
            ],
        },
        "win": {
            "cmd": "/path/to/executable",
            "args": [
                "<argument>"
            ]
        }
    },
    "environmentVariables": [
        {
            "key": "<name>",
            "value": "<value>",
        }
    ]
}
```

All fields that contain values are required as described here:

`id`  
A unique user\-defined ID for the test case\. The value of `id` must match the name of the test case folder in which the `test.json` file is located\. The ID is used in IDT\-generated test reports\.

`title`  
A descriptive name for the test case\. The name is displayed in the IDT CLI for test runners\.

`details`  
A short description of the purpose of the test case\.

`requireDUT`  
Optional\. Set to `true` if a device is required to run this test, otherwise set to `false`\. Default value is `true`\. Test runners will configure the devices they will use to run the test in their `device.json` file\.

`requiredResources`  
Optional\. An array that provides information about resource devices needed to run this test\.     
`requiredResources.name`  
The unique name to give the resource device when this test is running\.  
`requiredResources.features`  
An array of user\-defined resource device features\.     
`requiredResources.features.name`  
The name of the feature\. The device feature for which you want to use this device\. This name is matched against the feature name provided by the test runner in the `resource.json` file\.  
`requiredResources.features.version`  
Optional\. The version of the feature\. This value is matched against the feature version provided by the test runner in the `resource.json` file\. If a version is not provided, then the feature is not checked\. If a version number is not required for the feature, leave this field blank\.  
`requiredResources.features.jobSlots`  
Optional\. The number of simultaneous tests that this feature can support\. The default value is `1`\. If you want IDT to use distinct devices for individual features, then we recommend that you set this value to `1`\.

`execution.timeout`  
The amount of time \(in milliseconds\) that IDT waits for the test to finish running\. For more information about setting this value, see [Create IDT test case executables](test-executables.md)\.

`execution.os`  
The test case executables to run based on the operating system of the host computer that runs IDT\. Supported values are `linux`, `mac`, and `win`\.     
`execution.os.cmd`  
The path to the test case executable that you want to run for the specified operating system\. This location must be in the system path\.  
`execution.os.args`  
Optional\. The arguments to provide to run the test case executable\.

`environmentVariables`  
Optional\. An array of environment variables set for this test case\.     
`environmentVariables.key`  
The name of the environment variable\.  
`environmentVariables.value`  
The value of the environment variable\.
If you specify the same environment variable in the `test.json` file and in the `suite.json` file, the value in the `test.json` file takes precedence\. 

## Configure state\_machine\.json<a name="state-machine-json"></a>

A state machine is a construct that controls the test suite execution flow\. It determines the starting state of a test suite, manages state transitions based on user\-defined rules, and continues to transition through those states until it reaches the end state\. 

If your test suite doesn't include a user\-defined state machine, IDT will generate a state machine for you\. The default state machine performs the following functions:
+ Provides test runners with the ability to select and run specific test groups, instead of the entire test suite\.
+ If specific test groups are not selected, runs every test group in the test suite in a random order\. 
+ Generates reports and prints a console summary that shows the test results for each test group and test case\.

For more information about how the IDT state machine functions, see [Configure the IDT state machine](idt-state-machine.md)\.

## Configure userdata\_schema\.json<a name="userdata-schema-json"></a>

The `userdata_schema.json` file determines the schema in which test runners provide user data\. User data is required if your test suite requires information that is not present in the `device.json` file\. For example, your tests might need Wi\-Fi network credentials, specific open ports, or certificates that a user must provide\. This information can be provided to IDT as an input parameter called `userdata`, the value for which is a `userdata.json` file, that users create in their `<device-tester-extract-location>/config` folder\. The format of the `userdata.json` file is based on the `userdata_schema.json` file that you include in the test suite\.

To indicate that test runners must provide a `userdata.json` file:

1. In the `suite.json` file, set `userDataRequired` to `true`\.

1. In your `<custom-test-suite-folder>`, create a `userdata_schema.json` file\.

1. Edit the `userdata_schema.json` file to create a valid [IETF Draft v4 JSON Schema](https://json-schema.org/specification-links.html#draft-4)\.

When IDT runs your test suite, it automatically reads the schema and uses it to validate the `userdata.json` file provided by the test runner\. If valid, the contents of the `userdata.json` file are available in both the [IDT context](idt-context.md) and in the [state machine context](idt-state-machine.md#state-machine-context)\.