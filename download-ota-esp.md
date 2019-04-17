# Download, Build, Flash, and Run the Amazon FreeRTOS OTA demo on the Espressif ESP32<a name="download-ota-esp"></a>

1. Download the Amazon FreeRTOS source from [GitHub](https://github.com/aws/amazon-freertos)\. Create a project in your IDE that includes all required sources and libraries\.

1. Follow the instructions in [Getting Started with Espressif](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html) to set up the required GCC\-based toolchain\.

1. Open `demos/common/demo_runner/aws_demo_runner.c` in a text editor\. Find the `DEMO_RUNNER_RunDemos` function and make sure all function calls are commented out except `vStartOTAUpdateDemoTask`\.

1. Build the demo project by running `make`in the `demos/espressif/esp32_devkitc_esp_wrover_kit/make/` directory\. You can flash the demo program and verify its output by running `make flash monitor`, as described in [Getting Started with Espressif](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_espressif.html)\.

1. Before running the OTA Update demo:
   + Make sure that `vStartOTAUpdateDemoTask` is the only function called in the `DEMO_RUNNER_RunDemos()` function in `demos/common/demo_runner/aws_demo_runner.c`\.
   + Make sure that your SHA\-256/ECDSA code\-signing certificate is copied into the `demos/common/include/aws_ota_codesigner_certificate.h`\.