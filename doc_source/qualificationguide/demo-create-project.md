# Creating the Demo Project<a name="demo-create-project"></a>

After you configure your Amazon FreeRTOS download, you can create an IDE project with the required project structure for the Hello World demo\.

Follow the instructions below to create an IDE project with the required IDE project structure for demo applications\. These instructions were written using an Eclipse\-based IDE, but Amazon FreeRTOS demo projects look the same in most IDEs\.

**Important**  
You must import all files in their original position in the directory structure\. Never directly copy files into the project's folder, and never use absolute file paths\.  
If you are using an Eclipse\-based IDE, do not configure the project to build all the files in any folder\. Instead, add source files to a project by linking to each source file individually\.

1. Create a project named `aws_demos` in the `<amazon-freertos>/demos/<vendor>/<board>/<ide>` directory\.

1. In your IDE, create three virtual folders under `aws_demos`:
   + `application_code`
   + `config_files`
   + `lib`

   Under `aws_demos`, there should now be three virtual subdirectories in the IDE project:

   ```
   aws_demos    (The project name)
   + - application_code    (Contains application logic, in this case, demo code)
   + - config_files    (Contains header files that configure Amazon FreeRTOS libraries)
   + - lib    (Contains Amazon and third-party libraries)
   ```
**Note**  
Eclipse generates an additional `includes` folder\. This folder is not a part of the required structure\.

1. Import all of the folders and files in `<amazon-freertos>/demos/<vendor>/<board>/common/application_code` into the `application_code` virtual folder\.

1. Import all of the files in `<amazon-freertos>/demos/<vendor>/<board>/common/config_files` into the `config_files` virtual folder\.

1. Under `application_code`, create a folder named `common_demos`\.

1. Under `common_demos`, create a folder named `source`, and import all of the folders and files in all of the following directories into that folder:
   + `<amazon-freertos>/demos/common/demo_runner`
   + `<amazon-freertos>/demos/common/devmode_key_provisioning` \(only the `.c` file\)
   + `<amazon-freertos>/demos/common/mqtt`
   + `<amazon-freertos>/demos/common/logging`

1. Import the `<amazon-freertos>/demos/common/include` directory and its contents into the `common_demos` folder\.

1. Under the `lib` virtual folder, create two virtual folders named `aws` and `third_party`\.

1. Import the following files or directories and their contents into the `aws` folder:
**Note**  
If you are importing a file or folder whose directory path extends beyond the `lib` directory, you must create virtual folders in the IDE to match the directory path after `<amazon-freertos>/lib`\.
   + `<amazon-freertos>/lib/bufferpool`
   + `<amazon-freertos>/lib/FreeRTOS`
   + `<amazon-freertos>/lib/FreeRTOS/portable/MemMang/heap_4.c`
   + `<amazon-freertos>/lib/FreeRTOS/portable/<your-compiler>`
   + `<amazon-freertos>/lib/FreeRTOS-Plus-TCP`
**Note**  
If you have the FreeRTOS\+TCP library, see [Porting a TCP/IP Stack](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-tcp.html) in the Amazon FreeRTOS Porting Guide to determine if other files must be included in this project\.
   + `<amazon-freertos>/lib/include`
   + `<amazon-freertos>/lib/include/private` \(only the `.h` files\)
   + `<amazon-freertos>/lib/mqtt`
   + `<amazon-freertos>/lib/pkcs11/portable/<vendor>/<board>/pkcs11.c`
   + `<amazon-freertos>/lib/secure_sockets/portable/<vendor>/<board>/aws_secure_sockets.c`
   + `<amazon-freertos>/lib/tls`
**Note**  
Import the `tls` directory only if you have ported the TLS library\.
   + `<amazon-freertos>/lib/wifi/portable/<vendor>/<board>/aws_wifi.c`
**Note**  
Import the `aws_wifi.c` file only if you have ported the Wi\-Fi library\.

1. Import the following directories and their contents into `third_party`:
**Note**  
If you are importing a file or folder whose directory path extends beyond the `third_party` directory, you must create virtual folders in the IDE to match the full directory structure, after `<amazon-freertos>/lib/third_party`\.
   + `<amazon-freertos>/lib/third_party/mcu_vendor/<vendor>/<board>/<driver_library>/<driver_library_version>`
   + `<amazon-freertos>/lib/third_party/mbedtls`
**Note**  
Rename `mbedtls/library` to `mbedtls/source`\.
   + `<amazon-freertos>/lib/third_party/pkcs11`

1. Open your project's IDE properties, and add the following paths to your compiler's include path:
   + `<amazon-freertos>/demos/common/include`
   + `<amazon-freertos>/lib/include`
   + `<amazon-freertos>/lib/include/private`
   + `<amazon-freertos>/lib/FreeRTOS/portable/<compiler>/<architecture>`
   + `<amazon-freertos>/lib/third_party/mbedtls/include`
   + `<amazon-freertos>/demos/<vendor>/<board>/common/config_files`
   + Any paths required for vendor\-supplied driver libraries\.