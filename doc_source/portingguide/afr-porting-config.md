# Implementing the `configPRINT_STRING()` macro<a name="afr-porting-config"></a>

You must implement the `configPRINT_STRING()` macro before you port the Amazon FreeRTOS libraries\. Amazon FreeRTOS uses `configPRINT_STRING()` to output test results as human\-readable ASCII strings\.

## Prerequisites<a name="porting-prereqs-config"></a>

To implement the `configPRINT_STRING()` macro, you need the following:
+ A development board that supports UART or virtual COM port output\.
+ An Amazon FreeRTOS project configured for your platform, and a porting\-test IDE project\.

  For information, see [Setting Up Your Amazon FreeRTOS Source Code for Porting](porting-set-up-project.md)\.

## Implementation<a name="porting-steps-config"></a>

**To implement `configPRINT_STRING()`**

1. Connect your device to a terminal emulator to output test results\.

1. Open the file `<amazon-freertos>/tests/<vendor>/<board>/common/application_code/main.c`, and locate the call to `configPRINT_STRING("Test Message")` in the `prvMiscInitialization()` function\.

1. Immediately before the call to `configPRINT_STRING("Test Message")`, add code that uses the vendor\-supplied UART driver to initialize the UART baud rate level to 115200\.

1. Open `<amazon-freertos>/tests/<vendor>/<board>/common/config_files/FreeRTOSConfig.h`, and locate the empty definition of `configPRINT_STRING()`\. The macro takes a NULL\-terminated ASCII C string as its only parameter\.

1. Update the empty definition of `configPRINT_STRING()` so that it calls the vendor\-supplied UART output function\.

   For example, suppose the UART output function has the following prototype:

   ```
   void MyUARTOutput( char *DataToOutput, size_t LengthToOutput );
   ```

   You would implement `configPRINT_STRING()` as:

   ```
   #define configPRINT_STRING( X ) MyUARTOutput( (X), strlen( (X) ) )
   ```

## Testing<a name="porting-testing-config"></a>

Build and execute the test demo project\. If Test Message appears in the UART console, then the console is connected and configured correctly, `configPRINT_STRING()` is behaving properly, and testing is complete\. You can remove the call to `configPRINT_STRING("Test Message")` from `prvMiscInitialization()`\.

After you implement the `configPRINT_STRING()` macro, you can start configuring a FreeRTOS kernel port for your device\. See [Configuring a FreeRTOS Kernel Port](afr-porting-kernel.md) for instructions\.