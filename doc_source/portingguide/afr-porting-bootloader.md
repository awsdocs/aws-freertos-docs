# Porting the Bootloader Demo<a name="afr-porting-bootloader"></a>

Amazon FreeRTOS includes a demo bootloader application for the Microchip Curiosity PIC32MZEF platform\. For more information, see [Demo Bootloader for the Microchip Curiosity PIC32MZEF](https://docs.aws.amazon.com/freertos/latest/userguide/microchip-bootloader.html) in the Amazon FreeRTOS User Guide\. You can port this demo to other platforms\.

If you choose not to port the demo to your platform, you can use your own bootloader application\. To support OTA functionality, the application must meet the following requirements:
+ The bootloader is stored in non\-volatile memory so it cannot be overwritten\.
+ The bootloader can verify the cryptographic signature of the downloaded application image\.

  The signature verification must be consistent with the OTA image signer\.
+ The bootloader does not allow rolling back to a previously installed application image\.
+ The bootloader maintains at least one image that can be booted\.
+ The bootloader supports self\-testing, new OTA images\.

  If test execution fails, the bootloader rolls back to the previous valid image\. If test execution succeeds, the image is marked as valid and the previous version is erased\.
+ If the MCU contains more than one image, the newest image is executed\.

  The newest version can be determined based on implementation \(for example a user defined sequence number, application version, and so on\)\. As per other requirements, this can only be the case until a newer version has been verified and proven functional\.
+ If the MCU cannot verify any images, it enters a controlled, benign state\. In this state, it prevents itself from being taken over by ensuring no actions are performed\.

**Note**  
These requirements must be met, even in the presence of an accidental or malicious write to any MCU memory location, such as key store, program memory, or RAM\.  
The state of the bootloader application must be set by the OTA PAL\.