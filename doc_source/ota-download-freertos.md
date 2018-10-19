# Download Amazon FreeRTOS with the OTA Library<a name="ota-download-freertos"></a>

Follow the steps in this section to download code and build demo applications\.

## Download and Build Amazon FreeRTOS for the Texas Instruments CC3200SF\-LAUNCHXL<a name="download-ota-ti"></a><a name="download-demo"></a>

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

1. Follow the instructions in [Getting Started with Amazon FreeRTOS](freertos-getting-started.md), to import the `aws_demos` project into Code Composer Studio, configure your AWS IoT endpoint, your Wi\-Fi SSID and password, and a private key and certificate for your board\.

1. Open the project in Code Composer Studio and open `demos/common/demo_runner/aws_demo_runner.c`\. Find the `DEMO_RUNNER_RunDemos` function and make sure all function calls are commented out except `vStartOTAUpdateDemoTask`\.

1. Build the solution and make sure it builds without errors\.

1. Start a terminal emulator and use the following settings to connect to your board:
   + Baud rate: 115200
   + Data bits: 8
   + Parity: None
   + Stop bits: 1

1. Run the project on your board to make sure it can connect to Wi\-Fi and the AWS IoT MQTT message broker\.

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

## Download and Build Amazon FreeRTOS for the Microchip Curiosity PIC32MZEF<a name="download-ota-mchip"></a><a name="mch-dowload-demo"></a>

**To download the Amazon FreeRTOS OTA demo code**

1. Browse to the AWS IoT console and from the navigation pane, choose **Software**\.

1. Under **Amazon FreeRTOS Device Software**, choose **Configure download**\.

1. From the list of software configurations, choose **Connect to AWS IoT \- Microchip**\. Choose the configuration name, not the **Download** link\.

1. Under **Libraries**, choose **Add another library**, and then choose **OTA Updates**\.

1. Under **Demo projects**, choose **OTA Update**\.

1. Under **Name required**, enter a name for your custom Amazon FreeRTOS software configuration\.

1. Choose **Create and download**\.<a name="mch-build-demo"></a>

**To build the OTA update demo application**

1. Extract the \.zip file you just downloaded\.

1. Follow the instructions in [Getting Started with Amazon FreeRTOS](freertos-getting-started.md) to import the `aws_demos` project into the MPLAB X IDE, configure your AWS IoT endpoint, your Wi\-Fi SSID and password, and a private key and certificate for your board\.

1. Open `aws_demos/lib/aws/ota/aws_ota_codesigner_certificate.h`\.

1. Paste the contents of your code\-signing certificate into the `static const char signingcredentialSIGNING_CERTIFICATE_PEM` variable\. Following the same format as `aws_clientcredential_keys.h`, each line must end with the new line character \('\\n'\) and be enclosed in quotation marks\.

   For example, your certificate should look similar to the following:

   ```
   "-----BEGIN CERTIFICATE-----\n"
   "MIIBXTCCAQOgAwIBAgIJAM4DeybZcTwKMAoGCCqGSM49BAMCMCExHzAdBgNVBAMM\n"
   "FnRlc3Rf62lnbmVyQGFtYXpvbi5jb20wHhcNMTcxMTAzMTkxODM1WhcNMTgxMTAz\n"
   "MTkxODM2WjAhMR8wHQYDVQBBZZZ0ZXN0X3NpZ25lckBhbWF6b24uY29tMFkwEwYH\n"
   "KoZIzj0CAQYIKoZIzj0DAQcDQgAERavZfvwL1X+E4dIF7dbkVMUn4IrJ1CAsFkc8\n"
   "gZxPzn683H40XMKltDZPEwr9ng78w9+QYQg7ygnr2stz8yhh06MkMCIwCwYDVR0P\n"
   "BAQDAgeAMBMGA1UdJQQMMAoGCCsGAQUFBwMDMAoGCCqGSM49BAMCA0gAMEUCIF0R\n"
   "r5cb7rEUNtWOvGd05MacrgOABfSoVYvBOK9fP63WAqt5h3BaS123coKSGg84twlq\n"
   "TkO/pV/xEmyZmZdV+HxV/OM=\n"
   "-----END CERTIFICATE-----\n";
   ```

