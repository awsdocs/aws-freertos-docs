# Download, build, flash, and run the FreeRTOS OTA demo on the Texas Instruments CC3220SF\-LAUNCHXL<a name="download-ota-ti"></a><a name="download-demo"></a>

**To download FreeRTOS and the OTA demo code**

1. Browse to the AWS IoT console and from the navigation pane, choose **Software**\.

1. Under **FreeRTOS Device Software**, choose **Configure download**\.

1. From the list of software configurations, choose **Connect to AWS IoT \- TI**\. Choose the configuration name, not the **Download** link\.

1. Under **Libraries**, choose **Add another library**, and then choose **OTA Updates**\.

1. Under **Demo Projects**, choose **OTA Updates**\.

1. Under **Name required**, enter **Connect\-to\-IoT\-OTA\-TI**, and then choose **Create and download**\.

Save the zip file that contains FreeRTOS and the OTA demo code to your computer\. <a name="build-demo"></a>

**To build the demo application**

1. Extract the \.zip file\.

1. Follow the instructions in [Getting Started with FreeRTOS](freertos-getting-started.md) to import the `aws_demos` project into Code Composer Studio, configure your AWS IoT endpoint, your Wi\-Fi SSID and password, and a private key and certificate for your board\.

1.  Open `freertos/vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h`,  comment out `#define CONFIG_CORE_MQTT_MUTUAL_AUTH_DEMO_ENABLED`, and define `CONFIG_OTA_MQTT_UPDATE_DEMO_ENABLED` or `CONFIG_OTA_HTTP_UPDATE_DEMO_ENABLED`\.

1. Build the solution and make sure it builds without errors\.

1. Start a terminal emulator and use the following settings to connect to your board:
   + Baud rate: 115200
   + Data bits: 8
   + Parity: None
   + Stop bits: 1

1. Run the project on your board to confirm it can connect to Wi\-Fi and the AWS IoT MQTT message broker\.

When run, the terminal emulator should display text like the following:

