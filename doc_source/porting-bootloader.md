# Bootloader<a name="porting-bootloader"></a>

The bootloader must be dual\-bank capable and include logic for checking a CRC and app version in the image header\. The bootloader boots the newest image, based on the app version in the header, provided that the CRC is valid\. If the CRC check fails, the bootloader should zero out the header as an optimization for future reboots\.

Since the OTA v1 agent performs cryptographic signature verification, we suggest that v1 bootloaders not link to cryptographic code, so as to be as small as possible\. You must provide a compliant bootloader\.