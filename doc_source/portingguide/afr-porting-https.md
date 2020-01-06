# Configuring the HTTPS Client Library for Testing<a name="afr-porting-https"></a>

The HTTPS Client library implements the HTTPS/1\.1 protocol over TLS for devices running Amazon FreeRTOS\.

## Prerequisites<a name="testing-prereqs-https"></a>

To set up the Amazon FreeRTOS HTTPS Client library tests, you need the following:
+ A port of the TLS library\.

  For information about porting the TLS library to your platform, see [Porting the TLS Library](afr-porting-tls.md)\.

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

## Setting Up the IDE Test Project<a name="testing-ide-https"></a>

**To set up the HTTPS Client library in the IDE project**
+ Add all of the test source files in `<amazon-freertos>/libraries/c_sdk/standard/https` and its subdirectories to the `aws_tests` IDE project\.

## Configuring the `CMakeLists.txt` File<a name="testing-cmake-https"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in CMakeLists\.txt, follow the instructions in [Amazon FreeRTOS Portable Layers](cmake-template.md#cmake-portable)\.

The CMakeLists\.txt template list file under `<amazon-freertos>/vendors/<vendor>/boards/<board>/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

## Setting Up Your Local Testing Environment<a name="testing-local-https"></a>

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the HTTPS Client tests**
+ To enable the HTTPS Client tests, open `<amazon-freertos>/vendors/<vendor>/boards/<board>/aws_tests/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_HTTPS_CLIENT_ENABLED ` macro to `1`\.

## Running the Tests<a name="testing-run-https"></a>

**To execute the HTTPS Client tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\. If all tests pass, then testing is complete\.