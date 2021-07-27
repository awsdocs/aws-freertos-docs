# Update the version of your firmware<a name="dg-ota-update-firmware"></a>

The OTA Agent included with FreeRTOS checks the version of any update and installs it only if it is more recent than the existing firmware version\. The following steps show you how to increment the firmware version of the OTA demo application\.

1. Open the `aws_demos` project in your IDE\.

1. Open `demos/include/aws_application_version.h` and increment the `APP_VERSION_BUILD` token value\.

1. To schedule an update to a Renesas rx65n platform with a filetype other than 0 \(non\-firmware files\), you must sign the file with the Renesas Secure Flash Programmer tool, otherwise it will fail the signature check on the device\. The tool creates a signed file package with the extension `.rsu` which is a proprietery file type for Renesas\. The tool can be found on [Github](https://github.com/renesas/Amazon-FreeRTOS-Tools)\. You can use the following example command to generate the image:

   ```
   "Renesas Secure Flash Programmer.exe" CUI Update "RX65N(ROM 2MB)/Secure Bootloader=256KB" "sig-sha256-ecdsa" 1 "file_name" "output_file_name.rsu"
   ```

1. Rebuild the project\.

You must copy your firmware update into the Amazon S3 bucket that you created as described in [Create an Amazon S3 bucket to store your update](dg-ota-bucket.md)\. The name of the file you need to copy to Amazon S3 depends on the hardware platform you are using: 
+ Texas Instruments CC3220SF\-LAUNCHXL: `vendors/ti/boards/cc3220_launchpad/aws_demos/ccs/debug/aws_demos.bin`
+ Espressif ESP32: `vendors/espressif/boards/esp32/aws_demos/make/build/aws_demos.bin`