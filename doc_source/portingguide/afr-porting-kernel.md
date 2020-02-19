# Configuring a FreeRTOS Kernel Port<a name="afr-porting-kernel"></a>

This section provides instructions for integrating a port of the FreeRTOS kernel into a FreeRTOS port\-testing project\. For a list of available kernel ports, see [FreeRTOS Kernel Ports](https://freertos.org/RTOS_ports.html)\.

FreeRTOS uses the FreeRTOS kernel for multitasking and inter\-task communications\. For more information, see the [FreeRTOS Kernel Fundamentals](https://docs.aws.amazon.com/freertos/latest/userguide/dev-guide-freertos-kernel.html) in the FreeRTOS User Guide and [FreeRTOS\.org](https://freertos.org/index.html)\. 

**Note**  
Porting the FreeRTOS kernel to a new architecture is out of the scope of this documentation\. If you are interested in porting the FreeRTOS kernel to a new architecture, [contact the FreeRTOS engineering team](https://freertos.org/RTOS-contact-and-support.html)\.  
For the FreeRTOS Qualification program, only existing ports are supported\. Modifications to these ports are not accepted within the Qualification program\. Only the official ports that can be downloaded from [Github](https://github.com/aws/amazon-freertos) or [Sourceforge](https://sourceforge.net/projects/freertos/) are accepted\.

## Prerequisites<a name="porting-prereqs-kernel"></a>

To set up the FreeRTOS kernel for porting, you need the following:
+ An official FreeRTOS kernel port for the target platform\.
+ An IDE project or `CMakeLists.txt` list file that includes the correct FreeRTOS kernel port files for the target platform and compiler\.

  For information about setting up a test project, see [Setting Up Your FreeRTOS Source Code for Porting](porting-set-up-project.md)\.
+ An implementation of the `configPRINT_STRING()` macro for your device\.

  For information about implementing `configPRINT_STRING()`, see [Implementing the `configPRINT_STRING()` macro](afr-porting-config.md)\.

## Configuring the FreeRTOS Kernel<a name="porting-steps-kernel"></a>

The header file `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/FreeRTOSConfig.h` specifies application\-specific configuration settings for the FreeRTOS kernel\. For a description of each configuration option, see [Customisation](https://freertos.org/a00110.html) on FreeRTOS\.org\.

To configure the FreeRTOS kernel to work with your device, open `FreeRTOSConfig.h`, and verify that the configuration options in the following table are correctly specified for your platform\.


| Configuration option | Description | 
| --- | --- | 
|  `configCPU_CLOCK_HZ`  |  Specifies the frequency of the clock used to generate the tick interrupt\.  | 
|  `configMINIMAL_STACK_SIZE`  |  Specifies the minimum stack size\. As a starting point, this can be set to the value used in the official FreeRTOS demo for the FreeRTOS kernel port in use\. Official FreeRTOS demos are those distributed from the FreeRTOS\.org website\. Make sure that [stack overflow checking](https://www.freertos.org/Stacks-and-stack-overflow-checking.html) is set to 2, and increase `configMINIMAL_STACK_SIZE` if overflows occur\. To save RAM, set stack sizes to the minimum value that does not result in a stack overflow\.  | 
|  `configTOTAL_HEAP_SIZE`  |  Sets the size of the [FreeRTOS heap](https://www.freertos.org/a00111.html)\. Like task stack sizes, the heap size can be tuned to ensure unused heap space does not consume RAM\.  | 

**Note**  
If you are porting ARM Cortex\-M3, M4, or M7 devices, you must also specify [`configPRIO_BITS` and `configMAX_SYSCALL_INTERRUPT_PRIORITY`](https://www.freertos.org/RTOS-Cortex-M3-M4.html) correctly\.

## Testing<a name="porting-testing-kernel"></a>

1. Open `/libraries/freertos_plus/standard/utils/src/iot_system_init.c`, and comment out the lines that call `BUFFERPOOL_Init()`, `MQTT_AGENT_Init()`, and `SOCKETS_Init()` from within function `SYSTEM_Init()`\. These initialization functions belong to libraries that you haven't ported yet\. The porting sections for those libraries include instructions to uncomment these functions\.

1. Build the test project, and then flash it to your device for execution\.

1. If "\." appears in the UART console every 5 seconds, then the FreeRTOS kernel is configured correctly, and testing is complete\.

   Open `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/FreeRTOSConfig.h`, and set `configUSE_IDLE_HOOK` to 0 to stop the kernel from executing `vApplicationIdleHook()` and outputting "`.`"\.

1. If "`.`" appears at any frequency other than 5 seconds, open `FreeRTOSConfig.h` and verify that `configCPU_CLOCK_HZ` is set to the correct value for your board\.

After you have configured the FreeRTOS kernel port for your device, you can start porting the Wi\-Fi library\. See [Porting the Wi\-Fi Library](afr-porting-wifi.md) for instructions\.