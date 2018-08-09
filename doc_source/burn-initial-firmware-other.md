# Install the Initial Version of Firmware on a custom board<a name="burn-initial-firmware-other"></a>

Using your IDE build the aws\_demo project, making sure to include the OTA library\. For more information about the structure of the Amazon FreeRTOS source code, see [Navigating the Demo Applications](freertos-mds-projects-struct.md)

Make sure to include your code signing certificate, private key, and certificate trust chain either in Amazon FreeRTOS project or on your device\.

Using the appropriate tool, burn the application onto your board and make sure it is running correctly\.