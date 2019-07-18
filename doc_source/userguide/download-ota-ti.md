# Download, Build, Flash, and Run the Amazon FreeRTOS OTA demo on the Texas Instruments CC3200SF\-LAUNCHXL<a name="download-ota-ti"></a><a name="download-demo"></a>

**To download Amazon FreeRTOS and the OTA demo code**

1. Browse to the AWS IoT console and from the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. From the list of software configurations, choose **Connect to AWS IoT \- TI**\. Choose the configuration name, not the **Download** link\.

1. Under **Libraries**, choose **Add another library**, and then choose **OTA Updates**\.

1. Under **Demo Projects**, choose **OTA Updates**\.

1. Under **Name required**, enter **Connect\-to\-IoT\-OTA\-TI**, and then choose **Create and download**\.

Save the zip file that contains Amazon FreeRTOS and the OTA demo code to your computer\. <a name="build-demo"></a>

**To build the demo application**

1. Extract the \.zip file\.

1. Follow the instructions in [Getting Started with Amazon FreeRTOS](freertos-getting-started.md) to import the `aws_demos` project into Code Composer Studio, configure your AWS IoT endpoint, your Wi\-Fi SSID and password, and a private key and certificate for your board\.

1. Open `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_MQTT_DEMO_ENABLED`, and define `CONFIG_OTA_UPDATE_DEMO_ENABLED`\.

1. Build the solution and make sure it builds without errors\.

1. Start a terminal emulator and use the following settings to connect to your board:
   + Baud rate: 115200
   + Data bits: 8
   + Parity: None
   + Stop bits: 1

1. Run the project on your board to confirm it can connect to Wi\-Fi and the AWS IoT MQTT message broker\.

When run, the terminal emulator should display text like the following:

```
0 0 [Tmr Svc] Starting Wi-Fi Module ...
1 0 [Tmr Svc] Simple Link task created
Device came up in Station mode
2 142 [Tmr Svc] Wi-Fi module initialized.
3 142 [Tmr Svc] Starting key provisioning...
4 142 [Tmr Svc] Write root certificate...
5 243 [Tmr Svc] Write device private key...
6 340 [Tmr Svc] Write device certificate...
7 433 [Tmr Svc] Key provisioning done...
[WLAN EVENT] STA Connected to the AP: Mobile , BSSID: 24:de:c6:5d:32:a4
[NETAPP EVENT] IP acquired by the device

Device has connected to Mobile
Device IP Address is 192.168.111.12

8 2666 [Tmr Svc] Wi-Fi connected to AP Mobile.
9 2666 [Tmr Svc] IP Address acquired 192.168.111.12
10 2667 [OTA] OTA demo version 0.9.2
11 2667 [OTA] Creating MQTT Client...
12 2667 [OTA] Connecting to broker...
13 3512 [OTA] Connected to broker.
14 3715 [OTA Task] [prvSubscribeToJobNotificationTopics] OK: $aws/things/OtaGA/jobs/$next/get/accepted
15 4018 [OTA Task] [prvSubscribeToJobNotificationTopics] OK: $aws/things/OtaGA/jobs/notify-next
16 4027 [OTA Task] [prvPAL_GetPlatformImageState] xFileInfo.Flags = 0250
17 4027 [OTA Task] [prvPAL_GetPlatformImageState] eOTA_PAL_ImageState_Valid
18 4034 [OTA Task] [OTA_CheckForUpdate] Request #0
19 4248 [OTA] [OTA_AgentInit] Ready.
20 4249 [OTA Task] [prvParseJSONbyModel] Extracted parameter [ clientToken: 0:OtaGA ]
21 4249 [OTA Task] [prvParseJSONbyModel] parameter not present: execution
22 4249 [OTA Task] [prvParseJSONbyModel] parameter not present: jobId
23 4249 [OTA Task] [prvParseJSONbyModel] parameter not present: jobDocument
24 4249 [OTA Task] [prvParseJSONbyModel] parameter not present: afr_ota
25 4250 [OTA Task] [prvParseJSONbyModel] parameter not present: streamname
26 4250 [OTA Task] [prvParseJSONbyModel] parameter not present: files
27 4250 [OTA Task] [prvParseJSONbyModel] parameter not present: filepath
28 4250 [OTA Task] [prvParseJSONbyModel] parameter not present: filesize
29 4250 [OTA Task] [prvParseJSONbyModel] parameter not present: fileid
30 4250 [OTA Task] [prvParseJSONbyModel] parameter not present: certfile
31 4251 [OTA Task] [prvParseJSONbyModel] parameter not present: sig-sha1-rsa
32 4251 [OTA Task] [prvParseJobDoc] Ignoring job without ID.
33 4251 [OTA Task] [prvOTA_Close] Context->0x2001b2c4
34 5248 [OTA] State: Ready Received: 1 Queued: 1 Processed: 1 Dropped: 0
35 6248 [OTA] State: Ready Received: 1 Queued: 1 Processed: 1 Dropped: 0
36 7248 [OTA] State: Ready Received: 1 Queued: 1 Processed: 1 Dropped: 0
37 8248 [OTA] State: Ready Received: 1 Queued: 1 Processed: 1 Dropped: 0
38 9248 [OTA] State: Ready Received: 1 Queued: 1 Processed: 1 Dropped: 0
```