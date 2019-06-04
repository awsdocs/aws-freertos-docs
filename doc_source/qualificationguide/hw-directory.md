# Configuring the Amazon FreeRTOS Download for Demos<a name="hw-directory"></a>

Under the download's root directory \(`<amazon-freertos>`\), there are five folders: `cmake`, `demos`, `lib`, `tests`, and `tools`\.

```
<amazon-freertos>
+ - cmake    (Contains files for CMake support)
+ - demos    (Contains projects that build demo applications)
+ - lib    (Contains Amazon FreeRTOS and third-party libraries)
+ - tests    (Contains projects that build porting tests)
+ - tools    (Contains tools for configuration, development, and testing)
```

The `demos` folder is structured as follows:

```
<amazon-freertos>
+ - demos
    + - common    (Contains files built by all demo projects)
    + - pc    (Contains a demo project for the FreeRTOS Windows port)
    + - <vendor>    (Template, to be renamed to the name of the MCU vendor)
        + - <board>    (Template, to be renamed to the name of the development board)
```

The `<board>` folder is a template folder we provide to make it easier to create a demo project\. Its directory structure ensures that all demo projects have a consistent organization\. The `<board>` folder has the following structure:

```
<amazon-freertos>
+ - demos
    + - <vendor>
        + - <board>
            + - common
            |   + - application_code    (Contains main.c, which contains main())
            |   |   + - <vendor>_code    (Contains vendor-supplied, board-specific files)
            |   + - config_files    (Contains Amazon FreeRTOS config files)
            + - <ide>    (Contains an IDE-specific project)
```

**To configure the demo project files**

Copy `main.c` and `main.h` files for the demo application to the `application_code` folder\. You can reuse the `main.c` from [the `aws_tests` project that you used to test your ports](https://docs.aws.amazon.com/freertos/latest/portingguide/porting-create-project.html)\.

1. Save any required vendor\-supplied, board\-specific libraries to the `<vendor>_code` folder\.
**Important**  
Do not save vendor\-supplied libraries that are common across a target board's MCU family to any subdirectories of `<amazon-freertos>/tests` or `<amazon-freertos>/demos`\.

1. Replace `<vendor>` in the `<vendor>_code` folder with the name of the vendor\.

1. Rename the `<board>` folder to the name of the development board\.

After you configure the demo project files, you can create the project in the IDE\. For instructions, see [Creating the Demo Project](demo-create-project.md)\.