```
    0 1000 [Tmr Svc] Simple Link task created
    Device came up in Station mode
    1 2534 [Tmr Svc] Write certificate...
    2 5486 [Tmr Svc] [ERROR] Failed to destroy object. PKCS11_PAL_DestroyObject failed.
    3 5486 [Tmr Svc] Write certificate...
    4 5776 [Tmr Svc] Security alert threshold = 15
    5 5776 [Tmr Svc] Current number of alerts = 1
    6 5778 [Tmr Svc] Running Demos.
    7 5779 [iot_thread] [INFO ][DEMO][5779] ---------STARTING DEMO---------
    8 5779 [iot_thread] [INFO ][INIT][5779] SDK successfully initialized.
    Device came up in Station mode
    [WLAN EVENT] STA Connected to the AP: afrlab-pepper , BSSID: 74:83:c2:b4:46:27
    [NETAPP EVENT] IP acquired by the device
    Device has connected to afrlab-pepper
    Device IP Address is 192.168.36.176 
    9 8283 [iot_thread] [INFO ][DEMO][8282] Successfully initialized the demo. Network type for the demo: 1
    10 8283 [iot_thread] [INFO] OTA over MQTT demo, Application version 0.9.0
    11 8283 [iot_thread] [INFO] Creating a TLS connection to <endpoint>-ats.iot.us-west-2.amazonaws.com:8883.
    12 8852 [iot_thread] [INFO] Creating an MQTT connection to <endpoint>-ats.iot.us-west-2.amazonaws.com.
    13 8914 [iot_thread] [INFO] Packet received. ReceivedBytes=2.
    14 8914 [iot_thread] [INFO] CONNACK session present bit not set.
    15 8914 [iot_thread] [INFO] Connection accepted.
    16 8914 [iot_thread] [INFO] Received MQTT CONNACK successfully from broker.
    17 8914 [iot_thread] [INFO] MQTT connection established with the broker.
    18 8915 [iot_thread] [INFO]  Received: 0   Queued: 0   Processed: 0   Dropped: 0
    19 8953 [OTA Agent T] [INFO] Current State=[RequestingJob], Event=[Start], New state=[RequestingJob]
    20 9008 [MQTT Agent ] [INFO] Packet received. ReceivedBytes=3.
    21 9015 [OTA Agent T] [INFO] SUBSCRIBED to topic $aws/things/__test_infra_thing73/jobs/notify-next to broker.
    22 9015 [OTA Agent T] [INFO] Subscribed to MQTT topic: $aws/things/__test_infra_thing73/jobs/notify-next
    23 9504 [MQTT Agent ] [INFO] Publishing message to $aws/things/__test_infra_thing73/jobs/$next/get.
    24 9535 [MQTT Agent ] [INFO] Packet received. ReceivedBytes=2.
    25 9535 [MQTT Agent ] [INFO] Ack packet deserialized with result: MQTTSuccess.
    26 9536 [MQTT Agent ] [INFO] State record updated. New state=MQTTPublishDone.
    27 9537 [OTA Agent T] [INFO] Sent PUBLISH packet to broker $aws/things/__test_infra_thing73/jobs/$next/get to broker.
    28 9537 [OTA Agent T] [WARN] OTA Timer handle NULL for Timerid=0, can't stop.
    29 9537 [OTA Agent T] [INFO] Current State=[WaitingForJob], Event=[RequestJobDocument], New state=[WaitingForJob]
    30 9539 [MQTT Agent ] [INFO] Packet received. ReceivedBytes=120.
    31 9539 [MQTT Agent ] [INFO] De-serialized incoming PUBLISH packet: DeserializerResult=MQTTSuccess.
    32 9540 [MQTT Agent ] [INFO] State record updated. New state=MQTTPublishDone.
    33 9540 [MQTT Agent ] [INFO] Received job message callback, size 62.
    34 9616 [OTA Agent T] [INFO] Failed job document content check: Required job document parameter was not extracted: parameter=execution
    35 9616 [OTA Agent T] [INFO] Failed job document content check: Required job document parameter was not extracted: parameter=execution.jobId
    36 9617 [OTA Agent T] [INFO] Failed job document content check: Required job document parameter was not extracted: parameter=execution.jobDocument
    37 9617 [OTA Agent T] [INFO] Failed job document content check: Required job document parameter was not extracted: parameter=execution.jobDocument.afr_ota
    38 9617 [OTA Agent T] [INFO] Failed job document content check: Required job document parameter was not extracted: parameter=execution.jobDocument.afr_ota.protocols
    39 9618 [OTA Agent T] [INFO] Failed job document content check: Required job document parameter was not extracted: parameter=execution.jobDocument.afr_ota.files
    40 9618 [OTA Agent T] [INFO] Failed job document content check: Required job document parameter was not extracted: parameter=filesize
    41 9618 [OTA Agent T] [INFO] Failed job document content check: Required job document parameter was not extracted: parameter=fileid
    42 9619 [OTA Agent T] [INFO] Failed to parse JSON document as AFR_OTA job: DocParseErr_t=7
    43 9619 [OTA Agent T] [INFO] No active job available in received job document: OtaJobParseErr_t=OtaJobParseErrNoActiveJobs
    44 9619 [OTA Agent T] [ERROR] Failed to execute state transition handler: Handler returned error: OtaErr_t=OtaErrJobParserError
    45 9620 [OTA Agent T] [INFO] Current State=[WaitingForJob], Event=[ReceivedJobDocument], New state=[CreatingFile]
    46 9915 [iot_thread] [INFO]  Received: 0   Queued: 0   Processed: 0   Dropped: 0
    47 10915 [iot_thread] [INFO]  Received: 0   Queued: 0   Processed: 0   Dropped: 0
    48 11915 [iot_thread] [INFO]  Received: 0   Queued: 0   Processed: 0   Dropped: 0
    49 12915 [iot_thread] [INFO]  Received: 0   Queued: 0   Processed: 0   Dropped: 0
    50 13915 [iot_thread] [INFO]  Received: 0   Queued: 0   Processed: 0   Dropped: 0
    51 14915 [iot_thread] [INFO]  Received: 0   Queued: 0   Processed: 0   Dropped: 0
```