# Configuring the HTTPS client library for testing<a name="afr-porting-https"></a>

The HTTPS Client library implements the HTTPS/1\.1 protocol over TLS for devices running FreeRTOS\.

## Prerequisites<a name="testing-prereqs-https"></a>

To set up the FreeRTOS HTTPS Client library tests, you need the following:
+ A port of the TLS library\.

  For information about porting the TLS library to your platform, see [Porting the TLS library](afr-porting-tls.md)\.

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

## Setting up the IDE test project<a name="testing-ide-https"></a>

**To set up the HTTPS client library in the IDE project**
+ Add all of the test source files in `freertos/libraries/c_sdk/standard/https` and its subdirectories to the `aws_tests` IDE project\.

## Configuring the `CMakeLists.txt` file<a name="testing-cmake-https"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in CMakeLists\.txt, follow the instructions in [FreeRTOS portable layers](cmake-template.md#cmake-portable)\.

The CMakeLists\.txt template list file under `freertos/vendors/vendor/boards/board/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

## Setting up your local testing environment<a name="testing-local-https"></a>

After you set up the library in the IDE project, you need to configure some other files for testing\.

**To configure the source and header files for the HTTPS client tests**
+ To enable the HTTPS Client tests, open `freertos/vendors/vendor/boards/board/aws_tests/config_files/aws_test_runner_config.h`, and set the `testrunnerFULL_HTTPS_CLIENT_ENABLED ` macro to `1`\.

## Running the tests<a name="testing-run-https"></a>

**To execute the HTTPS client tests**

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\. If all tests pass, then testing is complete\.