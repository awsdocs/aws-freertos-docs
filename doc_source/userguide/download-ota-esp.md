# Download, build, flash, and run the FreeRTOS OTA demo on the Espressif ESP32<a name="download-ota-esp"></a>

1. Download the FreeRTOS source from [GitHub](https://github.com/aws/amazon-freertos)\. See the [README\.md](https://github.com/aws/amazon-freertos/blob/main/README.md) file for instructions\. Create a project in your IDE that includes all required sources and libraries\.

1. Follow the instructions in [Getting Started with Espressif](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html) to set up the required GCC\-based toolchain\.

1. Open `freertos/vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_CORE_MQTT_MUTUAL_AUTH_DEMO_ENABLED`, and define `CONFIG_OTA_MQTT_UPDATE_DEMO_ENABLED` or `CONFIG_OTA_HTTP_UPDATE_DEMO_ENABLED`\.

1. Build the demo project by running `make` in the `vendors/espressif/boards/esp32/aws_demos` directory\. You can flash the demo program and verify its output by running `make flash monitor`, as described in [Getting Started with Espressif](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html)\.

1. Before running the OTA Update demo:
   + Open `freertos/vendors/vendor/boards/board/aws_demos/config_files/aws_demo_config.h`, comment out `#define CONFIG_CORE_MQTT_MUTUAL_AUTH_DEMO_ENABLED`, and define `CONFIG_OTA_MQTT_UPDATE_DEMO_ENABLED` or `CONFIG_OTA_HTTP_UPDATE_DEMO_ENABLED`\.
   + Open `vendors/vendor/boards/board/aws_demos/config_files/ota_demo_config.h`, and copy your SHA\-256/ECDSA code\-signing certificate in:

     ```
     #define otapalconfigCODE_SIGNING_CERTIFICATE [] = "your-certificate-key";
     ```