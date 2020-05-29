# Creating a list file for your platform from the CMakeLists\.txt template<a name="cmake-template"></a>

A `CMakeLists.txt` template file is provided with FreeRTOS, under `freertos/vendors/vendor/boards/board/CMakeLists.txt`\. 

The `CMakeLists.txt` template file consists of four sections:
+ [FreeRTOS console metadata](#cmake-metadata)
+ [Compiler settings](#cmake-compiler)
+ [FreeRTOS portable layers](#cmake-portable)
+ [FreeRTOS demos and tests](#cmake-demos-tests)

Follow the instructions to edit these four sections of the list file to match your platform\. You can refer to the `CMakeLists.txt` files for other qualified vendor boards under `freertos/vendors` as examples\.

Two primary functions are called throughout the file:

`afr_set_board_metadata(name value)`  
This function defines metadata for the FreeRTOS console\. The function is defined in `freertos/tools/cmake/afr_metadata.cmake`\.

`afr_mcu_port(module_name [<DEPENDS> [targets...]])`  
This function defines the portable\-layer target associated with a FreeRTOS module \(that is, library\)\. It creates a CMake `GLOBAL INTERFACE IMPORTED` target with a name of the form `AFR:module_name::mcu_port`\. If `DEPENDS` is used, additional targets are linked with `target_link_libraries`\. The function is defined in `freertos/tools/cmake/afr_module.cmake`\.

## FreeRTOS console metadata<a name="cmake-metadata"></a>

The first section of the template file defines the metadata that is used to display a board's information in the FreeRTOS console\. Use the function `afr_set_board_metadata(name value)` to define each field listed in the template\. This table provides descriptions of each field\.


| Field Name | Value Description | 
| --- | --- | 
| ID | A unique ID for the board\. | 
| DISPLAY\_NAME | The name of the board as you want it displayed on the FreeRTOS console\. | 
| DESCRIPTION | A short description of the board for the FreeRTOS console\. | 
| VENDOR\_NAME | The name of the vendor of the board\. | 
| FAMILY\_NAME | The name of the board's MCU family\. | 
| DATA\_RAM\_MEMORY | The size of the board's RAM, followed by abbreviated units\. For example, use KB for kilobytes\. | 
| PROGRAM\_MEMORY | The size of the board's program memory, followed by abbreviated units\. For example, use "MB" for megabytes\. | 
| CODE\_SIGNER | The code\-signing platform used for OTA updates\. Use AmazonFreeRTOS\-Default for SHA256 hash algorithm and ECDSA encryption algorithm\. If you want to use a different code\-signing platform, [contact us](https://freertos.org/RTOS-contact-and-support.html)\. | 
| SUPPORTED\_IDE | A semicolon\-delimited list of IDs for the IDEs that the board supports\. | 
| IDE\_ID\_NAME | The name of the supported IDE\. Replace ID with the ID listed for the IDE in the SUPPORTED\_IDE field\. | 
| IDE\_ID\_COMPILER | A semicolon\-delimited list of names of supported compilers for the supported IDE\. Replace ID with the ID listed for the IDE in the SUPPORTED\_IDE field\. | 

## Compiler settings<a name="cmake-compiler"></a>

The second section of the template file defines the compiler settings for your board\. To create a target that holds the compiler settings, call the `afr_mcu_port` function with `compiler` in place of the *module\_name* to create an `INTERFACE` target with the name `AFR::compiler::mcu_port`\. The kernel publicly links to this `INTERFACE` target so that the compiler settings are transitively populated to all modules\.

Use the standard, built\-in CMake functions to define the compiler settings in this section of the list file\. As you define the compiler settings, follow these best practices:
+ Use `target_compile_definitions` to provide compile definitions and macros\.
+ Use `target_compile_options` to provide compiler flags\.
+ Use `target_include_directories` to provide include directories\.
+ Use `target_link_options` to provide linker flags\.
+ Use `target_link_directories` to provide linker\-search directories\.
+ Use `target_link_libraries` to provide libraries to link against\.

**Note**  
If you define the compiler settings somewhere else, you don't need to duplicate the information in this section of the file\. Instead, call `afr_mcu_port` with `DEPENDS` to bring in the target definition from another location\.  
For example:  

```
# your_target is defined somewhere else. It does not have to be in the same file.
afr_mcu_port(compiler DEPENDS your_target)
```
When you call `afr_mcu_port` with `DEPENDS`, it calls `target_link_libraries(AFR::module_name::mcu_port INTERFACE your_targets)`, which populates the compiler settings for the required `AFR::compiler::mcu_port` target\.

### Using multiple compilers<a name="w3aab9c15c15b9c17c11"></a>

If your board supports multiple compilers, you can use the `AFR_TOOLCHAIN` variable to dynamically select the compiler settings\. This variable is set to the name of the compiler you are using, which should be same as the name of the toolchain file found under `freertos/tools/cmake/toolchains`\.

For example:

```
if("${AFR_TOOLCHAIN}" STREQUAL "arm-gcc")
    afr_mcu_port(compiler DEPENDS my_gcc_settings).
elseif("${AFR_TOOLCHAIN}" STREQUAL "arm-iar")
    afr_mcu_port(compiler DEPENDS my_iar_settings).
else()
    message(FATAL_ERROR "Compiler ${AFR_TOOLCHAIN} not supported.")
endif()
```

### Advanced compiler settings<a name="w3aab9c15c15b9c17c13"></a>

If you want to set more advanced compiler settings, such as setting compiler flags based on programming language, or changing settings for different release and debug configurations, you can use CMake generator expressions\.

For example:

```
set(common_flags "-foo")
set(c_flags "-foo-c")
set(asm_flags "-foo-asm")
target_compile_options(
    my_compiler_settings INTERFACE
    $<$<COMPILE_LANGUAGE:C>:${common_flags} ${c_flags}> # This only have effect on C files.
    $<$<COMPILE_LANGUAGE:ASM>:${common_flags} ${asm_flags}> # This only have effect on ASM files.
)
```

CMake generator expressions are not evaluated at the configuration stage, when CMake reads list files\. They are evaluated at the generation stage, when CMake finishes reading list files and generates build files for the target build system\.

## FreeRTOS portable layers<a name="cmake-portable"></a>

The third section of the template file defines all of the portable layer targets for FreeRTOS \(that is, libraries\)\.

You must use the `afr_mcu_port(module_name)` function to define a portable layer target for each FreeRTOS module that you plan to implement\.

You can use any CMake functions you want, as long as the `afr_mcu_port` call creates a target with a name that provides the information required to build the corresponding FreeRTOS module\.

The `afr_mcu_port` function creates a [GLOBAL INTERFACE IMPORTED library target](https://cmake.org/cmake/help/latest/command/add_library.html?#interface-libraries) with a name of the form `AFR::module_name::mcu_port`\. As a `GLOBAL` target, it can be referenced in CMake list files\. As an `INTERFACE` target, it is not built as a standalone target or library, but compiled into the corresponding FreeRTOS module\. As an `IMPORTED` target, its name includes a namespace \(`::`\) in the target name \(for example, `AFR::kernel::mcu_port`\)\.

Modules without corresponding portable layer targets are disabled by default\. If you run CMake to configure FreeRTOS, without defining any portable layer targets, you should see the following output:

```
FreeRTOS modules:
  Modules to build:
  Disabled by user:
  Disabled by dependency:  kernel, posix, pkcs11, secure_sockets, mqtt, ...

  Available demos:
  Available tests:
```

As you update the `CMakeLists.txt` file with porting layer targets, the corresponding FreeRTOS modules are enabled\. You should also be able to build any FreeRTOS module whose dependency requirements are subsequently satisfied\. For example, if the MQTT library is enabled, the Device Shadow library is also enabled, because its only dependency is the MQTT library\.

**Note**  
The FreeRTOS kernel dependency is a minimum requirement\. The CMake configuration fails if the FreeRTOS kernel dependency is not satisfied\.

### Setting up the kernel porting target<a name="cmake-portable-kernel"></a>

To create the kernel porting target \(`AFR::kernel::mcu_port`\), call `afr_mcu_port` with the module name `kernel`\. When you call `afr_mcu_port`, specify the targets for the FreeRTOS portable layer and driver code\. After you create the target, you can provide the dependency information and the FreeRTOS portable layer and driver code information for the target to use\.

Follow the these instructions to set up the kernel porting target\.

**To set up the kernel porting target**

1. Create a target for the driver code\.

   For example, you can create a `STATIC` library target for the driver code:

   ```
   add_library(my_board_driver STATIC ${driver_sources})
   
   # Use your compiler settings
   target_link_libraries(
       my_board_driver
       PRIVATE AFR::compiler::mcu_port
   # Or use your own target if you already have it.
   #   PRIVATE ${compiler_settings_target}
   )
   
   target_include_directories(
       my_board_driver
       PRIVATE "include_dirs_for_private_usage"
       PUBLIC "include_dirs_for_public_interface"
   )
   ```

   Or you can create an `INTERFACE` library target for the driver code:

   ```
   # No need to specify compiler settings since kernel target has them.
   add_library(my_board_driver INTERFACE ${driver_sources})
   ```
**Note**  
An `INTERFACE` library target does not have build output\. If you use an `INTERFACE` library target, the driver code is compiled into the kernel library\.

1. Configure the FreeRTOS portable layer:

   ```
   add_library(freertos_port INTERFACE)
   target_sources(
       freertos_port
       INTERFACE
           "${AFR_MODULES_DIR}/freertos_kernel/portable/GCC/ARM_CM4F/port.c"
           "${AFR_MODULES_DIR}/freertos_kernel/portable/GCC/ARM_CM4F/portmacro.h"
           "${AFR_MODULES_DIR}/freertos_kernel/portable/MemMang/heap_4.c"
   )
   target_include_directories(
       freertos_port
       INTERFACE
           "${AFR_MODULES_DIR}/freertos_kernel/portable/GCC/ARM_CM4F"
           "${include_path_to_FreeRTOSConfig_h}
   )
   ```
**Note**  
You can also configure the FreeRTOS portable layer by specifying these source files and their include directories directly in the `AFR::kernel::mcu_port` target\.

1. Create the kernel portable layer target:

   ```
   # Bring in driver code and freertos portable layer dependency.
   afr_mcu_port(kernel DEPENDS my_board_driver freertos_port)
   
   # If you need to specify additional configurations, use standard CMake functions with
   # AFR::kernel::mcu_port as the target name.
   target_include_directories(
       AFR::kernel::mcu_port
       INTERFACE
           "${additional_includes}" # e.g. board configuration files
   )
   target_link_libraries(
       AFR::kernel::mcu_port
       INTERFACE
           "${additional_dependencies}"
   )
   ```

1. To test your list file and configuration, you can write a simple application that uses the FreeRTOS kernel port\. For more information about developing and building FreeRTOS applications with CMake, see [Building FreeRTOS with CMake](building-cmake.md)\.

1. After you create the demo, add `add_executable` and `target_link_libraries` calls to the list file, and compile the kernel as a static library to verify that the kernel portable layer is correctly configured\.

   ```
   add_executable(
       my_demo
       main.c
   )
   target_link_libraries(
       my_demo
       PRIVATE AFR::kernel
   )
   ```

### Setting up the porting targets for FreeRTOS modules<a name="cmake-portable-modules"></a>

After you add the portable layer target for the kernel, you can add portable layer targets for other FreeRTOS modules\.

For example, to add the portable layer for the Wi\-Fi module:

```
afr_mcu_port(wifi)
target_sources(
    AFR::wifi::mcu_port
    INTERFACE "${AFR_MODULES_DIR}/vendors/vendor/boards/board/ports/wifi/iot_wifi.c"
)
```

This example Wi\-Fi module portable layer has only one implementation file, which is based on the driver code\.

If you want to add the portable layer for the FreeRTOS Secure Sockets module, the module depends on TLS\. This makes its portable layer target slightly more complicated than that of the Wi\-Fi module\. FreeRTOS provides a default TLS implementation based on mbedTLS that you can link to:

```
afr_mcu_port(secure_sockets)
target_sources(
    AFR::secure_sockets::mcu_port
    INTERFACE ${portable_layer_sources}
)
target_link_libraries(
    AFR::secure_sockets::mcu_port
    AFR::tls
)
```

In this example code, the standard CMake function `target_link_libraries` states that the Secure Sockets portable layer depends on `AFR::tls`\.

You can reference all FreeRTOS modules by using their target name `AFR::module_name`\. For example, you can use the same syntax to also state a dependency on FreeRTOS\-Plus\-TCP:

```
target_link_libraries(
    AFR::secure_sockets::mcu_port
    AFR::freertos_plus_tcp
    AFR::tls
)
```

**Note**  
If your platform handles TLS by itself, you can use your driver code directly\. If you use your driver code directly for TLS, you don't need to call `target_link_libraries`, because all FreeRTOS modules implicitly depend on the kernel that includes your driver code\.  
Because all non\-kernel FreeRTOS modules implicitly depend on the kernel, their porting layers don't require you to specify the kernel as a dependency\. The POSIX module, however, is defined as an optional kernel module\. If you want to use POSIX, you must explicitly include it in your kernel portable layer\. For example:  

```
# By default, AFR::posix target does not expose standard POSIX headers in its public
# interface, i.e., You need to use "freertos_plus_posix/source/FreeRTOS_POSIX_pthread.c"  instead of "pthread.h".
# Link to AFR::use_posix instead if you need to use those headers directly.
target_link_libraries(
    AFR::kernel::mcu_port
    INTERFACE AFR::use_posix
)
```

## FreeRTOS demos and tests<a name="cmake-demos-tests"></a>

The final section of the template file defines the demo and test targets for FreeRTOS\. CMake targets are created automatically for each demo and test that satisfies the dependency requirements\.

In this section, define an executable target with the `add_executable` function\. Use `aws_tests` as the target name if you're compiling tests, or `aws_demos` if you're compiling demos\. You might need to provide other project settings, such as linker scripts and post\-build commands\. For example:

```
if(AFR_IS_TESTING)
    set(exe_target aws_tests)
else()
    set(exe_target aws_demos)
endif()

set(CMAKE_EXECUTABLE_SUFFIX ".elf")
add_executable(${exe_target} "${board_dir}/application_code/main.c")
```

`target_link_libraries` is then called to link available CMake demo or test targets to your executable target\.

**Note**  
You still need to modify `aws_demos/config_files/aws_demo_config.h` and `aws_tests/config_files/aws_test_runner_config.h` to enable demos and tests\.

### Running post\-build commands<a name="cmake-post-build"></a>

For information about running post\-build commands, see [add\_custom\_command](https://cmake.org/cmake/help/latest/command/add_custom_command.html)\. Use the second signature\. For example:

```
# This should run an external command "command --arg1 --arg2".
add_custom_command(
    TARGET ${exe_target} POST_BUILD COMMAND "command" "--arg1" "--arg2"
)
```

**Note**  
CMake supports many common, platform\-independent operations for creating directories, copying files, and so on\. For more information about CMake command\-line operations, see the [CMake command\-line tool reference](https://cmake.org/cmake/help/latest/manual/cmake.1.html#command-line-tool-mode)\. You can reference the CMake command\-line tool from a CMake list file with the built\-in variable `${CMAKE_COMMAND}`\.