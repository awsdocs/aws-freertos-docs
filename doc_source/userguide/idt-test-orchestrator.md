# Configure the IDT test orchestrator<a name="idt-test-orchestrator"></a>

Starting in IDT v4\.5\.2, IDT includes a new *test orchestrator* component\. The test orchestrator is an IDT component that controls the test suite execution flow, and generates the test report after IDT finishes running all tests\. The test orchestrator determines test selection and the order in which tests are run based on user\-defined rules\.

If your test suite doesn't include a user\-defined test orchestrator, IDT will generate a test orchestrator for you\. 

The default test orchestrator performs the following functions:
+ Provides test runners with the ability to select and run specific test groups, instead of the entire test suite\.
+ If specific test groups are not selected, runs every test group in the test suite in a random order\. 
+ Generates reports and prints a console summary that shows the test results for each test group and test case\.

The test orchestrator replaces the IDT state machine\. We strongly recommend that you use the test orchestrator to develop your test suites instead of the IDT state machine\. The test orchestrator provides the following improved features: 
+ Uses a declarative format compared to the imperative format that the IDT state machine uses\. This allows you to specify which tests you want to run and when you want to run them\. 
+ Manages specific group handling, report generation, error handling, and result tracking so that you aren't required to manually manage these actions\. 
+ Uses the YAML format, which supports comments by default\.
+ Requires 80 percent less disk space than the test orchestrator to define the same workflow\.
+ Adds pre\-test validation to verify that your workflow definition doesn't contain incorrect test IDs or circular dependencies\.

## Test orchestrator format<a name="idt-test-orchestrator-format"></a>

You can use the following template to configure your own `custom-test-suite-folder/suite/test_orchestrator.yaml` file: 

```
Aliases:
  string: context-expression

ConditionalTests:
  - Condition: context-expression
    Tests:
      - test-descriptor

Order:
  - - group-descriptor
    - group-descriptor

Features:
  - Name: feature-name
    Value: support-description
    Condition: context-expression
    Tests:
        - test-descriptor
    OneOfTests:
        - test-descriptor
    IsRequired: boolean
```

All fields that contain values are required as described here:

