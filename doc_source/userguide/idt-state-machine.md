# Configure the IDT state machine<a name="idt-state-machine"></a>

**Important**  
Starting in IDT v4\.5\.2, this state machine is deprecated\. We strongly recommend that you use the new test orchestrator\. For more information, see [Configure the IDT test orchestrator](idt-test-orchestrator.md)\.

A state machine is a construct that controls the test suite execution flow\. It determines the starting state of a test suite, manages state transitions based on user\-defined rules, and continues to transition through those states until it reaches the end state\. 

If your test suite doesn't include a user\-defined state machine, IDT will generate a state machine for you\. The default state machine performs the following functions:
+ Provides test runners with the ability to select and run specific test groups, instead of the entire test suite\.
+ If specific test groups are not selected, runs every test group in the test suite in a random order\. 
+ Generates reports and prints a console summary that shows the test results for each test group and test case\.

The state machine for an IDT test suite must meet the following criteria:
+ Each state corresponds to an action for IDT to take, such as to run a test group or product a report file\.
+ Transitioning to a state executes the action associated with the state\.
+ Each state defines the transition rule for the next state\.
+ The end state must be either `Succeed` or `Fail`\.

## State machine format<a name="state-machine-format"></a>

You can use the following template to configure your own `<custom-test-suite-folder>/suite/state_machine.json` file: 

```
{
  "Comment": "<description>",
  "StartAt": "<state-name>",
  "States": {
    "<state-name>": {
      "Type": "<state-type>",
      // Additional state configuration
    }
    
    // Required states
    "Succeed": {
      "Type": "Succeed"
    },
    "Fail": {
      "Type": "Fail"
    }
  }
}
```

All fields that contain values are required as described here:

**`Comment`**  
A description of the state machine\.

**`StartAt`**  
The name of the state at which IDT starts running the test suite\. The value of `StartAt` must be set to one of the states listed in the `States` object\.

