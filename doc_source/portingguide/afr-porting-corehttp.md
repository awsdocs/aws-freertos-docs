# Configuring the coreHTTP library for testing<a name="afr-porting-corehttp"></a>

Devices on the edge can use the HTTP protocol to communicate with the AWS Cloud\. AWS IoT services host an HTTP server that sends and receives messages to and from connected devices at the edge\.

## Prerequisites<a name="testing-prereqs-corehttp"></a>

To set up the coreHTTP library tests, you need the following:
+ A port of the TLS library\.

  For information about porting the TLS library to your platform, see [Porting the TLS library](afr-porting-tls.md)\.

If you're using an IDE to build test projects, you must set up your library port in the IDE project\.

## Setting up the IDE test project<a name="testing-ide-corehttp"></a>

**To set up the coreHTTP library in the IDE project**

1. Add all of the test source files in the `freertos/libraries/coreHTTP/source` directory and its subdirectories to the `aws_tests` IDE project\.

1. Add all the source files for the network layer \(used in the tests\) in the `freertos/libraries/abstractions/transport` directory and the `secure_sockets` subdirectory to the `aws_tests` IDE project\.

1. Add a `core_http_config.h` file to the config files directory at `freertos/vendors/vendor/boards/board/aws_tests/config_files`\. This file is required to build the coreHTTP library\.

1. Add the test source file at `freertos/tests/integration_test/core_http_system.c` to the `aws_tests` IDE project\.

## Setting up your local testing environment<a name="testing-local-corehttp"></a>

After you set up the library in the IDE project, you must configure other files for testing\.

**To configure the source and header files for the HTTP tests**
+ To enable the HTTP tests, open the `freertos/vendors/vendor-name/boards/board-name/aws_tests/config_files/aws_test_runner_config.h` file and set the `testrunnerFULL_CORE_HTTP_AWS_IOT_ENABLED` macro to `1`\.

## Running the tests<a name="testing-run-corehttp"></a>

**To run the HTTP tests**

1. Build the test project, and then flash it to your device so that you can run it\.

1. Check the test results in the UART console\. If all tests pass, then testing is complete\.