# Getting started with the IDT\-FreeRTOS UI<a name="dev-tester-ui-getting-started"></a>

This section shows you how to use the IDT\-FreeRTOS UI to create or modify your configuration, and then shows you how to run tests\. 

**Topics**
+ [Configure AWS credentials](#configure-aws-credentials)
+ [Open the IDT\-FreeRTOS UI](#open-idt-ui)
+ [Create a new configuration](#create-new-configuration)
+ [Modify an existing configuration](#modify-existing-configuration)
+ [Run qualification tests](#run-tests-from-ui)

## Configure AWS credentials<a name="configure-aws-credentials"></a>

You must configure your IAM user credentials for the AWS user that you created in [Create and configure an AWS account](dev-tester-ui-prereqs.md#ui-config-aws-account)\. You can specify your credentials in one of two ways:
+ In a credentials file
+ As environment variables

### Configure AWS credentials with a credentials file<a name="config-cred-file"></a>

IDT uses the same credentials file as the AWS CLI\. For more information, see [Configuration and credential files](https://docs.aws.amazon.com/cli/latest/userguide/cli-config-files.html)\.

The location of the credentials file varies, depending on the operating system you're using:
+ macOS, Linux: `~/.aws/credentials`
+ Windows: `C:\Users\UserName\.aws\credentials`

Add your AWS credentials to the `credentials` file in the following format:

```
[default]
aws_access_key_id = <your_access_key_id>
aws_secret_access_key = <your_secret_access_key>
```

**Note**  
If you don't use the `default` AWS profile, be sure to specify the profile name in the IDT\-FreeRTOS UI\. For more information about profiles, see [ Named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)\.

### Configure AWS credentials with environment variables<a name="config-env-vars"></a>

Environment variables are variables maintained by the operating system and used by system commands\. They're not saved if you close the SSH session\. The IDT\-FreeRTOS UI uses the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables to store your AWS credentials\.

To set these variables on Linux, macOS, or Unix, use export:

```
export AWS_ACCESS_KEY_ID=<your_access_key_id>
export AWS_SECRET_ACCESS_KEY=<your_secret_access_key>
```

To set these variables on Windows, use set:

```
set AWS_ACCESS_KEY_ID=<your_access_key_id>
set AWS_SECRET_ACCESS_KEY=<your_secret_access_key>
```

## Open the IDT\-FreeRTOS UI<a name="open-idt-ui"></a>

**To open the IDT\-FreeRTOS UI**

1. Download a supported IDT\-FreeRTOS version and extract the downloaded archive into a location on your file system where you have read and write permissions\.

1. Run the following command to navigate to the IDT\-FreeRTOS installation directory:

   ```
   cd devicetester-extract-location/bin 
   ```

1. Run the following command to open the IDT\-FreeRTOS UI:

------
#### [ Linux ]

   ```
   .devicetestergui_linux_x86-64.exe
   ```

------
#### [ Windows ]

   ```
   ./devicetestergui_win_x64-64
   ```

------
#### [ macOS ]

   ```
   ./devicetestergui_mac_x86-64
   ```

**Note**  
On Mac, to allow your system to run the UI, go to **System Preferences \-> Security & Privacy**\. When you run the tests, you may need to do this three more times\.

------

   The IDT\-FreeRTOS UI opens in your default browser\. For information about supported browsers, see [Use a supported web browser](dev-tester-ui-prereqs.md#idt-ui-supported-web-browser)\.

## Create a new configuration<a name="create-new-configuration"></a>

If you're a first\-time user, then you must create a new configuration to set up the JSON configuration files that IDT\-FreeRTOS requires to run tests\. You can then run tests or modify the configuration that was created\.

For examples of the `config.json`, `device.json`, and `userdata.json` files, see [Preparing to test your microcontroller board for the first time](qual-steps.md)\. For an example of the `resource.json` file that is used only for running Bluetooth Low Energy \(BLE\) tests, see [Running Bluetooth Low Energy tests](afr-bridgekeeper-dt-bt.md)\.

**To create a new configuration**

1. In the IDT\-FreeRTOS UI, open the navigation menu, and then choose **Create new configuration**\.
**Important**  
You must configure your AWS credentials before you open the UI\. If you haven't configured your credentials, close the IDT\-FreeRTOS UI browser window, follow the steps in [Configure AWS credentials](#configure-aws-credentials), and then reopen the IDT\-FreeRTOS UI\.

1. Follow the configuration wizard to enter the IDT configuration settings that are used to run qualification tests\. The wizard configures the following settings in JSON configuration files that are located in the `devicetester-extract-location/config` directory\.
   + **AWS settings**—The AWS account information that IDT\-FreeRTOS uses to create AWS resources during test runs\. These settings are configured in the `config.json` file\.
   + **FreeRTOS repository**—The absolute path to the FreeRTOS repository and ported code, and the type of qualification you want to perform\. These settings are configured in the `userdata.json` file\.

     You must port FreeRTOS for your device before you can run qualification tests\. For more information, see the [FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/)
   + **Build and flash**—The build and flash commands for your hardware that allow IDT to build and flash tests on to your board automatically\. These settings are configured in the `userdata.json` file\.
   + **Devices**—The device pool settings for the devices to be tested\. These settings are configured in `id` and `sku` fields, and the `devices` block for the device pool in the `device.json` file\.
   + **Networking**—The settings to test network communication support for your devices\. These settings are configured in the `features` block of the `device.json` file, and in the `clientWifiConfig` and `testWifiConfig` blocks in the `userdata.json` file\.
   + **Echo server**—The echo server configuration settings for secure socket tests\. These settings are configured in the `userdata.json` file\.

     For more information about echo server configuration, see [https://docs.aws.amazon.com/freertos/latest/portingguide/afr-echo-server.html](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-echo-server.html)\.
   + **CMake**—\(Optional\) The settings to run CMake build functionality tests\. This configuration is required only if you're using CMake as your build system\. These settings are configured in the `userdata.json` file\.
   + **BLE**—The settings to run Bluetooth Low Energy functionality tests\. These settings are configured in the `features` block of the `device.json` file and in the `resource.json` file\.
   + **OTA**—The settings to run OTA functionality tests\. These settings are configured in the `features` block of the `device.json` file and in the `userdata.json` file\.

1.  On the **Review** page, verify your configuration information\. 

After you finish reviewing your configuration, to run your qualification tests, choose **Run tests**\.

## Modify an existing configuration<a name="modify-existing-configuration"></a>

If you have already set up configuration files for IDT, then you can use the IDT\-FreeRTOS UI to modify your existing configuration\. Make sure that your existing configuration files are available in the `devicetester-extract-location/config` directory\.

**To modify a new configuration**

1. In the IDT\-FreeRTOS UI, open the navigation menu, and then choose **Edit existing configuration**\.

   The configuration dashboard displays information about your existing configuration settings\. If a configuration is incorrect or unavailable, the status for that configuration is `Error validating configuration`\.

1. To modify an existing configuration setting, complete the following steps: 

   1. Choose the name of a configuration setting to open its settings page\.

   1. Modify the settings, and then choose **Save** to regenerate the corresponding configuration file\.

After you finish modifying your configuration, verify that all of your configuration settings pass validation\. If the status for each configuration setting is `Valid`, you can run your qualification tests using this configuration\.

## Run qualification tests<a name="run-tests-from-ui"></a>

After you have created a configuration for IDT\-FreeRTOS, you can run your qualification tests\.

**To run qualification tests**

1. Validate your configuration\.

1. In the navigation menu, choose **Run tests**\.

1. To start the test run, choose **Start tests**\.

IDT\-FreeRTOS runs the qualification tests, and displays the test run summary and any errors in the **Test runner** console\. After the test run is complete, you can view the test results and logs from the following locations: 
+ Test results are located in the `devicetester-extract-location/results/execution-id` directory\.
+ Test logs are located in the `devicetester-extract-location/results/execution-id/logs` directory\.

For more information about test results and logs, see [Understanding results and logs](results-logs.md)\.