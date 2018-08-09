# Install the Initial Version of Firmware on the Texas Instruments CC3200SF\-LAUNCHXL<a name="burn-initial-firmware-ti"></a>

These steps assume you have already built the aws\_demos project as described in [Download and Build Amazon FreeRTOS for the Texas Instruments CC3200SF\-LAUNCHXL](ota-prereqs.md#download-ota-ti)\.<a name="burn-demo-ti"></a>

1. On your Texas Instruments CC3200SF\-LAUNCHXL, place the SOP jumper on the middle set of pins \(position = 1\) and reset the board\.

1. Download and install the [TI Uniflash tool](http://www.ti.com/tool/UNIFLASH)\.

1. Start Uniflash and from the list of configurations, choose **CC3220SF\-LAUNCHXL**, and then choose **Start Image Creator**\.

1. Choose **New Project**\.

1. On the **Start new project** page, type a name for your project\. Set **Device Type** to **CC3220SF**, set **Device Mode** to **Develop**, and then choose **Create Project**\.

1. Disconnect your terminal emulator and choose the **Connect** button on the right side of the Uniflash application window\.

1. On the left, under **Files**, choose **User Files**\.

1. In the **File** selector pane, choose the **Add File** icon ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/add-file.png)\.

1. Browse to the `/Applications/Ti/simplelink_cc32xx_sdk_2_10_00_04/tools/cc32xx_tools/certificate-playground` directory, select `dummy-root-ca-cert`, choose **Open**, and then choose **Write**\.

1. In the **File** selector pane, choose the **Add File** icon ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/add-file.png)\.

1. Browse to the working directory where you created the code signing certificate and private key, choose `tisigner.crt.der`, choose **Open**, and then choose **Write**\.

1. From the **Action** drop\-down list, choose **Select MCU Image**, and then choose **Browse** to choose the firmware image to use write to your device \(**aws\_demos\.bin**\)\. This file is located in the `AmazonFreeRTOS/demos/ti/cc3200_launchpad/ccs/Debug` directory\. Choose **Open**\.

1. In the file dialog box, confirm the file name is set to **mcuflashimg\.bin**\. Select the **Vendor** check box\. Under **File Token**, type **1952007250**\. Under **Private Key File Name**, choose **Browse** and then choose `tisigner.key` from the working directory where you created the code\-signing certificate and private key\. Under **Certification File Name**, choose `tisigner.crt.der`, and then choose **Write**\.

1. In the left pane, under **Files**, select **Service Pack**\.

1. Under **Service Pack File Name** choose **Browse** and browse to `simplelink_cc32x_sdk_2_10_00_04/tools/servicepack-cc3x20` and choose `sp_3.4.0.0_2.0.0.0_2.2.0.5.bin` and choose **Open**\.

1. In the left pane, under **Files**, select **Trusted Root\-Certificate Catalog**\.

1. Clear the **Use default Trusted Root\-Certificate Catalog** check box\.

1. Under **Source File**, choose **Browse**, select **simplelink\_cc32xx\_sdk\_2\_10\_00\_04/tools/certificate\-playground\\certcatalogPlayGround20160911\.lst**, and then choose **Open**\.

1. Under **Signature Source File**, choose **Browse**, select **simplelink\_cc32xx\_sdk\_2\_10\_00\_04/tools/certificate\-playground\\certcatalogPlayGround20160911\.lst\.signed\.bin**, and then choose **Open**\.

1. Choose the ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/save.png) button to save your project\.

1. Choose the ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/flame.png) button\.

1. Choose **Program Image \(Create and Program\)**\.

1. After programming process is complete, place the SOP jumper onto the first set of pins \(position = 0\), reset the board, and reconnect your terminal emulator to make sure the output is the same as when you debugged the demo with Code Composer Studio\. Notice the application version number in the terminal output\. You will use this version number to verify that your firmware has been updated by an OTA update\.

   The terminal should display output like the following:

   ```
   0 0 [Tmr Svc] Simple Link task created
   
   Device came up in Station mode
   
   1 369 [Tmr Svc] Starting key provisioning...
   2 369 [Tmr Svc] Write root certificate...
   3 467 [Tmr Svc] Write device private key...
   4 568 [Tmr Svc] Write device certificate...
   SL Disconnect...
   
   5 664 [Tmr Svc] Key provisioning done...
   Device came up in Station mode
   
   Device disconnected from the AP on an ERROR..!! 
   
   [WLAN EVENT] STA Connected to the AP: Guest , BSSID: 11:22:a1:b2:c3:d4
   
   [NETAPP EVENT] IP acquired by the device
   
   
   Device has connected to Guest
   
   Device IP Address is 111.222.3.44 
   
   
   6 1716 [OTA] OTA demo version 0.9.0
   7 1717 [OTA] Creating MQTT Client...
   8 1717 [OTA] Connecting to broker...
   9 1717 [OTA] Sending command to MQTT task.
   10 1717 [MQTT] Received message 10000 from queue.
   11 2193 [MQTT] MQTT Connect was accepted. Connection established.
   12 2193 [MQTT] Notifying task.
   13 2194 [OTA] Command sent to MQTT task passed.
   14 2194 [OTA] Connected to broker.
   15 2196 [OTA Task] Sending command to MQTT task.
   16 2196 [MQTT] Received message 20000 from queue.
   17 2697 [MQTT] MQTT Subscribe was accepted. Subscribed.
   18 2697 [MQTT] Notifying task.
   19 2698 [OTA Task] Command sent to MQTT task passed.
   20 2698 [OTA Task] [OTA] Subscribed to topic: $aws/things/TI-LaunchPad/jobs/$next/get/accepted
   
   21 2699 [OTA Task] Sending command to MQTT task.
   22 2699 [MQTT] Received message 30000 from queue.
   23 2800 [MQTT] MQTT Subscribe was accepted. Subscribed.
   24 2800 [MQTT] Notifying task.
   25 2801 [OTA Task] Command sent to MQTT task passed.
   26 2801 [OTA Task] [OTA] Subscribed to topic: $aws/things/TI-LaunchPad/jobs/notify-next
   
   27 2814 [OTA Task] [OTA] Check For Update #0
   28 2814 [OTA Task] Sending command to MQTT task.
   29 2814 [MQTT] Received message 40000 from queue.
   30 2916 [MQTT] MQTT Publish was successful.
   31 2916 [MQTT] Notifying task.
   32 2917 [OTA Task] Command sent to MQTT task passed.
   33 2917 [OTA Task] [OTA] Set job doc parameter [ clientToken: 0:TI-LaunchPad ]
   34 2917 [OTA Task] [OTA] Missing job parameter: execution
   35 2917 [OTA Task] [OTA] Missing job parameter: jobId
   36 2918 [OTA Task] [OTA] Missing job parameter: jobDocument
   37 2918 [OTA Task] [OTA] Missing job parameter: ts_ota
   38 2918 [OTA Task] [OTA] Missing job parameter: files
   39 2918 [OTA Task] [OTA] Missing job parameter: streamname
   40 2918 [OTA Task] [OTA] Missing job parameter: certfile
   41 2918 [OTA Task] [OTA] Missing job parameter: filepath
   42 2918 [OTA Task] [OTA] Missing job parameter: filesize
   43 2919 [OTA Task] [OTA] Missing job parameter: sig-sha1-rsa
   44 2919 [OTA Task] [OTA] Missing job parameter: fileid
   45 2919 [OTA Task] [OTA] Missing job parameter: attr
   47 3919 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
   48 4919 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
   49 5919 [OTA] [OTA] Queued: 1   Processed: 1   Dropped: 0
   ```