# Creating an IDE Project<a name="porting-create-project"></a>

After you configure your Amazon FreeRTOS download, you can create an IDE project and import the code into the project\.

Follow the instructions below to create an IDE project with the required IDE project structure for testing\. 

**Important**  
If you are using an Eclipse\-based IDE, do not configure the project to build all the files in any given folder\. Instead, add source files to a project by linking to each source file individually\.

1. Open your IDE, and create a project named `aws_tests` in the `<amazon-freertos>/projects/vendors/<vendor>/boards/<board>/<ide>` directory\.

1. In the IDE, create two virtual folders under the `aws_tests` project:
   + `application_code`
   + `config_files`

   Under `aws_tests`, there should now be two virtual folders in the IDE project: `application_code` and `config_files`\.
**Note**  
Eclipse generates an additional `includes` folder\. This folder is not a part of the required structure\.

1. Import all of the files under `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_tests/application_code` and its subdirectories into the `aws_tests/application_code` virtual folder in your IDE\.

1. Import all of the files under `<amazon-freertos>/test/src` and its subdirectories into the `aws_tests/application_code` virtual folder in your IDE\.

1. Import all of the header files in the `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files` directory into the `aws_tests/config_files` virtual folder in your IDE\.
**Note**  
If you are not porting a specific library, you do not need to import the files for that library into your project\. For example, if you are not porting the OTA library, you can leave out the `aws_ota_agent_config.h` and `aws_test_ota_config.h` files\. If you are not porting the Wi\-Fi library, you can leave out the `aws_test_wifi_config.h` and `aws_wifi_config.h` files\.

1. Import all of the files in `<amazon-freertos>/libraries` and its subdirectories into the `aws_tests` IDE project\.
**Note**  
If you are not porting a specific library, you do not need to import the files for that library into your project\.

1. Import all of the source files in the `<amazon-freertos>/freertos_kernel` and `<amazon-freertos>/freertos_kernel/include` directories into the `aws_tests` IDE project\.

1. Import the subdirectory of `<amazon-freertos>/freertos_kernel/portable` that corresponds to your compiler and platform architecture into the `aws_tests` IDE project\.

1. Import the FreeRTOS memory management implementation that you are using for your device into the `aws_tests` IDE project\.

   The `<amazon-freertos>/freertos_kernel/portable/MemMang` directory contains FreeRTOS memory management implementations\. We highly recommend that you use `heap_4.c` or `heap_5.c`\.

   For more information about FreeRTOS memory management, see [Memory Management](https://www.freertos.org/a00111.html)\.

1. Open your project's IDE properties, and add the following paths to your compiler's include path:
   + `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files`
   + `<amazon-freertos>/freertos_kernel/include`
   + `<amazon-freertos>/freertos_kernel/portable/<compiler>/<architecture>`
   + Any paths required for vendor\-supplied driver libraries

1. Define `UNITY_INCLUDE_CONFIG_H` and `AMAZON_FREERTOS_ENABLE_UNIT_TESTS` as project\-level macros in the project properties\.

After you finish setting up your IDE project, you are ready to port the Amazon FreeRTOS libraries to your device\. For instructions, see [Porting the Amazon FreeRTOS Libraries](afr-porting.md)\.