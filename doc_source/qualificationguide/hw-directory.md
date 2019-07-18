# Configuring the Amazon FreeRTOS Download for Demos<a name="hw-directory"></a>

Under the download's root directory \(`<amazon-freertos>`\), the `vendors` folder is structured as follows:

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

The `aws_demos` folder has the following structure:

```
vendors/<vendor>/boards/<board>/aws_demos
+ - application_code    (Contains main.c, which contains main())
|   + - <vendor>_code    (Contains vendor-supplied, board-specific files)
|   + - main.c    (Contains main())
+ - config_files    (Contains Amazon FreeRTOS config files)
```

**To configure the demo project files**

Copy `main.c` and `main.h` files for the demo application to the `application_code` folder\. You can reuse the `main.c` from [the `aws_tests` project](https://docs.aws.amazon.com/freertos/latest/portingguide/porting-create-project.html) that you used to test your ports\.

1. Save any required vendor\-supplied, board\-specific libraries to the `<vendor>_code` folder\.
**Important**  
Do not save vendor\-supplied libraries that are common across a target board's MCU family to any subdirectories of `aws_tests` or `aws_demos`\.

1. Replace `<vendor>` in the `<vendor>_code` folder with the name of the vendor\.

1. Rename the `<board>` folder to the name of the development board\.

After you configure the demo project files, you can create the project in the IDE\. For instructions, see [Creating the Demo Project](demo-create-project.md)\.

If you are [creating a CMake listfile](afq-cmake.md), make sure that you provide a CMakelist entry for the demo project\.