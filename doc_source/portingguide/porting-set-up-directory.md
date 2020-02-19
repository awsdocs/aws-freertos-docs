# Configuring the FreeRTOS Download<a name="porting-set-up-directory"></a>

Follow the instructions below to configure the FreeRTOS download for porting FreeRTOS code to your device\.

## Configuring Directories for Vendor\-supplied, Board\-specific Libraries<a name="w3aab9c13b5"></a>

Under the download's root directory \(`<freertos>`\), the `vendors` folder is structured as follows:

```
vendors
+ - <vendor>    (Template, to be renamed to the name of the MCU vendor)
    + - boards    
    |   + - <board>    (Template, to be renamed to the name of the development board)
    |       + - aws_demos
    |       + - aws_tests
    |       + - CMakeLists.txt
    |       + - ports
    + - <driver_library>    (Template, to be renamed to the library name)
        + - <driver_library_version>    (Template, to be renamed to the library version)
```

The `<vendor> and ``<board>` folders are template folders that we provide to make it easier to create demo and test projects\. Their directory structure ensures that all demo and test projects have a consistent organization\.

The `aws_tests` folder has the following structure:

```
vendors/<vendor>/boards/<board>/aws_tests
+ - application_code    (Contains main.c, which contains main())
|   + - <vendor>_code    (Contains vendor-supplied, board-specific files)
|   + - main.c    (Contains main())
+ - config_files    (Contains FreeRTOS config files)
```

All test projects require vendor\-supplied driver libraries\. Some vendor\-supplied files, such as a header file that maps GPIO output to an LED light, are specific to a target development board\. These files belong in the `<vendor>_code` folder\.

Other vendor\-supplied files, such as a GPIO library, are common across a board's MCU family\. These files belong in the *<driver\_library>* folder\.

**To set up the directories for vendor\-supplied libraries that are common across an MCU family**

1. Save all required vendor\-supplied libraries that are common across a target board's MCU family in the `<driver_library_version>` folder\.

1. Rename the `<vendor>` folder to the name of the vendor, and rename the `<driver_library>` and `<driver_library_version>` folders to the name of the driver library and its version\.

**Important**  
Do not save vendor\-supplied libraries that are common across a target board's MCU family to any subdirectories of `<freertos>/test` or `<freertos>/demos`\.

## Configuring Directories for Project Files<a name="w3aab9c13b7"></a>

Under `<freertos>`, the `projects` folder is structured as follows:

```
projects
+ - <vendor>    (Template, to be renamed to the name of the MCU vendor)
    + - <board>    (Template, to be renamed to the name of the development board)
        + - <ide>    (Contains an IDE-specific project)
        + - visual_studio    (contains project files for Visual Studio)
```

**To set up the project directories**

1. Rename the `<ide>` folder to the name of the IDE that you are using to build the test project\.

1. Rename the `<vendor>` folder to the name of the vendor, and rename the `<board>` folder to the name of the development board\.

## Configuring `FreeRTOSConfig.h`<a name="w3aab9c13b9"></a>

After you have configured the directory structure of your FreeRTOS download, configure your board name in the `FreeRTOSConfig.h` configuration header file\.

**To configure your board name in `FreeRTOSConfig.h`**

1. Open `<freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/FreeRTOSConfig.h`\.

1. In the line `#define configPLATFORM_NAME "<Unknown>"`, change *<Unknown>* to match the name of your board\.