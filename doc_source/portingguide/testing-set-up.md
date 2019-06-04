# Setting Up Your Amazon FreeRTOS Source Code for Testing<a name="testing-set-up"></a>

Amazon FreeRTOS includes tests for each ported library in folders under `<amazon-freertos>/tests/common`\. The `tests/common/test_runner/aws_test_runner.c` defines a `RunTests` function that runs each test that you have specified in the `/tests/vendor/board/common/config_files/aws_test_runner_config.h` header file\. As you port each Amazon FreeRTOS library, you can test the ports by building the ported Amazon FreeRTOS source code, flashing the compiled code to your board, and running it on the board\.

To build the Amazon FreeRTOS source code for testing, use a supported IDE\. For instructions on creating an IDE project for development and testing, see [Creating an IDE Project](porting-create-project.md)\.

After you build the code, use your platform's flash utility to flash the compiled code to your device\.

**Note**  
You specify your build and flash tools in the `userdata.json` file for Device Tester, so you do not need to flash your code manually if you are using Device Tester\.