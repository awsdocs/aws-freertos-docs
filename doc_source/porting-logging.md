# Logging<a name="porting-logging"></a>

Amazon FreeRTOS provides a thread\-safe logging task that can be used by calling the `configPRINTF` function\. `configPRINTF` is designed to behave like `printf`\. To port `configPRINTF`, initialize your communications peripheral, and define the `configPRINT_STRING` macro so that it takes an input string and displays it on your preferred output\.

## Logging Configuration<a name="porting-config"></a>

`configPRINT_STRING` should be defined for your board’s implementation of logging\. Current examples use a UART serial terminal, but other interfaces can also be used\.

```
#define configPRINT_STRING( x )
```

Use `configLOGGING_MAX_MESSAGE_LENGTH` to set the maximum number of bytes to be printed\. Messages longer than this length are truncated\.

```
#define configLOGGING_MAX_MESSAGE_LENGTH
```

When `configLOGGING_INCLUDE_TIME_AND_TASK_NAME` is set to 1, all printed messages are prepended with additional debug information \(the message number, FreeRTOS tick count, and task name\)\.

```
#define configLOGGING_INCLUDE_TIME_AND_TASK_NAME    1
```

`vLoggingPrintf` is the name of the FreeRTOS thread\-safe `printf` call\. You do not need to change this value to use AmazonFreeRTOS logging\.

```
#define configPRINTF( X )    vLoggingPrintf X
```