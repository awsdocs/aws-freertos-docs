# Update the Version of Your Firmware<a name="dg-ota-update-firmware"></a>

The OTA agent included with FreeRTOS checks the version of any update and installs it only if it is more recent than the existing firmware version\. The following steps show you how to increment the firmware version of the OTA demo application\.

1. Open the `aws_demos` project in your IDE\.

1. Open `demos/include/aws_application_version.h` and increment the `APP_VERSION_BUILD` token value\.

1. If you are using the Microchip Curiosity PIC32MZEF, increment the OTA sequence number in `vendors/microchip/boards/curiosity_pic32mzef/bootloader/bootloader/utility/user-config/ota-descriptor.config`\. The OTA sequence number should be incremented for every new OTA image generated\.

1. Rebuild the project\.

You must copy your firmware update into the Amazon S3 bucket that you created as described in [Create an Amazon S3 Bucket to Store Your Update](dg-ota-bucket.md)\. The name of the file you need to copy to Amazon S3 depends on the hardware platform you are using: 
+ Texas Instruments CC3220SF\-LAUNCHXL: `vendors/ti/boards/cc3220_launchpad/aws_demos/ccs/debug/aws_demos.bin`
+ Microchip Curiosity PIC32MZEF: `vendors/microchip/boards/curiosity_pic32mzef/aws_demos/mplab/dist/pic32mz_ef_curiosity/production/mplab.production.ota.bin`
+ Espressif ESP32: `vendors/espressif/boards/esp32/aws_demos/make/build/aws_demos.bin`