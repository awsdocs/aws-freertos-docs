# Install the initial version of firmware on the Nordic nRF52840 DK<a name="burn-initial-firmware-nordic"></a>

This guide is written with the assumption that you have already performed the steps in [Getting started with the Nordic nRF52840\-DK](getting_started_nordic.md) and [Over\-the\-Air Update Prerequisites](https://docs.aws.amazon.com/freertos/latest/userguide/ota-prereqs.html)\. Before you attempt an OTA update, you might want to run the MQTT demo project described in [Getting Started with FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-getting-started.html) to ensure that your board and toolchain are set up correctly\.

**To flash an initial factory image to the board**

1. Open `freertos/vendors/nordic/boards/nrf52840-dk/aws_demos/config_files/aws_demo_config.h`\.

1. Replace `#define CONFIG_MQTT_DEMO_ENABLED` with `#define democonfigOTA_UPDATE_DEMO_ENABLED`\.

1. With the OTA Update demo selected, follow the same steps outlined in [Getting started with the Nordic nRF52840\-DK](getting_started_nordic.md) to build and flash the image\.

   You should see output similar to the following\.

   ```
   9 1285 [OTA Task] [prvSubscribeToJobNotificationTopics] OK: $aws/things/your-thing-name/jobs/notify-next
   10 1286 [OTA Task] [OTA_CheckForUpdate] Request #0
   11 1289 [OTA Task] [prvParseJSONbyModel] Extracted parameter [ clientToken: 0:your-thing-name ]
   12 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: execution
   13 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: jobId
   14 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: jobDocument
   15 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: afr_ota
   16 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: streamname
   17 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: files
   18 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: filepath
   19 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: filesize
   20 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: fileid
   21 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: certfile
   22 1289 [OTA Task] [prvParseJSONbyModel] parameter not present: sig-sha256-ecdsa
   23 1289 [OTA Task] [prvParseJobDoc] Ignoring job without ID.
   24 1289 [OTA Task] [prvOTA_Close] Context->0x3ffbb4a8
   25 1290 [OTA] [OTA_AgentInit] Ready.
   26 1390 [OTA] State: Ready Received: 1 Queued: 1 Processed: 1 Dropped: 0
   27 1490 [OTA] State: Ready Received: 1 Queued: 1 Processed: 1 Dropped: 0
   28 1590 [OTA] State: Ready Received: 1 Queued: 1 Processed: 1 Dropped: 0
   29 1690 [OTA] State: Ready Received: 1 Queued: 1 Processed: 1 Dropped: 0
   ```

   Your board is now listening for OTA updates\.