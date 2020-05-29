# Creating the demo project<a name="demo-create-project"></a>

After you configure your FreeRTOS download, you can create an IDE project with the required project structure for the Hello World demo\.

Follow the instructions below to create an IDE project with the required IDE project structure for demo applications\.

**Important**  
If you are using an Eclipse\-based IDE, do not configure the project to build all the files in any folder\. Instead, add source files to a project by linking to each source file individually\.

1. Create a project named `aws_demos` and save the project to the `projects/vendor/board/ide` directory\.

1. In your IDE, create two virtual folders under `aws_demos`:
   + `application_code`
   + `config_files`

   Under `aws_demos`, there should now be two virtual subdirectories in the IDE project: `application_code` and `config_files`\.
**Note**  
Eclipse generates an additional `includes` folder\. This folder is not a part of the required structure\.

1. Import all of the folders and files in `aws_demos/application_code` and its subdirectories into the `application_code` virtual folder in your IDE\.

1. Import all of the files from `aws_demos/config_files` into the `config_files` virtual folder in your IDE\.

1. Import all of the folders and files from the following directories into the `application_code` virtual folder in your IDE:
   + `freertos/demos/demo_runner`
   + `freertos/demos/mqtt`
   + `freertos/demos/logging`
   + `freertos/libraries/.../provisioning/src`

1. Import the `freertos/demos/include` directory and its contents into the `application_code` virtual folder in your IDE\.

1. Import the following directories and their contents into the `aws_demos` IDE project:
**Note**  
Only import the files and directories that apply to your platform and port\.
   + `freertos/libraries`
   + `freertos/freertos_kernel`
   + `freertos/vendors/vendor/boards/board/driver_library/driver_library_version`

1. Open your project's IDE properties, and add the following paths to your compiler's include path:
   + `freertos/demos/include`
   + `freertos/freertos_kernel/portable/compiler/architecture`
   + `freertos/libraries/3rdparty/mbedtls/include`
   + `freertos/vendors/vendor/boards/board/aws_demos/config_files`
   + Any paths required for vendor\-supplied driver libraries\.