1. Install [Python 3](https://www.python.org/downloads/) or later\.

1. Install `pyOpenSSL` by running `pip install pyopenssl`\.

1. Copy your code\-signing certificate in \.pem format in the path `\demos\common\ota\bootloader\utility\codesigner_cert_utility\`\. Rename the certificate file `aws_ota_codesigner_certificate.pem`\.

1. Open the project in MPLAB X IDE and open `demos/common/demo_runner/aws_demo_runner.c`\. Find the `DEMO_RUNNER_RunDemos` function and make sure all function calls are commented out except `vStartOTAUpdateDemoTask`\.

1. Build the solution and make sure it builds without errors\.

1. Start a terminal emulator and use the following settings to connect to your board:
   + Baud rate: 115200
   + Data bits: 8
   + Parity: None
   + Stop bits: 1

1. Unplug the debugger from your board and run the project on your board to make sure it can connect to Wi\-Fi and the AWS IoT MQTT message broker\.

When you run the project, the MPLAB X IDE should open an output window\. Make sure the ICD4 tab is selected\. You should see the following output\.

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

The terminal emulator should display text like the following:

```
AWS Validate: no valid signature in descr: 0xbd000000
AWS Validate: no valid signature in descr: 0xbd100000


>AWS Launch:  No Map performed. Running directly from address: 0x9d000020?
AWS Launch:  wait for app at: 0x9d000020
WILC1000: Initializing...
0 0 

>[None] Seed for randomizer: 1172751941
1 0 [None] Random numbers: 00004272 00003B34 00000602 00002DE3
Chip ID 1503a0

[spi_cmd_rsp][356][nmi spi]: Failed cmd response read, bus error...

[spi_read_reg][1086][nmi spi]: Failed cmd response, read reg (0000108c)...

[spi_read_reg][1116]Reset and retry 10 108c

Firmware ver. : 4.2.1

Min driver ver : 4.2.1

Curr driver ver: 4.2.1

WILC1000: Initialization successful!

Start Wi-Fi Connection...
Wi-Fi Connected
2 7219 [IP-task] vDHCPProcess: offer c0a804beip
3 7230 [IP-task] vDHCPProcess: offer c0a804beip
4 7230 [IP-task] 

IP Address: 192.168.4.190
5 7230 [IP-task] Subnet Mask: 255.255.240.0
6 7230 [IP-task] Gateway Address: 192.168.0.1
7 7230 [IP-task] DNS Server Address: 208.67.222.222


8 7232 [OTA] OTA demo version 0.9.0
9 7232 [OTA] Creating MQTT Client...
10 7232 [OTA] Connecting to broker...
11 7232 [OTA] Sending command to MQTT task.
12 7232 [MQTT] Received message 10000 from queue.
13 8501 [IP-task] Socket sending wakeup to MQTT task.
14 10207 [MQTT] Received message 0 from queue.
15 10256 [IP-task] Socket sending wakeup to MQTT task.
16 10256 [MQTT] Received message 0 from queue.
17 10256 [MQTT] MQTT Connect was accepted. Connection established.
18 10256 [MQTT] Notifying task.
19 10257 [OTA] Command sent to MQTT task passed.
20 10257 [OTA] Connected to broker.
21 10258 [OTA Task] Sending command to MQTT task.
22 10258 [MQTT] Received message 20000 from queue.
23 10306 [IP-task] Socket sending wakeup to MQTT task.
24 10306 [MQTT] Received message 0 from queue.
25 10306 [MQTT] MQTT Subscribe was accepted. Subscribed.
26 10306 [MQTT] Notifying task.
27 10307 [OTA Task] Command sent to MQTT task passed.
28 10307 [OTA Task] [OTA] Subscribed to topic: $aws/things/Microchip/jobs/$next/get/accepted

29 10307 [OTA Task] Sending command to MQTT task.
30 10307 [MQTT] Received message 30000 from queue.
31 10336 [IP-task] Socket sending wakeup to MQTT task.
32 10336 [MQTT] Received message 0 from queue.
33 10336 [MQTT] MQTT Subscribe was accepted. Subscribed.
34 10336 [MQTT] Notifying task.
35 10336 [OTA Task] Command sent to MQTT task passed.
36 10336 [OTA Task] [OTA] Subscribed to topic: $aws/things/Microchip/jobs/notify-next

37 10336 [OTA Task] [OTA] Check For Update #0
38 10336 [OTA Task] Sending command to MQTT task.
39 10336 [MQTT] Received message 40000 from queue.
40 10366 [IP-task] Socket sending wakeup to MQTT task.
41 10366 [MQTT] Received message 0 from queue.
42 10366 [MQTT] MQTT Publish was successful.
43 10366 [MQTT] Notifying task.
44 10366 [OTA Task] Command sent to MQTT task passed.
45 10376 [IP-task] Socket sending wakeup to MQTT task.
46 10376 [MQTT] Received message 0 from queue.
47 10376 [OTA Task] [OTA] Set job doc parameter [ clientToken: 0:Microchip ]
48 10376 [OTA Task] [OTA] Missing job parameter: execution
49 10376 [OTA Task] [OTA] Missing job parameter: jobId
50 10376 [OTA Task] [OTA] Missing job parameter: jobDocument
51 10378 [OTA Task] [OTA] Missing job parameter: ts_ota
52 10378 [OTA Task] [OTA] Missing job parameter: files
53 10378 [OTA Task] [OTA] Missing job parameter: streamname
54 10378 [OTA Task] [OTA] Missing job parameter: certfile
55 10378 [OTA Task] [OTA] Missing job parameter: filepath
56 10378 [OTA Task] [OTA] Missing job parameter: filesize
57 10378 [OTA Task] [OTA] Missing job parameter: sig-sha256-ecdsa
58 10378 [OTA Task] [OTA] Missing job parameter: fileid
59 10378 [OTA Task] [OTA] Missing job parameter: attr
60 10378 [OTA Task] [OTA] Returned buffer to MQTT Client.
61 11367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
62 12367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
63 13367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
64 14367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
65 15367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
66 16367 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
```

This output shows the Microchip Curiosity PIC32MZEF is able to connect to AWS IoT and subscribe to the MQTT topics required for OTA updates\. The `Missing job parameter` messages are expected because there are no OTA update jobs pending\.

## Download and Build Amazon FreeRTOS for the Espressif ESP32<a name="download-ota-esp"></a>

1. Download the Amazon FreeRTOS source from [GitHub](https://github.com/aws/amazon-freertos)\. Create a project in your IDE that includes all required sources and libraries\.

1. Follow the instructions in [Getting Started with Espressif](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html) to set up the required GCC\-based toolchain\.

1. Open `demos/common/demo_runner/aws_demo_runner.c` in a text editor\. Find the `DEMO_RUNNER_RunDemos` function and make sure all function calls are commented out except `vStartOTAUpdateDemoTask`\.

1. Build the demo project by running `make`in the `demos/espressif/esp32_devkitc_esp_wrover_kit/make/` directory\. You can flash the demo program and verify its output by running `make flash monitor`, as described in [Getting Started with Espressif](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html)\.

1. Before running the OTA Update demo:
   + Make sure that `vStartOTAUpdateDemoTask` is the only function called in the `DEMO_RUNNER_RunDemos()` function in `demos/common/demo_runner/aws_demo_runner.c`\.
   + Make sure that your SHA\-256/ECDSA code\-signing certificate is copied into the `demos/common/include/aws_ota_codesigner_certificate.h`\.

## Download and Build Amazon FreeRTOS for a Custom Hardware Platform<a name="download-freertos-to-port"></a>

Download the Amazon FreeRTOS source from [GitHub](https://github.com/aws/amazon-freertos)\. Create a project in your IDE that includes all required sources and libraries\.

Build and run the project to make sure it can connect to AWS IoT\.

For more information about porting Amazon FreeRTOS to a new platform, see [Amazon FreeRTOS Porting Guide](porting-guide.md)\.