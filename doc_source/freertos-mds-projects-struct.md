# Navigating the Demo Applications<a name="freertos-mds-projects-struct"></a>

## Directory and File Organization<a name="freertos-files-org"></a>

There are two subfolders in the main Amazon FreeRTOS directory:
+ `demos`

  Contains example code that can be run on an Amazon FreeRTOS device to demonstrate Amazon FreeRTOS functionality\. There is one subdirectory for each target platform selected\. These subdirectories contain code used by the demos, but not all demos can be run independently\. If you use the [Amazon FreeRTOS console](https://console.aws.amazon.com/freertos), only the target platform you choose has a subdirectory under `demos`\.

  The function `DEMO_RUNNER_RunDemos()` located in AmazonFreeRTOS\\demos\\common\\demo\_runner\\aws\_demo\_runner\.c contains code that calls each example\. By default, only the `vStartMQTTEchoDemo()` function is called\. Depending on the configuration you selected when you downloaded the code, or whether you obtained the code from Github, the other example runner functions are either commented out or omitted entirely\. Although you can change the selection of demos here, be aware that not all combinations of examples work together\. Depending on the combination, the software might not be able to be executed on the selected target due to memory constraints\. All of the examples that can be executed by Amazon FreeRTOS appear in the common directory under `demos`\.
+ `lib`

  The `lib` directory contains the source code of the Amazon FreeRTOS libraries\. These libraries are available to you as part of Amazon FreeRTOS:
  + MQTT
  + Device shadow
  + Greengrass
  + Wi\-Fi

  There are helper functions that assist in implementing the library functionality\. We do not recommend that you change these helper functions\. If you need to change one of these libraries, make sure it conforms to the library interface defined in the `libs/include` directory\.

## Configuration Files<a name="freertos-config"></a>

The demos have been configured to get you started quickly\. You might want to change some of the configurations for your project to create a version that runs on your platform\. You can find configuration files at `AmazonFreeRTOS/<vendor>/<platform>/common/config_files`\.

The configuration files include:

`aws_bufferpool.h`  
Configures the size and quantity of static buffers available for use by the application\.

`aws_clientcredential_keys.h`  
Configures your device certificate and private key\.

`aws_demo_config.h`  
Configures the task parameters used in the demos: stack size, priorities, and so on\.

`aws_ggd_config.h`  
Configures the parameters used to configure a Greengrass core, such as network interface IDs\.

`aws_mqtt_agent_config.h`  
Configures the parameters related to MQTT operations, such as task priorities, MQTT brokers, and keep\-alive counts\.

`aws_mqtt_library.h`  
Configures MQTT library parameters, such as the subscription length and the maximum number of subscriptions\.

`aws_secure_sockets_config.h`  
Configures the timeouts and the byte ordering when using Secure Sockets\.

`aws_shadow_configure.h`  
Configures the parameters used for an AWS IoT shadow, such as the number of JSMN tokens used when parsing a JSON file received from a shadow\.

`aws_clientcredential.h`  
Configures parameters, including the Wi\-Fi \(SSID, password, and security type\), the MQTT broker endpoint, and IoT thing name\.

`FreeRTOSConfig.h`  
Configures the FreeRTOS kernel for multitasking operations\.