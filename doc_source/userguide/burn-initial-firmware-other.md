# Install the initial version of firmware on a custom board<a name="burn-initial-firmware-other"></a>

Using your IDE, build the `aws_demos` project, making sure to include the OTA library\. For more information about the structure of the FreeRTOS source code, see [FreeRTOS demos](freertos-next-steps.md)\.

Make sure to include your code\-signing certificate, private key, and certificate trust chain either in the FreeRTOS project or on your device\.

Using the appropriate tool, burn the application onto your board and make sure it is running correctly\.