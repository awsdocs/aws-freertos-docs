# Configuring the Amazon FreeRTOS Download<a name="porting-set-up-directory"></a>

All qualified Amazon FreeRTOS ports use the same directory structure\. New files must be created in the correct folder locations\.

Follow the instructions below to configure the Amazon FreeRTOS download for porting Amazon FreeRTOS code to your device\.

## Configuring Directories for Vendor\-supplied, Board\-specific Libraries<a name="w3aab9c13b7"></a>

Under the download's root directory \(`<amazon-freertos>`\), there are five folders: `cmake`, `demos`, `lib`, `tests`, and `tools`\.

```
<amazon-freertos>
+ - cmake    (Contains files for CMake support)
+ - demos    (Contains projects that build demo applications)
+ - lib    (Contains Amazon FreeRTOS and third-party libraries)
+ - tests    (Contains projects that build porting tests)
+ - tools    (Contains tools for configuration, development, and testing)
```

The `tests` folder is structured as follows:

```
<amazon-freertos>
+ - tests
    + - common    (Contains files built by all test projects)
    + - pc    (Contains a reference test project for the FreeRTOS Windows port)
    + - <vendor>    (Template, to be renamed to the name of the MCU vendor)
        + - <board>    (Template, to be renamed to the name of the development board)
```

The `<board>` folder is a template folder provided to simplify the creation of a new test project\. This template folder's directory structure ensures all test projects have a consistent organization\. The `<board>` folder has the following structure:

```
<amazon-freertos>
+ - tests
    + - <vendor>
        + - <board>
            + - common
            |   + - application_code    (Contains main.c, which contains main())
            |   |   + - <vendor>_code    (Contains vendor-supplied, board-specific files)
            |   + - config_files    (Contains Amazon FreeRTOS config files)
            + - <ide>    (Contains an IDE-specific project)
```

All test projects require vendor\-supplied driver libraries\. Some vendor\-supplied files, such as a header file that maps GPIO output to an LED light, are specific to a target development board\. These files belong in the `<vendor>_code` folder\.

**To set up the directories for vendor\-supplied, board\-specific libraries**

1. Save all required vendor\-supplied libraries that are specific to the board in the `<vendor>_code` folder\.

1. Rename the *<vendor>* in the `<vendor>_code` folder to the name of the vendor\.

1. Rename the `<ide>` folder to the name of the IDE that you are using to build the test project\.

1. Rename the `<vendor>` folder to the name of the vendor, and rename the `<board>` folder to the name of the development board\.

## Configuring Directories for Common Vendor\-supplied Libraries<a name="w3aab9c13b9"></a>

Other vendor\-supplied files, such as a GPIO library, are common across a board's MCU family\. These files belong in the `<amazon-freertos>/lib/third_party/mcu_vendor` folder, which has the following structure:

```
<amazon-freertos>
+ - lib
    + - third_party    (Contains all non-board-specific, third-party libraries)
        + - mcu_vendor    (Contains vendor-supplied, MCU-specific libraries)
            + - <vendor>    (Template, to be renamed to the name of the MCU vendor)
                + - <driver_library>    (Template, to be renamed to the library name)
                    + - <driver_library_version>    (Template, to be renamed to the library version)
```

**To set up the directories for vendor\-supplied libraries that are common across an MCU family**

1. Save all required vendor\-supplied libraries that are common across a target board's MCU family in the `<driver_library_version>` folder\.

1. Rename the `<vendor>` folder to the name of the vendor, and rename the `<driver_library>` and `<driver_library_version>` folders to the name of the driver library and its version\.

**Important**  
Do not save vendor\-supplied libraries that are common across a target board's MCU family to any subdirectories of `<amazon-freertos>/tests` or `<amazon-freertos>/demos`\.

## Configuring `FreeRTOSConfig.h`<a name="w3aab9c13c11"></a>

After you have configured the directory structure of your Amazon FreeRTOS download, configure your board name in the `FreeRTOSConfig.h` configuration header file\.

**To configure your board name in `FreeRTOSConfig.h`**

1. Open `<amazon-freertos>/demos/<vendor>/<board>/common/config_files/FreeRTOSConfig.h`\.

1. In the line `#define configPLATFORM_NAME "<Unknown>"`, change *<Unknown>* to match the name of your board\.