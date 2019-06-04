# Creating an IDE Project<a name="porting-create-project"></a>

After you configure your Amazon FreeRTOS download, you can create an IDE project and import the code into the project\.

Follow the instructions below to create an IDE project with the required IDE project structure for testing\. These instructions were written using an Eclipse\-based IDE, but Amazon FreeRTOS test projects look the same in most IDEs\.

**Important**  
You must import all files in their original position in the directory structure\. Never directly copy files into the project's folder, and never use absolute file paths\.  
If you are using an Eclipse\-based IDE, do not configure the project to build all the files in any given folder\. Instead, add source files to a project by linking to each source file individually\.

1. Open your IDE, and create a project named `aws_tests` in the `<amazon-freertos>/tests/<vendor>/<board>/<ide>` directory\.

1. In the IDE, create three virtual folders under the `aws_tests` project:
   + `application_code`
   + `config_files`
   + `lib`

   Under `aws_tests`, there should now be three virtual folders in the IDE project:

   ```
   aws_tests    (The project name)
   + - application_code    (Contains application logic, in this case, porting test code)
   + - config_files    (Contains header files that configure Amazon FreeRTOS libraries)
   + - lib    (Contains Amazon and third-party libraries)
   ```
**Note**  
Eclipse generates an additional `includes` folder\. This folder is not a part of the required structure\.

1. Under `aws_tests/application_code`, create a virtual folder named `common_test`, and import the following directories into that virtual folder:
   + `<amazon-freertos>/tests/common/framework`
   + `<amazon-freertos>/tests/common/include`
   + `<amazon-freertos>/tests/common/memory_leak`
   + `<amazon-freertos>/tests/common/test_runner`
   + `<amazon-freertos>/tests/common/utils`

1. Import the `<amazon-freertos>/tests/<vendor>/<board>/common/application_code/<vendor>_code` directory and the `<amazon-freertos>/tests/<vendor>/<board>/common/application_code/main.c` file directly into the `aws_tests/application_code` virtual folder\.

   The `application_code` virtual folder should now look like this in the IDE project:

   ```
   aws_tests
   + - application_code
       + - common_test
       |   + - framework
       |   + - include
       |   + - memory_leak
       |   + - test_runner
       |   + - utils
       + - <vendor>_code
       + - main.c
   ```

1. Import all of the header files in the `<amazon-freertos>/tests/<vendor>/<board>/common/config_files` directory into the `aws_tests/config_files` virtual folder\.
**Note**  
If you are not porting a specific library, you do not need to import the files for that library into your project\. For example, if you are not porting the OTA library, you can leave out the `aws_ota_agent_config.h` and `aws_test_ota_config.h` files\. If you are not porting the Wi\-Fi library, you can leave out the `aws_test_wifi_config.h` and `aws_wifi_config.h` files\.

   The `config_files` virtual folder should now look something like this in the IDE project:

   ```
   aws_tests
   + - config_files
       + - aws_bufferpool_config.h
       + - aws_ggd_config.h
       + - aws_mqtt_agent_config.h
       + - aws_mqtt_config.h
       + - aws_ota_agent_config.h
       + - aws_pkcs11_config.h
       + - aws_secure_sockets_config.h
       + - aws_shadow_config.h
       + - aws_test_ota_config.h
       + - aws_test_pkcs11_config.h
       + - aws_test_runner_config.h
       + - aws_test_tcp_config.h
       + - aws_test_wifi_config.h
       + - aws_wifi_config.h
       + - FreeRTOSConfig.h
       + - FreeRTOSIPConfig.h
       + - trcConfig.h
       + - trcSnapshotConfig.h
       + - unity_config.h
   ```

1. Under `aws_tests/lib`, create a virtual folder named `aws`\. As you port Amazon FreeRTOS, you create virtual folders under this directory for each library\.

1. Import the directory `<amazon-freertos>/lib/include` into the `aws_tests/lib/aws` virtual folder\.

1. Under `aws_tests/lib/aws`, create virtual folders named `FreeRTOS` and `Utils`\.

1. Import `<amazon-freertos>/lib/utils/aws_system_init.c` into the `aws_tests/lib/aws/Utils` virtual folder\.

1. Import the FreeRTOS memory management implementation that you are using for your device into the `aws_tests/lib/aws/FreeRTOS` virtual folder\. The `<amazon-freertos>/lib/FreeRTOS/portable/MemMang` directory contains FreeRTOS memory management implementations\. We highly recommend that you use `heap_4.c` or `heap_5.c`\.

   For more information about FreeRTOS memory management, see [Memory Management](https://www.freertos.org/a00111.html)\.

1. Import all of the files in the `<amazon-freertos>/lib/FreeRTOS` directory into the `aws_tests/lib/aws/FreeRTOS` virtual folder\.

1. Under `aws_tests/lib/aws/FreeRTOS`, create a virtual folder named `portable`, and then import the subdirectory of `<amazon-freertos>/lib/FreeRTOS/portable` that corresponds to your compiler and architecture into the `portable` virtual folder\.

   The `lib/aws` virtual folder should now look like this in the IDE project:

   ```
   aws_tests
   + - lib
       + - aws
           + - FreeRTOS
           |   + - portable
           |   |   + - MemMang
           |   |   |   + - heap_4.c
           |   |   + - MSVC-MingW
           |   |       + - port.c
           |   |       + - portmacro.h
           |   + - event_groups.c
           |   + - list.c
           |   + - queue.c
           |   + - tasks.c
           |   + - timers.c
           + - include
           + - Utils
           |   + - aws_system_init.c
           + - unity_config.h
   ```

1. Under `aws_tests/lib`, create a virtual folder named `third_party`, and then import all of the files in `<amazon-freertos>/lib/third_party/<mcu_vendor>/<vendor>/<driver_library>/<driver_library_version>` into the `third_party` virtual folder\.

1. Under `aws_tests/lib/third_party`, create virtual folders named `unity` and `unity_fixture`\.

1. Import all of the files in `<amazon-freertos>/lib/third_party/unity/src` into the `aws_tests/lib/third_party/unity` virtual folder\.

1. Import all of the files in `<amazon-freertos>/lib/third_party/unity/extras/fixture/src` into the `aws_tests/lib/third_party/unity_fixture` virtual folder\.

   The `lib/third_party` virtual folder should now look like this in the IDE project:

   ```
   aws_tests
   + - lib
       + - third_party
           + - unity
           |   + - unity_internals.h
           |   + - unity.c
           |   + - unity.h
           + - unity_fixture
               + - unity_fixture_internals.h
               + - unity_fixture.c
               + - unity_fixture.h
               + - unity_fixture_malloc_overrides.h
   ```

1. Open your project's IDE properties, and add the following paths to your compiler's include path:
   + `<amazon-freertos>/tests/common/include `
   + `<amazon-freertos>/lib/include`
   + `<amazon-freertos>/lib/include/private`
   + `<amazon-freertos>/lib/FreeRTOS/portable/<compiler>/<architecture>`
   + `<amazon-freertos>/lib/third_party/unity/src`
   + `<amazon-freertos>/lib/third_party/unity/extras/fixture/src`
   + `<amazon-freertos>/demos/<vendor>/<board>/common/config_files`
   + Any paths required for vendor\-supplied driver libraries

1. Define `UNITY_INCLUDE_CONFIG_H` and `AMAZON_FREERTOS_ENABLE_UNIT_TESTS` as project\-level macros in the project properties\.

After you finish setting up your IDE project, you are ready to port the Amazon FreeRTOS libraries to your device\. For instructions, see [Porting the Amazon FreeRTOS Libraries](afr-porting.md)\.