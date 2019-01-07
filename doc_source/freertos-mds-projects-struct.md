# Navigating the Demo Applications<a name="freertos-mds-projects-struct"></a>

This section contains information about directory and file organization and configuration files for the demos\. 

## Directory and File Organization<a name="freertos-files-org"></a>

There are two subfolders in the main Amazon FreeRTOS directory:
+ `demos`

  Contains example code that can be run on an Amazon FreeRTOS device to demonstrate Amazon FreeRTOS functionality\. There is one subdirectory for each target platform selected\. These subdirectories contain code used by the demos, but not all demos can be run independently\. If you use the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), only the target platform you choose has a subdirectory under `demos`\.

  The function `DEMO_RUNNER_RunDemos()` located in `AmazonFreeRTOS\demos\common\demo_runner\aws_demo_runner.c` contains code that calls each example\. By default, only the `vStartMQTTEchoDemo()` function is called\. Depending on the configuration you selected when you downloaded the code, or whether you obtained the code from GitHub, the other example runner functions are either commented out or omitted entirely\. Although you can change the selection of demos here, be aware that not all combinations of examples work together\. Depending on the combination, the software might not be able to be executed on the selected target due to memory constraints\. All of the examples that can be executed by Amazon FreeRTOS appear in the common directory under `demos`\.
+ `lib`

  The `lib` directory contains the source code of the Amazon FreeRTOS libraries\.

  There are helper functions that assist in implementing the library functionality\. We do not recommend that you change these helper functions\. If you need to change one of these libraries, make sure it conforms to the library interface defined in the `libs/include` directory\.

## Configuration Files<a name="freertos-config"></a>

The demos have been configured to get you started quickly\. You might want to change some of the configurations for your project to create a version that runs on your platform\. You can find configuration files at `demos/<vendor>/<platform>/common/config_files`\.