`Aliases`  
Optional\. User\-defined strings that map to context expressions\. Aliases allow you to generate friendly names to identify context expressions in your test orchestrator configuration\. This is especially useful if you're creating complex context expressions or expressions that you use in multiple places\.  
You can use context expressions to store context queries that allow you to access data from other IDT configurations\. For more information, see [Access data in the context](idt-context.md#accessing-context-data)\.  

**Example**  

```
Aliases:
    FizzChosen: "'{{$pool.features[?(@.name == 'Fizz')].value[0]}}' == 'yes'"    
    BuzzChosen: "'{{$pool.features[?(@.name == 'Buzz')].value[0]}}' == 'yes'"    
    FizzBuzzChosen: "'{{$aliases.FizzChosen}}' && '{{$aliases.BuzzChosen}}'"
```

`ConditionalTests`  
Optional\. A list of conditions, and the corresponding test cases that are run when each condition is satisfied\. Each condition can have multiple test cases; however, you can assign a given test case to only one condition\.  
By default, IDT runs any test case that isn't assigned to a condition in this list\. If you don't specify this section, IDT runs all test groups in the test suite\.  
Each item in the `ConditionalTests` list includes the following parameters:    
`Condition`  
A context expression that evaluates to a Boolean value\. If the evaluated value is true, IDT runs the test cases that are specified in the `Tests` parameter\.  
`Tests`  
The list of test descriptors\.   
Each test descriptor uses the test group ID and one or more test case IDs to identify the individual tests to run from a specific test group\. The test descriptor uses the following format:  

```
GroupId: group-id
CaseIds: [test-id, test-id] # optional
```

**Example**  
The following example uses generic context expressions that you can define as `Aliases`\.  

```
ConditionalTests:
    - Condition: "{{$aliases.Condition1}}"
      Tests:
          - GroupId: A
          - GroupId: B
    - Condition: "{{$aliases.Condition2}}"
      Tests:
          - GroupId: D
    - Condition: "{{$aliases.Condition1}} || {{$aliases.Condition2}}"
      Tests:
          - GroupId: C
```

Based on the defined conditions, IDT selects test groups as follows:
+ If `Condition1` is true, IDT runs the tests in test groups A, B, and C\.
+ If `Condition2` is true, IDT runs the tests in test groups C and D\.

`Order`  
Optional\. The order in which to run tests\. You specify the test order at the test group level\. If you don't specify this section, IDT runs all applicable test groups in a random order\. The value of `Order` is a list of group descriptor lists\. Any test group that you don't list in `Order`, can be run in parallel with any other listed test group\.  

Each group descriptor list contains one of more group descriptors, and identifies the order in which to run the groups that are specified in each descriptor\. You can use the following formats to define individual group descriptors:
+ `group-id`—The group ID of an existing test group\.
+ `[group-id, group-id]`—List of test groups that can be run in any order relative to each other\.
+ `"*"`—Wildcard\. This is equivalent to the list of all test groups that are not already specified in the current group descriptor list\.

The value for `Order` must also meet the following requirements:
+ Test group IDs that you specify in a group descriptor must exist in your test suite\. 
+ Each group descriptor list must include at least one test group\.
+ Each group descriptor list must contain unique group IDs\. You cannot repeat a test group ID within individual group descriptors\.
+ A group descriptor list can have at most one wildcard group descriptor\. The wildcard group descriptor must be the first or the last item in the list\.

**Examples**  
For a test suite that contains test groups A, B, C, D, and E, the following list of examples shows different ways to specify that IDT should first run test group A, then run test group B, and then run test groups C, D, and E in any order\.  
+ 

  ```
  Order:
      - - A
        - B
        - [C, D, E]
  ```
+ 

  ```
  Order:
      - - A
        - B
        - "*"
  ```
+ 

  ```
  Order:
      - - A
        - B
      
      - - B
        - C
      
      - - B
        - D
      
      - - B
        - E
  ```

`Features`  
Optional\. The list of product features that you want IDT to add to the `awsiotdevicetester_report.xml` file\. If you don't specify this section, IDT won't add any product features to the report\.  
A product feature is user\-defined information about specific criteria that a device might meet\. For example, the MQTT product feature can designate that the device publishes MQTT messages properly\. In `awsiotdevicetester_report.xml`, product features are set as `supported`, `not-supported`, or a custom user\-defined value, based on whether specified tests passed\.  
Each item in the `Features` list consists of the following parameters:    
`Name`  
The name of the feature\.  
`Value`  
Optional\. The custom value that you want to use in the report instead of `supported`\. If this value is not specified, then based IDT sets the feature value to `supported` or `not-supported` based on test results\. If you test the same feature with different conditions, you can use a custom value for each instance of that feature in the `Features` list, and IDT concatenates the feature values for supported conditions\. For more information, see   
`Condition`  
A context expression that evaluates to a Boolean value\. If the evaluated value is true, IDT adds the feature to the test report after it finishes running the test suite\. If the evaluated value is false, the test is not included in the report\.   
`Tests`  
Optional\. The list of test descriptors\. All of the tests that are specified in this list must pass for the feature to be supported\.   
Each test descriptor in this list uses the test group ID and one or more test case IDs to identify the individual tests to run from a specific test group\. The test descriptor uses the following format:  

```
GroupId: group-id
CaseIds: [test-id, test-id] # optional
```
You must specify either `Tests` or `OneOfTests` for each feature in the `Features` list\.  
`OneOfTests`  
Optional\. The list of test descriptors\. At least one of the tests that are specified in this list must pass for the feature to be supported\.  
Each test descriptor in this list uses the test group ID and one or more test case IDs to identify the individual tests to run from a specific test group\. The test descriptor uses the following format:  

```
GroupId: group-id
CaseIds: [test-id, test-id] # optional
```
You must specify either `Tests` or `OneOfTests` for each feature in the `Features` list\.  
`IsRequired`  
The boolean value that defines whether the feature is required in the test report\. The default value is `false`\.

## Test orchestrator context<a name="idt-test-orchestrator-context"></a>

The test orchestrator context is a read\-only JSON document that contains data that is available to the test orchestrator during execution\. The test orchestrator context is accessible only from the test orchestrator, and contains information that determines the test flow\. For example, you can use information configured by test runners in the `userdata.json` file to determine whether a specific test is required to run\.

The test orchestrator context uses the following format:

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
    }
}
```

`pool`  
Information about the device pool selected for the test run\. For a selected device pool, this information is retrieved from the corresponding top\-level device pool array element defined in the `device.json` file\.

`userData`  
Information in the `userdata.json` file\.

`config`  
Information in the `config.json` file\.

You can query the context using JSONPath notation\. The syntax for JSONPath queries in state definitions is `{{query}}`\. When you access data from the test orchestrator context, make sure that each value evaluates to a string, a number, or a Boolean\.

For more information about using JSONPath notation to access data from the context, see [Use the IDT context](idt-context.md)\.