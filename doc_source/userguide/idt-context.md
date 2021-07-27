# Use the IDT context<a name="idt-context"></a>

When IDT runs a test suite, the test suite can access a set of data that can be used to determine how each test runs\. This data is called the IDT context\. For example, user data configuration provided by test runners in a `userdata.json` file is made available to test suites in the IDT context\. 

The IDT context can be considered a read\-only JSON document\. Test suites can retrieve data from and write data to the context using standard JSON data types like objects, arrays, numbers and so on\.

## Context schema<a name="idt-context-schema"></a>

The IDT context uses the following format:

```
{
    "config": {
        <config-json-content>
        "timeoutMultiplier": timeout-multiplier
    },
    "device": {
        <device-json-device-element>
    },
    "devicePool": {
        <device-json-pool-element>
    },
    "resource": {
        "devices": [
            {
                <resource-json-device-element>
                "name": "<resource-name>"
            }
        ]
    },
    "testData": {
        "awsCredentials": {
            "awsAccessKeyId": "<access-key-id>",
            "awsSecretAccessKey": "<secret-access-key>",
            "awsSessionToken": "<session-token>"
        },
        "logFilePath": "/path/to/log/file"
    },
    "userData": {
        <userdata-json-content>
    }
}
```

**`config`**  
Information from the [`config.json` file](set-config-custom.md#config-json-custom)\. The `config` field also contains the following additional field:    
**`config.timeoutMultiplier`**  
The multiplier for the any timeout value used by the test suite\. This value is specified by the test runner from the IDT CLI\. The default value is `1`\.

**`device`**  
Information about the device selected for the test run\. This information is equivalent to the `devices` array element in the [`device.json` file](set-config-custom.md#device-config-custom) for the selected device\.

**`devicePool`**  
Information about the device pool selected for the test run\. This information is equivalent to the top\-level device pool array element defined in the `device.json` file for the selected device pool\.

**`resource`**  
Information about resource devices from the `resource.json` file\.    
**`resource.devices`**  
This information is equivalent to the `devices` array defined in the `resource.json` file\. Each `devices` element includes the following additional field:    
**`resource.device.name`**  
The name of the resource device\. This value is set to the `requiredResource.name` value in the `test.json` file\.

**`testData.awsCredentials`**  
The AWS credentials used by the test to connect to the AWS cloud\. This information is obtained from the `config.json` file\.

**`testData.logFilePath`**  
The path to the log file to which the test case writes log messages\. The test suite creates this file if it doesn't exist\. 

**`userData`**  
Information provided by the test runner in the [`userdata.json` file](set-config-custom.md#userdata-config-custom)\.

## Access data in the context<a name="accessing-context-data"></a>

You can query the context using JSONPath notation from your JSON files and from your text executable with the `GetContextValue` and `GetContextString` APIs\. The syntax for JSONPath strings to access the IDT context varies as follows:
+ In `suite.json` and `test.json`, you use `{{query}}`\. That is, do not use the root element `$.` to start your expression\.
+ In `statemachine.json`, you use `{{$.query}}`\.
+ In API commands, you use `query` or `{{$.query}}`, depending on the command\. For more information, see the inline documentation in the SDKs\. 

The following table describes the operators in a typical foobar JSONPath expression:


| Operator  | Description  | 
| --- | --- | 
| $ | The root element\. Because the top\-level context value for IDT is an object, you will typically use $\. to start your queries\. | 
| \.childName | Accesses the child element with name childName from an object\. If applied to an array, yields a new array with this operator applied to each element\. The element name is case sensitive\. For example, the query to access the awsRegion value in the config object is $\.config\.awsRegion\. | 
| \[start:end\] | Filters elements from an array, retrieving items beginning from the start index and going up to the end index, both inclusive\. | 
| \[index1, index2, \.\.\. , indexN\] | Filters elements from an array, retrieving items from only the specified indices\. | 
| \[?\(expr\)\] | Filters elements from an array using the expr expression\. This expression must evaluate to a boolean value\. | 

To create filter expressions, use the following syntax:

```
<jsonpath> | <value> operator <jsonpath> | <value> 
```

In this syntax: 
+ `jsonpath` is a JSONPath that uses standard JSON syntax\. 
+ `value` is any custom value that uses standard JSON syntax\.
+ `operator` is one of the following operators:
  + `<` \(Less than\)
  + `<=` \(Less than or equal to\)
  + `==` \(Equal to\)

    If the JSONPath or value in your expression is an array, boolean, or object value, then this is the only supported binary operator that you can use\.
  + `>=` \(Greater than or equal to\)
  + `>` \(Greater than\)
  + `=~` \(Regular expression match\)\. To use this operator in a filter expression, the JSONPath or value on the left side of your expression must evaluate to a string and the right side must be a pattern value that follows the [RE2 syntax](https://github.com/google/re2/wiki/Syntax)\.

You can use JSONPath queries in the form \{\{*query*\}\} as placeholder strings within the `args` and `environmentVariables` fields in `test.json` files and within the `environmentVariables` fields in `suite.json` files\. IDT performs a context lookup and populates the fields with the evaluated value of the query\. For example, in the `suite.json` file, you can use placeholder strings to specify environment variable values that change with each test case and IDT will populate the environment variables with the correct value for each test case\. However, when you use placeholder strings in `test.json` and `suite.json` files, the following considerations apply for your queries:
+ You must each occurrence of the `devicePool` key in your query in all lower case\. That is, use `devicepool` instead\.
+ For arrays, you can use only arrays of strings\. In addition, arrays use a non\-standard `item1, item2,...,itemN` format\. If the array contains only one element, then it is serialized as `item`, making it indistinguishable from a string field\. 
+ You cannot use placeholders to retrieve objects from the context\.

Because of these considerations, we recommend that whenever possible, you use the API to access the context in your test logic instead of placeholder strings in `test.json` and `suite.json` files\. However, in some cases it might be more convenient to use JSONPath placeholders to retrieve single strings to set as environment variables\. 