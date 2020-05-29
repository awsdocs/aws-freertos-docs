# Porting the Bluetooth Low Energy library<a name="afr-porting-ble"></a>

You can use the FreeRTOS Bluetooth Low Energy library to provision Wi\-Fi and send MQTT messages over Bluetooth Low Energy\. The Bluetooth Low Energy library also includes higher\-level APIs that you can use to communicate directly with the Bluetooth Low Energy stack\. For more information, see [FreeRTOS Bluetooth Low Energy Library](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ble-library.html) in the FreeRTOS User Guide\.

**Note**  
A port of the FreeRTOS Bluetooth Low Energy library is currently not required for qualification\.

## Prerequisites<a name="porting-prereqs-ble"></a>

To port the Bluetooth Low Energy library, you need the following:
+ An IDE project or `CMakeLists.txt` list file that includes the vendor\-supplied Bluetooth Low Energy drivers\.

  For information about setting up a test project, see [Setting Up Your FreeRTOS Source Code for Porting](porting-set-up-project.md)\.
+ A validated configuration of the FreeRTOS kernel\.

  For information about configuring the FreeRTOS kernel for your platform, see [Configuring a FreeRTOS kernel port](afr-porting-kernel.md)\.
+ A [Raspberry Pi 3 Model B\+](https://www.raspberrypi.org/products/raspberry-pi-3-model-b-plus/), with a memory card\.

## Porting<a name="porting-steps-ble"></a>

Three files in the `freertos/libraries/abstractions/ble_hal/include` folder define the FreeRTOS Bluetooth Low Energy APIs:
+ `bt_hal_manager.h`
+ `bt_hal_manager_adapter_ble.h`
+ `bt_hal_gatt_server.h`

Each file includes comments that describe the APIs\. You must implement the following APIs:

**`bt_hal_manager.h`**
+ `pxBtManagerInit`
+ `pxEnable`
+ `pxDisable`
+ `pxGetDeviceProperty`
+ `pxSetDeviceProperty` \(All options are mandatory except `eBTpropertyRemoteRssi` and `eBTpropertyRemoteVersionInfo`\)
+ `pxPair`
+ `pxRemoveBond`
+ `pxGetConnectionState`
+ `pxPinReply`
+ `pxSspReply`
+ `pxGetTxpower`
+ `pxGetLeAdapter`
+ `pxDeviceStateChangedCb`
+ `pxAdapterPropertiesCb`
+ `pxSspRequestCb`
+ `pxPairingStateChangedCb`
+ `pxTxPowerCb`

**`bt_hal_manager_adapter_ble.h`**
+ `pxRegisterBleApp`
+ `pxUnregisterBleApp`
+ `pxBleAdapterInit`
+ `pxStartAdv`
+ `pxStopAdv`
+ `pxSetAdvData`
+ `pxConnParameterUpdateRequest`
+ `pxRegisterBleAdapterCb`
+ `pxAdvStartCb`
+ `pxSetAdvDataCb`
+ `pxConnParameterUpdateRequestCb`
+ `pxCongestionCb`

**`bt_hal_gatt_server.h`**
+ `pxRegisterServer`
+ `pxUnregisterServer`
+ `pxGattServerInit`
+ `pxAddService`
+ `pxAddIncludedService`
+ `pxAddCharacteristic`
+ `pxSetVal`
+ `pxAddDescriptor`
+ `pxStartService`
+ `pxStopService`
+ `pxDeleteService`
+ `pxSendIndication`
+ `pxSendResponse`
+ `pxMtuChangedCb`
+ `pxCongestionCb`
+ `pxIndicationSentCb`
+ `pxRequestExecWriteCb`
+ `pxRequestWriteCb`
+ `pxRequestReadCb`
+ `pxServiceDeletedCb`
+ `pxServiceStoppedCb`
+ `pxServiceStartedCb`
+ `pxDescriptorAddedCb`
+ `pxSetValCallbackCb`
+ `pxCharacteristicAddedCb`
+ `pxIncludedServiceAddedCb`
+ `pxServiceAddedCb`
+ `pxConnectionCb`
+ `pxUnregisterServerCb`
+ `pxRegisterServerCb`

## Testing<a name="porting-testing-ble"></a>

This diagram shows the Bluetooth Low Energy testing framework\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/ble-port-test.png)

