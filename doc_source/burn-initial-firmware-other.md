# Install the Initial Version of Firmware on a Custom Board<a name="burn-initial-firmware-other"></a>

Using your IDE, build the `aws_demos` project, making sure to include the OTA library\. For more information about the structure of the Amazon FreeRTOS source code, see [Amazon FreeRTOS Demos](freertos-next-steps.md)\.

Make sure to include your code\-signing certificate, private key, and certificate trust chain either in the Amazon FreeRTOS project or on your device\.

Using the appropriate tool, burn the application onto your board and make sure it is running correctly\.