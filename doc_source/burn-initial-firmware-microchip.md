# Install the Initial Version of Firmware on the Microchip Curiosity PIC32MZEF<a name="burn-initial-firmware-microchip"></a>

These steps assume you have already built the aws\_demos project as described in [Download and Build Amazon FreeRTOS for the Microchip Curiosity PIC32MZEF](ota-prereqs.md#download-ota-mchip)\.<a name="mch-burn-demo"></a>

**To burn the demo application onto your board**

1. Open `aws_demos/lib/aws/ota/aws_codesign_keys.h`\.

1. Paste the contents of "ecdsasigner\.crt" created earlier into the `static const char signingcredentialSIGNING_CERTIFICATE_PEM` variable\. Following the same format as aws\_clientcredential\_keys\.h, each line must end with the new line character \('\\n'\) and be surrounded by quotes\.

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

1. Paste the contents of "ecdsasigner\.key" created earlier into the `static const char signingcredentialSIGNING_PRIVATE_KEY_PEM` variable\. Following the same format as aws\_clientcredential\_keys\.h, each line must end with the new line character \('\\n'\) and be surrounded by quotes\.

1. Rebuild the aws\_demos project and make sure it compiles without errors\.

1. Click on the ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/MakeAndProgram.png) from the top tool bar\.

1. After the programming process is complete, disconnect the ICD 4 debugger and reset the board\. Reconnect your terminal emulator to make sure the output is the same as when you debugged the demo with MPLAB X IDE\.

The terminal should display output similar to the following:

```
AWS Validate: no valid signature in descr: 0xbd000000
AWS Validate: no valid signature in descr: 0xbd100000


>AWS Launch:  No Map performed. Running directly from address: 0x9d000020?
AWS Launch:  wait for app at: 0x9d000020
WILC1000: Initializing...
0 0 

>[None] Seed for randomizer: 838152022
1 0 [None] Random numbers: 0000066B 0000062C 0000030B 00000533
Chip ID 1503a0

Firmware ver. : 4.2.1

Min driver ver : 4.2.1

Curr driver ver: 4.2.1

WILC1000: Initialization successful!

Start Wi-Fi Connection...
Wi-Fi Connected
2 1962 [IP-task] vDHCPProcess: offer c0a80f16ip
3 1973 [IP-task] vDHCPProcess: offer c0a80f16ip
4 1973 [IP-task] 

IP Address: 111.222.33.44
5 1973 [IP-task] Subnet Mask: 255.255.240.0
6 1973 [IP-task] Gateway Address: 111.222.3.4
7 1973 [IP-task] DNS Server Address: 111.22.333.444


8 1975 [OTA] OTA demo version 0.9.0
9 1975 [OTA] Creating MQTT Client...
10 1975 [OTA] Connecting to broker...
11 1975 [OTA] Sending command to MQTT task.
12 1975 [MQTT] Received message 10000 from queue.
13 3184 [IP-task] Socket sending wakeup to MQTT task.
14 4911 [MQTT] Received message 0 from queue.
15 4968 [IP-task] Socket sending wakeup to MQTT task.
16 4968 [MQTT] Received message 0 from queue.
17 4979 [IP-task] Socket sending wakeup to MQTT task.
18 4979 [MQTT] Received message 0 from queue.
19 4979 [MQTT] MQTT Connect was accepted. Connection established.
20 4979 [MQTT] Notifying task.
21 4980 [OTA] Command sent to MQTT task passed.
22 4980 [OTA] Connected to broker.
23 4981 [OTA Task] Sending command to MQTT task.
24 4981 [MQTT] Received message 20000 from queue.
25 4998 [IP-task] Socket sending wakeup to MQTT task.
26 4998 [MQTT] Received message 0 from queue.
27 5029 [IP-task] Socket sending wakeup to MQTT task.
28 5029 [MQTT] Received message 0 from queue.
29 5029 [MQTT] MQTT Subscribe was accepted. Subscribed.
30 5029 [MQTT] Notifying task.
31 5030 [OTA Task] Command sent to MQTT task passed.
32 5030 [OTA Task] [OTA] Subscribed to topic: $aws/things/Microchip/jobs/$next/get/accepted

33 5030 [OTA Task] Sending command to MQTT task.
34 5030 [MQTT] Received message 30000 from queue.
35 6069 [IP-task] Socket sending wakeup to MQTT task.
36 6069 [MQTT] Received message 0 from queue.
37 6069 [MQTT] MQTT Subscribe was accepted. Subscribed.
38 6069 [MQTT] Notifying task.
39 6070 [OTA Task] Command sent to MQTT task passed.
40 6070 [OTA Task] [OTA] Subscribed to topic: $aws/things/Microchip/jobs/notify-next

41 6070 [OTA Task] [OTA] Check For Update #0
42 6070 [OTA Task] Sending command to MQTT task.
43 6070 [MQTT] Received message 40000 from queue.
44 7109 [IP-task] Socket sending wakeup to MQTT task.
45 7109 [MQTT] Received message 0 from queue.
46 7110 [MQTT] MQTT Publish was successful.
47 7110 [MQTT] Notifying task.
48 7111 [OTA Task] Command sent to MQTT task passed.
49 7111 [OTA Task] [OTA] Set job doc parameter [ clientToken: 0:Microchip ]
50 7111 [OTA Task] [OTA] Missing job parameter: execution
51 7111 [OTA Task] [OTA] Missing job parameter: jobId
52 7111 [OTA Task] [OTA] Missing job parameter: jobDocument
53 7111 [OTA Task] [OTA] Missing job parameter: ts_ota
54 7111 [OTA Task] [OTA] Missing job parameter: files
55 7111 [OTA Task] [OTA] Missing job parameter: streamname
56 7111 [OTA Task] [OTA] Missing job parameter: certfile
57 7111 [OTA Task] [OTA] Missing job parameter: filepath
58 7111 [OTA Task] [OTA] Missing job parameter: filesize
59 7111 [OTA Task] [OTA] Missing job parameter: sig-sha256-ecdsa
60 7111 [OTA Task] [OTA] Missing job parameter: fileid
61 7111 [OTA Task] [OTA] Missing job parameter: attr
62 7111 [OTA Task] [OTA] Returned buffer to MQTT Client.
63 8112 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
```