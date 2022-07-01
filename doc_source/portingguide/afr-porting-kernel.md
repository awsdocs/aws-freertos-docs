# Configuring a FreeRTOS kernel port<a name="afr-porting-kernel"></a>

This section provides instructions for integrating a port of the FreeRTOS kernel into a FreeRTOS port\-testing project\. For a list of available kernel ports, see [FreeRTOS kernel ports](https://freertos.org/RTOS_ports.html)\.

FreeRTOS uses the FreeRTOS kernel for multitasking and intertask communications\. For more information, see the [FreeRTOS kernel fundamentals](https://docs.aws.amazon.com/freertos/latest/userguide/dev-guide-freertos-kernel.html) in the *FreeRTOS User Guide* and [FreeRTOS\.org](https://freertos.org/index.html)\. 

**Note**  
Porting the FreeRTOS kernel to a new architecture is not included in this documentation\. If you are interested, [contact the FreeRTOS engineering team](https://freertos.org/RTOS-contact-and-support.html)\.  
For the FreeRTOS Qualification program, only existing FreeRTOS kernel ports are supported\. Modifications to these ports are not accepted within the program\. Review the [ FreeRTOS kernel port policy](https://freertos.org/differences-between-officially-supported-and-contributed-FreeRTOS-code.html) for more information\.

## Prerequisites<a name="porting-prereqs-kernel"></a>

To set up the FreeRTOS kernel for porting, you need the following:
+ An official FreeRTOS kernel port, or FreeRTOS supported ports for the target platform\.
+ An IDE project that includes the correct FreeRTOS kernel port files for the target platform and compiler\. For information about setting up a test project, see [Setting up your workspace and project for porting](porting-set-up-project.md)\.

## Configuring the FreeRTOS kernel<a name="porting-steps-kernel"></a>

FreeRTOS kernel is customized using a configuration file called `FreeRTOSConfig.h`\. This file specifies application\-specific configuration settings for the kernel\. For a description of each configuration option, see [Customization](https://freertos.org/a00110.html) on FreeRTOS\.org\.

To configure the FreeRTOS kernel to work with your device, include `FreeRTOSConfig.h`, and modify any additional FreeRTOS configurations\.

For a description of each configuration option, see [Customization](https://freertos.org/a00110.html) configurations on FreeRTOS\.org\.

## Testing<a name="porting-testing-kernel"></a>
+ Run a simple FreeRTOS task to log a message to serial output console\.
+ Verify that the message outputs to console as expected\.