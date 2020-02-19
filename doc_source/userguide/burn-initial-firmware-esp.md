# Install the Initial Version of Firmware on the Espressif ESP32<a name="burn-initial-firmware-esp"></a>

This guide is written with the assumption that you have already performed the steps in [Getting Started with the Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html) and [Over\-the\-Air Update Prerequisites](https://docs.aws.amazon.com/freertos/latest/userguide/ota-prereqs.html)\. Before you attempt an OTA update, you might want to run the MQTT demo project described in [Getting Started with FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-getting-started.html) to ensure that your board and tool chain are set up correctly\.

**To flash an initial factory image to the board**

1.  Open `<freertos>/vendors/<vendor>/boards/<board>/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_MQTT_DEMO_ENABLED`, and define `CONFIG_OTA_UPDATE_DEMO_ENABLED`\.

1. Copy your SHA\-256/ECDSA PEM\-formatted code\-signing certificate that you generated in the [OTA Update Prerequisites](ota-prereqs.md) to `demos/include/aws_ota_codesigner_certificate.h`\. It should be formatted in following way\.

   ```
   static const char signingcredentialSIGNING_CERTIFICATE_PEM[] = "-----BEGIN CERTIFICATE-----\n"
      ...base64 data...\n"
      -----END CERTIFICATE-----\n";
   ```

1. With the OTA Update demo selected, follow the same steps outlined in [Getting Started with ESP32](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html) to build and flash the image\. If you have previously built and flashed the project, you might need to run `make clean` first\. After you run `make flash monitor`, you should see something like the following\. The ordering of some messages might vary, because the demo application runs multiple tasks at once\.

   ```
   I (28) boot: ESP-IDF v3.1-dev-322-gf307f41-dirty 2nd stage bootloader
   I (28) boot: compile time 16:32:33
   I (29) boot: Enabling RNG early entropy source...
   I (34) boot: SPI Speed : 40MHz
   I (38) boot: SPI Mode : DIO
   I (42) boot: SPI Flash Size : 4MB
   I (46) boot: Partition Table:
   I (50) boot: ## Label Usage Type ST Offset Length
   I (57) boot: 0 nvs WiFi data 01 02 00010000 00006000
   I (64) boot: 1 otadata OTA data 01 00 00016000 00002000
   I (72) boot: 2 phy_init RF data 01 01 00018000 00001000
   I (79) boot: 3 ota_0 OTA app 00 10 00020000 00100000
   I (87) boot: 4 ota_1 OTA app 00 11 00120000 00100000
   I (94) boot: 5 storage Unknown data 01 82 00220000 00010000
   I (102) boot: End of partition table
   I (106) esp_image: segment 0: paddr=0x00020020 vaddr=0x3f400020 size=0x14784 ( 83844) map
   I (144) esp_image: segment 1: paddr=0x000347ac vaddr=0x3ffb0000 size=0x023ec ( 9196) load
   I (148) esp_image: segment 2: paddr=0x00036ba0 vaddr=0x40080000 size=0x00400 ( 1024) load
   I (151) esp_image: segment 3: paddr=0x00036fa8 vaddr=0x40080400 size=0x09068 ( 36968) load
   I (175) esp_image: segment 4: paddr=0x00040018 vaddr=0x400d0018 size=0x719b8 (465336) map
   I (337) esp_image: segment 5: paddr=0x000b19d8 vaddr=0x40089468 size=0x04934 ( 18740) load
   I (345) esp_image: segment 6: paddr=0x000b6314 vaddr=0x400c0000 size=0x00000 ( 0) load
   I (353) boot: Loaded app from partition at offset 0x20000
   I (353) boot: ota rollback check done
   I (354) boot: Disabling RNG early entropy source...
   I (360) cpu_start: Pro cpu up.
   I (363) cpu_start: Single core mode
   I (368) heap_init: Initializing. RAM available for dynamic allocation:
   I (375) heap_init: At 3FFAE6E0 len 00001920 (6 KiB): DRAM
   I (381) heap_init: At 3FFC0748 len 0001F8B8 (126 KiB): DRAM
   I (387) heap_init: At 3FFE0440 len 00003BC0 (14 KiB): D/IRAM
   I (393) heap_init: At 3FFE4350 len 0001BCB0 (111 KiB): D/IRAM
   I (400) heap_init: At 4008DD9C len 00012264 (72 KiB): IRAM
   I (406) cpu_start: Pro cpu start user code
   I (88) cpu_start: Starting scheduler on PRO CPU.
   I (113) wifi: wifi firmware version: f79168c
   I (113) wifi: config NVS flash: enabled
   I (113) wifi: config nano formating: disabled
   I (113) system_api: Base MAC address is not set, read default base MAC address from BLK0 of EFUSE
   I (123) system_api: Base MAC address is not set, read default base MAC address from BLK0 of EFUSE
   I (133) wifi: Init dynamic tx buffer num: 32
   I (143) wifi: Init data frame dynamic rx buffer num: 32
   I (143) wifi: Init management frame dynamic rx buffer num: 32
   I (143) wifi: wifi driver task: 3ffc73ec, prio:23, stack:4096
   I (153) wifi: Init static rx buffer num: 10
   I (153) wifi: Init dynamic rx buffer num: 32
   I (163) wifi: wifi power manager task: 0x3ffcc028 prio: 21 stack: 2560
   0 6 [main] WiFi module initialized. Connecting to AP <Your_WiFi_SSID>...
   I (233) phy: phy_version: 383.0, 79a622c, Jan 30 2018, 15:38:06, 0, 0
   I (233) wifi: mode : sta (30:ae:a4:80:0a:04)
   I (233) WIFI: SYSTEM_EVENT_STA_START
   I (363) wifi: n:1 0, o:1 0, ap:255 255, sta:1 0, prof:1
   I (1343) wifi: state: init -> auth (b0)
   I (1343) wifi: state: auth -> assoc (0)
   I (1353) wifi: state: assoc -> run (10)
   I (1373) wifi: connected with <Your_WiFi_SSID>, channel 1
   I (1373) WIFI: SYSTEM_EVENT_STA_CONNECTED
   1 302 [IP-task] vDHCPProcess: offer c0a86c13ip
   I (3123) event: sta ip: 192.168.108.19, mask: 255.255.224.0, gw: 192.168.96.1
   I (3123) WIFI: SYSTEM_EVENT_STA_GOT_IP
   2 302 [IP-task] vDHCPProcess: offer c0a86c13ip
   3 303 [main] WiFi Connected to AP. Creating tasks which use network...
   4 304 [OTA] OTA demo version 0.9.6
   5 304 [OTA] Creating MQTT Client...
   6 304 [OTA] Connecting to broker...
   I (4353) wifi: pm start, type:0
   
   I (8173) PKCS11: Initializing SPIFFS
   I (8183) PKCS11: Partition size: total: 52961, used: 0
   7 1277 [OTA] Connected to broker.
   8 1280 [OTA Task] [prvSubscribeToJobNotificationTopics] OK: $aws/things/<Your_Thing_Name>/jobs/$next/get/accepted
   I (12963) ota_pal: prvPAL_GetPlatformImageState
   I (12963) esp_ota_ops: [0] aflags/seq:0x2/0x1, pflags/seq:0xffffffff/0x0
   9 1285 [OTA Task] [prvSubscribeToJobNotificationTopics] OK: $aws/things/<Your_Thing_Name>/jobs/notify-next
   10 1286 [OTA Task] [OTA_CheckForUpdate] Request #0
   11 1289 [OTA Task] [prvParseJSONbyModel] Extracted parameter [ clientToken: 0:<Your_Thing_Name> ]
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
   [ ... ]
   ```

1. The ESP32 board is now listening for OTA updates\. The ESP\-IDF monitor is launched by the `make flash monitor` command\. You can press **Ctrl\+\]** to quit\. You can also use your favorite TTY terminal program \(for example, PuTTY, Tera Term, or GNU Screen\) to listen to the board's serial output\. Be aware that connecting to the board's serial port might cause it to reboot\.