To test your Bluetooth Low Energy ports, your computer communicates with an external, Bluetooth\-enabled device \(a Raspberry Pi 3 Model B\+\) over SSH, and with your device over Bluetooth Low Energy\.

The Bluetooth Low Energy porting and qualification tests target the low\-level wrapper layer that lies just above the manufacturer's hardware stack in the FreeRTOS Bluetooth Low Energy architecture:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/portingguide/images/ble-architecture-porting.png)

If you are using an IDE to build test projects, you need to set up your library port in the IDE project\.

### Setting up the IDE test project<a name="testing-ide-ble"></a>

If you are using an IDE for porting and testing, you need to add some source files to the IDE test project before you can test your ported code\.

**Important**  
In the following steps, make sure that you add the source files to your IDE project from their on\-disk location\. Do not create duplicate copies of source files\.

**To set up the Bluetooth Low Energy library in the IDE project**

1. Add all of the files in `freertos/vendors/vendor/boards/board/ports/ble` to your `aws_tests` IDE project\.

1. Add all of the files in `freertos/libraries/abstractions/ble_hal/include` to your `aws_tests` IDE project\.

1. Add all of the files in `freertos/libraries/c_sdk/standard/ble` to your `aws_tests` IDE project\.

1. Open `freertos/vendors/vendor/boards/board/aws_tests/application_code/main.c`, and enable the required Bluetooth Low Energy drivers\.

### Configuring the `CMakeLists.txt` file<a name="testing-cmake-ble"></a>

If you are using CMake to build your test project, you need to define a portable layer target for the library in your CMake list file\.

To define a library's portable layer target in `CMakeLists.txt`, follow the instructions in [FreeRTOS portable layers](cmake-template.md#cmake-portable)\.

The `CMakeLists.txt` template list file under `freertos/vendors/vendor/boards/board/CMakeLists.txt` includes example portable layer target definitions\. You can uncomment the definition for the library that you are porting, and modify it to fit your platform\.

### Setting up your local testing environment<a name="testing-local-ble"></a>

**To set up the Raspberry Pi for testing**

1. Follow the instructions in [Setting up your Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up) to set up your Raspberry Pi with Raspbian OS\.

1. Download bluez 5\.50 from the [kernel\.org repository](https://git.kernel.org/pub/scm/bluetooth/bluez.git)\.

1. Follow the instructions in the [README](https://git.kernel.org/pub/scm/bluetooth/bluez.git/about/) on the kernel\.org repository to install bluez 5\.50 on the Raspberry Pi\.

1. Enable SSH on the Raspberry Pi\. For instructions, see the [Raspberry Pi documentation](https://www.raspberrypi.org/documentation/remote-access/ssh/)\.

1. On your computer, open the `freertos/tests/bleTestsScripts/runPI.sh` script, and change the IP addresses in the first two lines to the IP address of your Raspberry Pi:

   ```
   #!/bin/sh
   
   scp * root@192.168.1.4: 
   ssh -t -t 192.168.1.4 -l root << 'ENDSSH'
   rm -rf "/var/lib/bluetooth/*"
   hciconfig hci0 reset
   python test1.py
   sleep 1
   ENDSSH
   ```

### Running the tests<a name="testing-run-ble"></a>

**To execute the Bluetooth Low Energy tests**

1. Execute the `runPI.sh` script\.

1. Build the test project, and then flash it to your device for execution\.

1. Check the test results in the UART console\.

## Validation<a name="w3aac11c39c13"></a>

To officially qualify a device for FreeRTOS, you need to validate the device's ported source code with AWS IoT Device Tester\. Follow the instructions in [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide to set up Device Tester for port validation\. To test a specific library's port, the correct test group must be enabled in the `device.json` file in the Device Tester `configs` folder\.

After you have ported the Bluetooth Low Energy library, you can start the FreeRTOS qualification process\. For more information, see the [FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\.