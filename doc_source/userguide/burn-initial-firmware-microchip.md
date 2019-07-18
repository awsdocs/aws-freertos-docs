# Install the Initial Version of Firmware on the Microchip Curiosity PIC32MZEF<a name="burn-initial-firmware-microchip"></a>

These steps are written with the assumption that you have already built the `aws_demos` project, as described in [Download, Build, Flash, and Run the Amazon FreeRTOS OTA demo on the Microchip Curiosity PIC32MZEF](download-ota-mchip.md)\.<a name="mch-burn-demo"></a>

**To burn the demo application onto your board**

1. Rebuild the `aws_demos` project and make sure it compiles without errors\.

1. On the tool bar, choose ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/MakeAndProgram.png)\.

1. After the programming process is complete, disconnect the ICD 4 debugger and reset the board\. Reconnect your terminal emulator to make sure the output is the same as when you debugged the demo with MPLAB X IDE\.

   The terminal should display output similar to the following:

   ```
   Bootloader version 00.09.00
   [prvBOOT_Init] Watchdog timer initialized.
   [prvBOOT_Init] Crypto initialized.
   
   [prvValidateImage] Validating image at Bank : 0
   [prvValidateImage] No application image or magic code present at: 0xbd000000
   [prvBOOT_ValidateImages] Validation failed for image at 0xbd000000
   
   [prvValidateImage] Validating image at Bank : 1
   [prvValidateImage] No application image or magic code present at: 0xbd100000
   [prvBOOT_ValidateImages] Validation failed for image at 0xbd100000
   
   [prvBOOT_ValidateImages] Booting default image.
   
   
   >0 36246 [IP-task] vDHCPProcess: offer ac140a0eip
                                                    1 36297 [IP-task] vDHCPProcess: offer ac140a0eip
                    2 36297 [IP-task]
   
   IP Address: 172.20.10.14
   3 36297 [IP-task] Subnet Mask: 255.255.255.240
   4 36297 [IP-task] Gateway Address: 172.20.10.1
   5 36297 [IP-task] DNS Server Address: 172.20.10.1
   
   
   6 36299 [OTA] OTA demo version 0.9.2
   7 36299 [OTA] Creating MQTT Client...
   8 36299 [OTA] Connecting to broker...
   9 38673 [OTA] Connected to broker.
   10 38793 [OTA Task] [prvSubscribeToJobNotificationTopics] OK: $aws/things/devthingota/jobs/$next/get/accepted
   11 38863 [OTA Task] [prvSubscribeToJobNotificationTopics] OK: $aws/things/devthingota/jobs/notify-next
   12 38863 [OTA Task] [OTA_CheckForUpdate] Request #0
   13 38964 [OTA] [OTA_AgentInit] Ready.
   14 38973 [OTA Task] [prvParseJSONbyModel] Extracted parameter [ clientToken: 0:devthingota ]
   15 38973 [OTA Task] [prvParseJSONbyModel] parameter not present: execution
   16 38973 [OTA Task] [prvParseJSONbyModel] parameter not present: jobId
   17 38973 [OTA Task] [prvParseJSONbyModel] parameter not present: jobDocument
   18 38973 [OTA Task] [prvParseJSONbyModel] parameter not present: streamname
   19 38973 [OTA Task] [prvParseJSONbyModel] parameter not present: files
   20 38975 [OTA Task] [prvParseJSONbyModel] parameter not present: filepath
   21 38975 [OTA Task] [prvParseJSONbyModel] parameter not present: filesize
   22 38975 [OTA Task] [prvParseJSONbyModel] parameter not present: fileid
   23 38975 [OTA Task] [prvParseJSONbyModel] parameter not present: certfile
   24 38975 [OTA Task] [prvParseJSONbyModel] parameter not present: sig-sha256-ecdsa
   25 38975 [OTA Task] [prvParseJobDoc] Ignoring job without ID.
   26 38975 [OTA Task] [prvOTA_Close] Context->0x8003b620
   27 38975 [OTA Task] [prvPAL_Abort] Abort - OK
   28 39964 [OTA] State: Ready  Received: 1   Queued: 1   Processed: 1   Dropped: 0
   29 40964 [OTA] State: Ready  Received: 1   Queued: 1   Processed: 1   Dropped: 0
   30 41964 [OTA] State: Ready  Received: 1   Queued: 1   Processed: 1   Dropped: 0
   31 42964 [OTA] State: Ready  Received: 1   Queued: 1   Processed: 1   Dropped: 0
   32 43964 [OTA] State: Ready  Received: 1   Queued: 1   Processed: 1   Dropped: 0
   33 44964 [OTA] State: Ready  Received: 1   Queued: 1   Processed: 1   Dropped: 0
   34 45964 [OTA] State: Ready  Received: 1   Queued: 1   Processed: 1   Dropped: 0
   35 46964 [OTA] State: Ready  Received: 1   Queued: 1   Processed: 1   Dropped: 0
   36 47964 [OTA] State: Ready  Received: 1   Queued: 1   Processed: 1   Dropped: 0
   ```

