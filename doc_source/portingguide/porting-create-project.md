# Creating an IDE project<a name="porting-create-project"></a>

After you configure your FreeRTOS download, you can create an IDE project and import the code into the project\.

Follow the instructions below to create an IDE project with the required IDE project structure for testing\. 

**Important**  
If you are using an Eclipse\-based IDE, do not configure the project to build all the files in any given folder\. Instead, add source files to a project by linking to each source file individually\.

1. Open your IDE, and create a project named `aws_tests` in the `freertos/projects/vendor/board/ide` directory\.

1. In the IDE, create two virtual folders under the `aws_tests` project:
   + `application_code`
   + `config_files`

   Under `aws_tests`, there should now be two virtual folders in the IDE project: `application_code` and `config_files`\.
**Note**  
Eclipse generates an additional `includes` folder\. This folder is not a part of the required structure\.

1. Import all of the files under `freertos/vendors/vendor/boards/board/aws_tests/application_code` and its subdirectories into the `aws_tests/application_code` virtual folder in your IDE\.

1. Import all of the files under `freertos/tests` and its subdirectories into the `aws_tests/application_code` virtual directory in your IDE\.

1. Import all of the header files in the `freertos/vendors/vendor/boards/board/aws_tests/config_files` directory into the `aws_tests/config_files` virtual folder in your IDE\.
**Note**  
If you are not porting a specific library, you do not need to import the files for that library into your project\. For example, if you are not porting the OTA library, you can leave out the `aws_ota_agent_config.h` and `aws_test_ota_config.h` files\. If you are not porting the Wi\-Fi library, you can leave out the `aws_test_wifi_config.h` and `aws_wifi_config.h` files\.

1. Import the required libraries in `freertos/libraries` and its subdirectories into the `aws_tests` IDE project, including any required third\-party libraries\. For information on the required libraries please follow the [FreeRTOS porting flowchart](https://docs.aws.amazon.com/freertos/latest/portingguide/porting-chart.html)\. Information about which FreeRTOS libraries depend on third\-party libraries that need to be included in addition to the test project is provided in the specific library's porting section\. 

   Finally, import the unity files from the following directories into your project\.
   + `freertos/libraries/3rdparty/unity/src/`
   + `freertos/libraries/3rdparty/unity/extras/fixture/src/`
**Note**  
If you are not porting a specific library, you do not need to import the files for that library into your project\.

1. Import all of the source files in the `freertos/freertos_kernel` and `freertos/freertos_kernel/include` directories into the `aws_tests` IDE project\.

1. Import the subdirectory of `freertos/freertos_kernel/portable` that corresponds to your compiler and platform architecture into the `aws_tests` IDE project\.

1. Import the FreeRTOS memory management implementation that you are using for your device into the `aws_tests` IDE project\.

   The `freertos/freertos_kernel/portable/MemMang` directory contains FreeRTOS memory management implementations\. We highly recommend that you use `heap_4.c` or `heap_5.c`\.

   For more information about FreeRTOS memory management, see [Memory Management](https://www.freertos.org/a00111.html)\.

1. Open your project's IDE properties, and add the following paths to your compiler's include path:
   + `freertos/vendors/vendor/boards/board/aws_tests/config_files`
   + `freertos/freertos_kernel/include`
   + `freertos/freertos_kernel/portable/compiler/architecture`
   + Any paths required for vendor\-supplied driver libraries

1. Define `UNITY_INCLUDE_CONFIG_H` and `AMAZON_FREERTOS_ENABLE_UNIT_TESTS` as project\-level macros in the project properties\.

After you finish setting up your IDE project, you are ready to port the FreeRTOS libraries to your device\. For instructions, see [Porting the FreeRTOS Libraries](afr-porting.md)\.