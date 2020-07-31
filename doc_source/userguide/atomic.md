# Atomic operations<a name="atomic"></a>

## Overview<a name="freertos-atomic-overview"></a>

[ Atomic operations](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/platform/iot__atomic__generic_8h.html) ensure non\-blocking synchronization in concurrent programming\. You can use atomic operations to solve performance issues that are caused by asynchronous operations that act on shared memory\. FreeRTOS supports atomic operations, as implemented in the `iot_atomic.h` header file\.

The `iot_atomic.h` header file includes two implementations for atomic operations:
+ Disabling interrupt globally\.

  This implementation is available to all FreeRTOS platforms\.
+ ISA native atomic support\.

  This implementation is only available to platforms that compile with GCC, version 4\.7\.0 and higher, and have ISA atomic support\. For information about GCC built\-in functions, see [Built\-in Functions for Memory Model Aware Atomic Operations](https://gcc.gnu.org/onlinedocs/gcc/_005f_005fatomic-Builtins.html)\.

## Initialization<a name="freertos-atomic-initialization"></a>

Before you use FreeRTOS Atomic Operations, you need to choose which implementation of atomic operations that you want to use\.

1. Open [`FreeRTOSConfig.h`](dev-guide-freertos-kernel.md#freertos-config) configuration file for edit\.

1. For the ISA native atomic support implementation, define and set the `configUSE_ATOMIC_INSTRUCTION` variable to `1`\.

   For the disabling global interrupt implementation, undefine or clear `configUSE_ATOMIC_INSTRUCTION`\.

## API reference<a name="freertos-atomic-api"></a>

For a full API reference, see [ Atomic Operations C SDK API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/c-sdk/platform/iot__atomic__generic_8h.html)\.