**`States`**  
An object that maps user\-defined state names to valid IDT states\. Each States\.*state\-name* object contains the definition of a valid state mapped to the *state\-name*\.  
The `States` object must include the `Succeed` and `Fail` states\. For information about valid states, see [Valid states and state definitions](#valid-states)\.

## Valid states and state definitions<a name="valid-states"></a>

This section describes the state definitions of all of the valid states that can be used in the IDT state machine\. Some of the following states support configurations at the test case level\. However, we recommend that you configure state transition rules at the test group level instead of the test case level unless absolutely necessary\.

**Topics**
+ [RunTask](#state-runtask)
+ [Choice](#state-choice)
+ [Parallel](#state-parallel)
+ [AddProductFeatures](#state-addproductfeatures)
+ [Report](#state-report)
+ [LogMessage](#state-logmessage)
+ [SelectGroup](#state-selectgroup)
+ [Fail](#state-fail)
+ [Succeed](#state-succeed)

### RunTask<a name="state-runtask"></a>

The `RunTask` state runs test cases from a test group defined in the test suite\.

```
{
    "Type": "RunTask",
    "Next": "<state-name>",
    "TestGroup": "<group-id>",
    "TestCases": [
        "<test-id>"
    ],
    "ResultVar": "<result-name>"
}
```

All fields that contain values are required as described here:

**`Next`**  
The name of the state to transition to after executing the actions in the current state\.

**`TestGroup`**  
Optional\. The ID of the test group to run\. If this value is not specified, then IDT runs the test group that the test runner selects\.

**`TestCases`**  
Optional\. An array of test case IDs from the group specified in `TestGroup`\. Based on the values of `TestGroup` and `TestCases`, IDT determines the test execution behavior as follows:   
+ When both `TestGroup` and `TestCases` are specified, IDT runs the specified test cases from the test group\. 
+ When `TestCases` are specified but `TestGroup` is not specified, IDT runs the specified test cases\.
+ When `TestGroup` is specified, but `TestCases` is not specified, IDT runs all of the test cases within the specified test group\.
+ When neither `TestGroup` or `TestCases` is specified, IDT runs all test cases from the test group that the test runner selects from the IDT CLI\. To enable group selection for test runners, you must include both `RunTask` and `Choice` states in your `statemachine.json` file\. For an example of how this works, see [Example state machine: Run user\-selected test groups](#allow-specific-groups)\.

  For more information about enabling IDT CLI commands for test runners, see [Enable IDT CLI commands](test-executables.md#idt-cli-coop)\.

**`ResultVar`**  
The name of the context variable to set with the results of the test run\. Do not specify this value if you did not specify a value for `TestGroup`\. IDT sets the value of the variable that you define in `ResultVar` to `true` or `false` based on the following:   
+ If the variable name is of the form `text_text_passed`, then the value is set to whether all tests in the first test group passed or were skipped\.
+ In all other cases, the value is set to whether all tests in all test groups passed or were skipped\.

Typically, you will use `RunTask` state to specify a test group ID without specifying individual test case IDs, so that IDT will run all of the test cases in the specified test group\. All test cases that are run by this state run in parallel, in a random order\. However, if all of the test cases require a device to run, and only a single device is available, then the test cases will run sequentially instead\. 

**Error handling**

If any of the specified test groups or test case IDs are not valid, then this state issues the `RunTaskError` execution error\. If the state encounters an execution error, then it also sets the `hasExecutionError` variable in the state machine context to `true`\.

### Choice<a name="state-choice"></a>

The `Choice` state lets you dynamically set the next state to transition to based on user\-defined conditions\.

```
{
    "Type": "Choice",
    "Default": "<state-name>", 
    "FallthroughOnError": true | false,
    "Choices": [
        {
            "Expression": "<expression>",
            "Next": "<state-name>"
        }
    ]
}
```

All fields that contain values are required as described here:

**`Default`**  
The default state to transition to if none of the expressions defined in `Choices` can be evaluated to `true`\.

**`FallthroughOnError`**  
Optional\. Specifies the behavior when the state encounters an error in evaluating expressions\. Set to `true` if you want to skip an expression if the evaluation results in an error\. If no expressions match, then the state machine transitions to the `Default` state\. If the `FallthroughOnError` value is not specified, it defaults to `false`\. 

**`Choices`**  
An array of expressions and states to determine which state to transition to after executing the actions in the current state\.    
**`Choices.Expression`**  
An expression string that evaluates to a boolean value\. If the expression evaluates to `true`, then the state machine transitions to the state defined in `Choices.Next`\. Expression strings retrieve values from the state machine context and then perform operations on them to arrive at a boolean value\. For information about accessing the state machine context, see [State machine context](#state-machine-context)\.   
**`Choices.Next`**  
The name of the state to transition to if the expression defined in `Choices.Expression` evaluates to `true`\.

**Error handling**

The `Choice` state can require error handling in the following cases: 
+ Some variables in the choice expressions don’t exist in the state machine context\.
+ The result of an expression is not a boolean value\.
+ The result of a JSON lookup is not a string, number, or boolean\.

You cannot use a `Catch` block to handle errors in this state\. If you want to stop executing the state machine when it encounters an error, you must set `FallthroughOnError` to `false`\. However, we recommend that you set `FallthroughOnError` to `true`, and depending on your use case, do one of the following:
+ If a variable you are accessing is expected to not exist in some cases, then use the value of `Default` and additional `Choices` blocks to specify the next state\.
+ If a variable that you are accessing should always exist, then set the `Default` state to `Fail`\.

### Parallel<a name="state-parallel"></a>

The `Parallel` state lets you define and run new state machines in parallel with each other\.

```
{
    "Type": "Parallel",
    "Next": "<state-name>",
    "Branches": [
        <state-machine-definition>
    ]
}
```

All fields that contain values are required as described here:

**`Next`**  
The name of the state to transition to after executing the actions in the current state\.

**`Branches`**  
An array of state machine definitions to run\. Each state machine definition must contain its own `StartAt`, `Succeed`, and `Fail` states\. The state machine definitions in this array cannot reference states outside of their own definition\.   
Because each branch state machine shares the same state machine context, setting variables in one branch and then reading those variables from another branch might result in unexpected behavior\.

The `Parallel` state moves to the next state only after it runs all of the branch state machines\. Each state that requires a device will wait to run until the device is available\. If multiple devices are available, this state runs test cases from multiple groups in parallel\. If enough devices are not available, then test cases will run sequentially\. Because test cases are run in a random order when they run in parallel, different devices might be used to run tests from the same test group\. 

**Error handling**

Make sure that both the branch state machine and the parent state machine transition to the `Fail` state to handle execution errors\. 

Because branch state machines do not transmit execution errors to the parent state machine, you cannot use a `Catch` block to handle execution errors in branch state machines\. Instead, use the `hasExecutionErrors` value in the shared state machine context\. For an example of how this works, see [Example state machine: Run two test groups in parallel](#run-in-parallel)\.

### AddProductFeatures<a name="state-addproductfeatures"></a>

The `AddProductFeatures` state lets you add product features to the `awsiotdevicetester_report.xml` file generated by IDT\. 

A product feature is user\-defined information about specific criteria that a device might meet\. For example, the `MQTT` product feature can designate that the device publishes MQTT messages properly\. In the report, product features are set as `supported`, `not-supported`, or a custom value, based on whether specified tests passed\.



**Note**  
The `AddProductFeatures` state does not generate reports by itself\. This state must transition to the [`Report` state](#state-report) to generate reports\.

```
{
    "Type": "Parallel",
    "Next": "<state-name>",
    "Features": [
        {
            "Feature": "<feature-name>", 
            "Groups": [
                "<group-id>"
            ],
            "OneOfGroups": [
                "<group-id>"
            ],
            "TestCases": [
                "<test-id>"
            ],
            "IsRequired": true | false,
            "ExecutionMethods": [
                "<execution-method>"
            ]
        }
    ]
}
```

All fields that contain values are required as described here:

**`Next`**  
The name of the state to transition to after executing the actions in the current state\.

**`Features`**  
An array of product features to show in the `awsiotdevicetester_report.xml` file\.    
**`Feature`**  
The name of the feature  
**`FeatureValue`**  
Optional\. The custom value to use in the report instead of `supported`\. If this value is not specified, then based on test results, the feature value is set to `supported` or `not-supported`\.   
If you use a custom value for `FeatureValue`, you can test the same feature with different conditions, and IDT concatenates the feature values for the supported conditions\. For example, the following excerpt shows the `MyFeature` feature with two separate feature values:  

```
...
{
    "Feature": "MyFeature",
    "FeatureValue": "first-feature-supported",
    "Groups": ["first-feature-group"]
},
{
    "Feature": "MyFeature",
    "FeatureValue": "second-feature-supported",
    "Groups": ["second-feature-group"]
},
...
```
If both test groups pass, then the feature value is set to `first-feature-supported, second-feature-supported`\.   
**`Groups`**  
Optional\. An array of test group IDs\. All tests within each specified test group must pass for the feature to be supported\.  
**`OneOfGroups`**  
Optional\. An array of test group IDs\. All tests within at least one of the specified test groups must pass for the feature to be supported\.   
**`TestCases`**  
Optional\. An array of test case IDs\. If you specify this value, then the following apply:  
+ All of the specified test cases must pass for the feature to be supported\.
+ `Groups` must contain only one test group ID\.
+ `OneOfGroups` must not be specified\.  
**`IsRequired`**  
Optional\. Set to `false` to mark this feature as an optional feature in the report\. The default value is `true`\.  
**`ExecutionMethods`**  
Optional\. An array of execution methods that match the `protocol` value specified in the `device.json` file\. If this value is specified, then test runners must specify a `protocol` value that matches one of the values in this array to include the feature in the report\. If this value is not specified, then the feature will always be included in the report\.

To use the `AddProductFeatures` state, you must set the value of `ResultVar` in the `RunTask` state to one of the following values:
+ If you specified individual test case IDs, then set `ResultVar` to `group-id_test-id_passed`\.
+ If you did not specify individual test case IDs, then set `ResultVar` to `group-id_passed`\.

The `AddProductFeatures` state checks for test results in the following manner: 
+ If you did not specify any test case IDs, then the result for each test group is determined from the value of the `group-id_passed` variable in the state machine context\.
+ If you did specify test case IDs, then the result for each of the tests is determined from the value of the `group-id_test-id_passed` variable in the state machine context\.

**Error handling**

If a group ID provided in this state is not a valid group ID, then this state results in the `AddProductFeaturesError` execution error\. If the state encounters an execution error, then it also sets the `hasExecutionErrors` variable in the state machine context to `true`\.

### Report<a name="state-report"></a>

The `Report` state generates the `suite-name_Report.xml` and `awsiotdevicetester_report.xml` files\. This state also streams the report to the console\.

```
{
    "Type": "Report",
    "Next": "<state-name>"
}
```

All fields that contain values are required as described here:

**`Next`**  
The name of the state to transition to after executing the actions in the current state\.

You should always transition to the `Report` state towards the end of the test execution flow so that test runners can view test results\. Typically, the next state after this state is `Succeed`\. 

**Error handling**

If this state encounters issues with generating the reports, then it issues the `ReportError` execution error\. 

### LogMessage<a name="state-logmessage"></a>

The `LogMessage` state generates the `test_manager.log` file and streams the log message to the console\.

```
{
    "Type": "LogMessage",
    "Next": "<state-name>"
    "Level": "info | warn | error"
    "Message": "<message>"
}
```

All fields that contain values are required as described here:

**`Next`**  
The name of the state to transition to after executing the actions in the current state\.

**`Level`**  
The error level at which to create the log message\. If you specify a level that is not valid, this state generates an error message and discards it\. 

**`Message`**  
The message to log\.

### SelectGroup<a name="state-selectgroup"></a>

The `SelectGroup` state updates the state machine context to indicate which groups are selected\. The values set by this state are used by any subsequent `Choice` states\.

```
{
    "Type": "SelectGroup",
    "Next": "<state-name>"
    "TestGroups": [
        <group-id>"
    ]
}
```

All fields that contain values are required as described here:

**`Next`**  
The name of the state to transition to after executing the actions in the current state\.

**`TestGroups`**  
An array of test groups that will be marked as selected\. For each test group ID in this array, the `group-id_selected` variable is set to `true` in the context\. Make sure that you provide valid test group IDs because IDT does not validate whether the specified groups exist\.

### Fail<a name="state-fail"></a>

The `Fail` state indicates that the state machine did not execute correctly\. This is an end state for the state machine, and each state machine definition must include this state\.

```
{
    "Type": "Fail"
}
```

### Succeed<a name="state-succeed"></a>

The `Succeed` state indicates that the state machine executed correctly\. This is an end state for the state machine, and each state machine definition must include this state\.

```
{
    "Type": "Succeed"
}
```

## State machine context<a name="state-machine-context"></a>

The state machine context is a read\-only JSON document that contains data that is available to the state machine during execution\. The state machine context is accessible only from the state machine, and contains information that determines the test flow\. For example, you can use information configured by test runners in the `userdata.json` file to determine whether a specific test is required to run\.

The state machine context uses the following format:

```
{
    "pool": {
        <device-json-pool-element>
    },
    "userData": {
        <userdata-json-content>
    },
    "config": {
        <config-json-content>
    },
    "suiteFailed": true | false,
    "specificTestGroups": [
        "<group-id>"
    ],
    "specificTestCases": [
        "<test-id>"
    ],
    "hasExecutionErrors": true
}
```

**`pool`**  
Information about the device pool selected for the test run\. For a selected device pool, this information is retrieved from the corresponding top\-level device pool array element defined in the `device.json` file\.

**`userData`**  
Information in the `userdata.json` file\.

**`config`**  
Information pin the `config.json` file\.

**`suiteFailed`**  
The value is set to `false` when the state machine starts\. If a test group fails in a `RunTask` state, then this value is set to `true` for the remaining duration of the state machine execution\.

**`specificTestGroups`**  
If the test runner selects specific test groups to run instead of the entire test suite, this key is created and contains the list of specific test group IDs\.

**`specificTestCases`**  
If the test runner selects specific test cases to run instead of the entire test suite, this key is created and contains the list of specific test case IDs\.

**`hasExecutionErrors`**  
Does not exit when the state machine starts\. If any state encounters an execution errors, this variable is created and set to `true` for the remaining duration of the state machine execution\.

You can query the context using JSONPath notation\. The syntax for JSONPath queries in state definitions is `{{$.query}}`\. You can use JSONPath queries as placeholder strings within some states\. IDT replaces the placeholder strings with the value of the evaluated JSONPath query from the context\. You can use placeholders for the following values:
+ The `TestCases` value in `RunTask` states\. 
+ The `Expression` value `Choice` state\.

When you access data from the state machine context, make sure the following conditions are met: 
+ Your JSON paths must begin with `$.`
+ Each value must evaluate to a string, a number, or a boolean\.

For more information about using JSONPath notation to access data from the context, see [Use the IDT context](idt-context.md)\.

## Execution errors<a name="execution-errors"></a>

Execution errors are errors in the state machine definition that the state machine encounters when executing a state\. IDT logs information about each error in the `test_manager.log` file and streams the log message to the console\.

You can use the following methods to handle execution errors:
+ Add a [`Catch` block](#catch) in the state definition\.
+ Check the value of the [`hasExecutionErrors` value](#context) in the state machine context\.

### Catch<a name="catch"></a>

To use `Catch`, add the following to your state definition:

```
"Catch": [
    {    
        "ErrorEquals": [
            "<error-type>"
        ]
        "Next": "<state-name>" 
    }
]
```

All fields that contain values are required as described here:

**`Catch.ErrorEquals`**  
An array of the error types to catch\. If an execution error matches one of the specified values, then the state machine transitions to the state specified in `Catch.Next`\. See each state definition for information about the type of error it produces\.

**`Catch.Next`**  
The next state to transition to if the current state encounters an execution error that matches one of the values specified in `Catch.ErrorEquals` \.

Catch blocks are handled sequentially until one matches\. If the no errors match the ones listed in the Catch blocks, then the state machines continues to execute\. Because execution errors are a result of incorrect state definitions, we recommend that you transition to the Fail state when a state encounters an execution error\.

### hasExecutionError<a name="context"></a>

When some states encounter execution errors, in addition to issuing the error, they also set the `hasExecutionError` value to `true` in the state machine context\. You can use this value to detect when an error occurs, and then use a `Choice` state to transition the state machine to the `Fail` state\.

This method has the following characteristics\.
+ The state machine does not start with any value assigned to `hasExecutionError`, and this value is not available until a particular state sets it\. This means that you must explicitly set the `FallthroughOnError` to `false` for the `Choice` states that access this value to prevent the state machine from stopping if no execution errors occur\. 
+ Once it is set to `true`, `hasExecutionError` is never set to false or removed from the context\. This means that this value is useful only the first time that it is set to `true`, and for all subsequent states, it does not provide a meaningful value\.
+ The `hasExecutionError` value is shared with all branch state machines in the `Parallel` state, which can result in unexpected results depending on the order in which it is accessed\.

Because of these characteristics, we do not recommend that you use this method if you can use a Catch block instead\. 

## Example state machines<a name="state-machine-examples"></a>

This section provides some example state machine configurations\.

**Topics**
+ [Example state machine: Run a single test group](#single-test-group)
+ [Example state machine: Run user\-selected test groups](#allow-specific-groups)
+ [Example state machine: Run a single test group with product features](#run-with-product-features)
+ [Example state machine: Run two test groups in parallel](#run-in-parallel)

### Example state machine: Run a single test group<a name="single-test-group"></a>

This state machine:
+ Runs the test group with id `GroupA`, which must be present in the suite in a `group.json` file\.
+ Checks for execution errors and transitions to `Fail` if any are found\.
+ Generates a report and transitions to `Succeed` if there are no errors, and `Fail` otherwise\.

```
{
    "Comment": "Runs a single group and then generates a report.",
    "StartAt": "RunGroupA",
    "States": {
        "RunGroupA": {
            "Type": "RunTask",
            "Next": "Report",
            "TestGroup": "GroupA",
            "Catch": [
                {
                    "ErrorEquals": [
                        "RunTaskError"
                    ],
                    "Next": "Fail"
                }
            ]
        },
        "Report": {
            "Type": "Report",
            "Next": "Succeed",
            "Catch": [
                {
                    "ErrorEquals": [
                        "ReportError"
                    ],
                    "Next": "Fail"
                }
            ]
        },
        "Succeed": {
            "Type": "Succeed"
        },
        "Fail": {
            "Type": "Fail"
        }
    }
}
```

### Example state machine: Run user\-selected test groups<a name="allow-specific-groups"></a>

This state machine:
+ Checks if the test runner selected specific test groups\. The state machine does not check for specific test cases because test runners cannot select test cases without also selecting a test group\.
+ If test groups are selected: 
  + Runs the test cases within the selected test groups\. To do so, the state machine does not explicitly specify any test groups or test cases in the `RunTask` state\.
  + Generates a report after running all tests and exits\.
+ If test groups are not selected:
  + Runs tests in test group `GroupA`\.
  + Generates reports and exits\.

```
{
    "Comment": "Runs specific groups if the test runner chose to do that, otherwise runs GroupA.",
    "StartAt": "SpecificGroupsCheck",
    "States": {
        "SpecificGroupsCheck": {
            "Type": "Choice",
            "Default": "RunGroupA",
            "FallthroughOnError": true,
            "Choices": [
                {
                    "Expression": "{{$.specificTestGroups[0]}} != ''",
                    "Next": "RunSpecificGroups"
                }
            ]
        },
        "RunSpecificGroups": {
            "Type": "RunTask",
            "Next": "Report",
            "Catch": [
                {
                    "ErrorEquals": [
                        "RunTaskError"
                    ],
                    "Next": "Fail"
                }
            ]
        },
        "RunGroupA": {
            "Type": "RunTask",
            "Next": "Report",
            "TestGroup": "GroupA",
            "Catch": [
                {
                    "ErrorEquals": [
                        "RunTaskError"
                    ],
                    "Next": "Fail"
                }
            ]
        },
        "Report": {
            "Type": "Report",
            "Next": "Succeed",
            "Catch": [
                {
                    "ErrorEquals": [
                        "ReportError"
                    ],
                    "Next": "Fail"
                }
            ]
        },
        "Succeed": {
            "Type": "Succeed"
        },
        "Fail": {
            "Type": "Fail"
        }
    }
}
```

### Example state machine: Run a single test group with product features<a name="run-with-product-features"></a>

This state machine:
+ Runs the test group `GroupA`\.
+ Checks for execution errors and transitions to `Fail` if any are found\.
+ Adds the `FeatureThatDependsOnGroupA` feature to the `awsiotdevicetester_report.xml` file:
  + If `GroupA` passes, the feature is set to `supported`\.
  + The feature is not marked optional in the report\.
+ Generates a report and transitions to `Succeed` if there are no errors, and `Fail` otherwise

```
{
    "Comment": "Runs GroupA and adds product features based on GroupA",
    "StartAt": "RunGroupA",
    "States": {
        "RunGroupA": {
            "Type": "RunTask",
            "Next": "AddProductFeatures",
            "TestGroup": "GroupA",
            "ResultVar": "GroupA_passed",
            "Catch": [
                {
                    "ErrorEquals": [
                        "RunTaskError"
                    ],
                    "Next": "Fail"
                }
            ]
        },
        "AddProductFeatures": {
            "Type": "AddProductFeatures",
            "Next": "Report",
            "Features": [
                {
                    "Feature": "FeatureThatDependsOnGroupA",
                    "Groups": [
                        "GroupA"
                    ],
                    "IsRequired": true
                }
            ]
        },
        "Report": {
            "Type": "Report",
            "Next": "Succeed",
            "Catch": [
                {
                    "ErrorEquals": [
                        "ReportError"
                    ],
                    "Next": "Fail"
                }
            ]
        },
        "Succeed": {
            "Type": "Succeed"
        },
        "Fail": {
            "Type": "Fail"
        }
    }
}
```

### Example state machine: Run two test groups in parallel<a name="run-in-parallel"></a>

This state machine:
+ Runs the `GroupA` and `GroupB` test groups in parallel\. The `ResultVar` variables stored in the context by the `RunTask` states in the branch state machines by are available to the `AddProductFeatures` state\.
+ Checks for execution errors and transitions to `Fail` if any are found\. This state machine does not use a `Catch` block because that method does not detect execution errors in branch state machines\.
+ Adds features to the `awsiotdevicetester_report.xml` file based on the groups that pass
  + If `GroupA` passes, the feature is set to `supported`\.
  + The feature is not marked optional in the report\.
+ Generates a report and transitions to `Succeed` if there are no errors, and `Fail` otherwise

If two devices are configured in the device pool, both `GroupA` and `GroupB` can run at the same time\. However, if either `GroupA` or `GroupB` has multiple tests in it, then both devices may be allocated to those tests\. If only one device is configured, the test groups will run sequentially\.

```
{
    "Comment": "Runs GroupA and GroupB in parallel",
    "StartAt": "RunGroupAAndB",
    "States": {
        "RunGroupAAndB": {
            "Type": "Parallel",
            "Next": "CheckForErrors",
            "Branches": [
                {
                    "Comment": "Run GroupA state machine",
                    "StartAt": "RunGroupA",
                    "States": {
                        "RunGroupA": {
                            "Type": "RunTask",
                            "Next": "Succeed",
                            "TestGroup": "GroupA",
                            "ResultVar": "GroupA_passed",
                            "Catch": [
                                {
                                    "ErrorEquals": [
                                        "RunTaskError"
                                    ],
                                    "Next": "Fail"
                                }
                            ]
                        },
                        "Succeed": {
                            "Type": "Succeed"
                        },
                        "Fail": {
                            "Type": "Fail"
                        }
                    }
                },
                {
                    "Comment": "Run GroupB state machine",
                    "StartAt": "RunGroupB",
                    "States": {
                        "RunGroupA": {
                            "Type": "RunTask",
                            "Next": "Succeed",
                            "TestGroup": "GroupB",
                            "ResultVar": "GroupB_passed",
                            "Catch": [
                                {
                                    "ErrorEquals": [
                                        "RunTaskError"
                                    ],
                                    "Next": "Fail"
                                }
                            ]
                        },
                        "Succeed": {
                            "Type": "Succeed"
                        },
                        "Fail": {
                            "Type": "Fail"
                        }
                    }
                }
            ]
        },
        "CheckForErrors": {
            "Type": "Choice",
            "Default": "AddProductFeatures",
            "FallthroughOnError": true,
            "Choices": [
                {
                    "Expression": "{{$.hasExecutionErrors}} == true",
                    "Next": "Fail"
                }
            ]
        },
        "AddProductFeatures": {
            "Type": "AddProductFeatures",
            "Next": "Report",
            "Features": [
                {
                    "Feature": "FeatureThatDependsOnGroupA",
                    "Groups": [
                        "GroupA"
                    ],
                    "IsRequired": true
                },
                {
                    "Feature": "FeatureThatDependsOnGroupB",
                    "Groups": [
                        "GroupB"
                    ],
                    "IsRequired": true
                }
            ]
        },
        "Report": {
            "Type": "Report",
            "Next": "Succeed",
            "Catch": [
                {
                    "ErrorEquals": [
                        "ReportError"
                    ],
                    "Next": "Fail"
                }
            ]
        },
        "Succeed": {
            "Type": "Succeed"
        },
        "Fail": {
            "Type": "Fail"
        }
    }
}
```