The following procedure creates a unified hex file or factory image that consists of a reference bootloader and an application with a cryptographic signature\. The bootloader verifies the cryptographic signature of the application on boot and supports OTA updates\.

**To build and flash a factory image**

1. Make sure you have the SRecord tools installed from [Source Forge](http://srecord.sourceforge.net/)\. Verify that the directory that contains the `srec_cat` and `srec_info` programs is in your system path\.

1. Update the OTA sequence number and application version for the factory image\.

1. Build the `aws_demos` project\.

1. Run the `factory_image_generator.py` script to generate the factory image\.

   ```
   factory_image_generator.py -b mplab.production.bin -p MCHP-Curiosity-PIC32MZEF –k private_key.pem  -x aws_bootloader.X.production.hex
   ```

   This command takes the following parameters:
   + `mplab.production.bin`: The application binary\.
   + `MCHP-Curiosity-PIC32MZEF`: The platform name\.
   + `private_key.pem`: The code\-signing private key\.
   + `aws_bootloader.X.production.hex`: The bootloader hex file\.

   When you build the`aws_demos` project, the application binary image and bootloader hex file are built as part of the process\. Each project under the `vendors/microchip/boards/curiosity_pic32mzef/aws_demos/` directory contains a `dist/pic32mz_ef_curiosity/production/` directory that contains these files\. The generated unified hex file is named `mplab.production.unified.factory.hex`\.

1. Use the MPLab IPE tool to program the generated hex file onto the device\.

1. You can check that your factory image works by watching the board's UART output as the image is uploaded\. If everything is set up correctly, you should see the image boot successfully:

   ```
   [prvValidateImage] Validating image at Bank : 0
   [prvValidateImage] Valid magic code at: 0xbd000000
   [prvValidateImage] Valid image flags: 0xfc at: 0xbd000000
   [prvValidateImage] Addresses are valid.
   [prvValidateImage] Crypto signature is valid.
   [...]
   [prvBOOT_ValidateImages] Booting image with sequence number 1 at 0xbd000000
   ```

1. If your certificates are incorrectly configured or if an OTA image is not properly signed, you might see messages like the following before the chip's bootloader erases the invalid update\. Check that your code\-signing certificates are consistent and review the previous steps carefully\.

   ```
   [prvValidateImage] Validating image at Bank : 0
   [prvValidateImage] Valid magic code at: 0xbd000000
   [prvValidateImage] Valid image flags: 0xfc at: 0xbd000000
   [prvValidateImage] Addresses are valid.
   [prvValidateImage] Crypto signature is not valid.
   [prvBOOT_ValidateImages] Validation failed for image at 0xbd000000
   [BOOT_FLASH_EraseBank] Bank erased at : 0xbd000000
   ```