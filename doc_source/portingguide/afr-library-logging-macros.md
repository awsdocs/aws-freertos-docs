# Implementing the library logging macros<a name="afr-library-logging-macros"></a>

The FreeRTOS libraries use the following logging macros, listed in increasing order of verbosity\.
+ `LogError`
+ `LogWarn`
+ `LogInfo`
+ `LogDebug`

A definition for all the macros must be provided\. The recommendations are: 
+ Macros should support `C89` style logging\.
+ Logging should be thread safe\. Log lines from multiple tasks must not interleave with each other\.
+ Logging APIs must not block, and must free application tasks from blocking on I/O\.

Refer to the [Logging Functionality](https://www.freertos.org/logging.html) on FreeRTOS\.org for implementation specifics\. You can see an implementation in this [example](https://github.com/FreeRTOS/lab-iot-reference-nxp-rt1060/tree/main/examples/common/logging)\.

## Testing<a name="testing-logging"></a>
+ Run a test with multiple tasks to verify logs do not interleave\.
+ Run a test to verify that the logging APIs do not block on I/O\.
+ Test logging macros with various standards, such as `C89,C99` style logging\.
+ Test logging macros by setting different log levels, such as `Debug`, `Info`, `Error`, and `Warning`\.