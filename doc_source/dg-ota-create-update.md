# Update the Version of your Firmware<a name="dg-ota-create-update"></a>

The OTA agent that comes with Amazon FreeRTOS checks the version of any update and only installs it if it is newer than the existing firmware version\. The following steps show you how to increment the firmware version of the OTA demo application\.

1. Open the `aws_demos` project in your IDE\.

1. Open `aws_demos/application_code/common_demos/include/aws_application_version.h` and increment the `APP_VERSION_BUILD` token value to 1\.

1. Rebuild the project\.

The complete version of the firmware will now be 0\.9\.1\.