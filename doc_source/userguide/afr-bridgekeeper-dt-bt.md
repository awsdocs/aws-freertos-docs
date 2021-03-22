# Running Bluetooth Low Energy tests<a name="afr-bridgekeeper-dt-bt"></a>

This section describes how to set up and run the Bluetooth tests using AWS IoT Device Tester for FreeRTOS\. Bluetooth tests are not required for core qualification\. If you do not want to test your device with FreeRTOS Bluetooth support you may skip this setup, be sure to leave the BLE feature in device\.json set to `No`\.

## Prerequisites<a name="dt-bt-prereq"></a>
+ Follow the instructions in [Preparing to test your microcontroller board for the first time](qual-steps.md)\.
+ A Raspberry Pi 3B\+\. \(Required to run the Raspberry Pi BLE companion application\)
+ A micro SD card and SD card adapter for the Raspberry Pi software\.

 

## Raspberry Pi setup<a name="dt-bt-pi-setup"></a>

To test the BLE capabilities of the device under test \(DUT\), you must have a Raspberry Pi Model 3B\+\.

**To set up your Raspberry Pi to run BLE tests**

1. Download the custom [Yocto image](https://docs.aws.amazon.com/freertos/latest/userguide/afr/IDT_AFR_BLE_RaspberryPi_1.0.0.rpi-sdimg) that contains the software required to perform the tests\.
**Note**  
The Yocto image should only be used for testing with AWS IoT Device Tester for FreeRTOS and not for any other purpose\.

1. Flash the yocto image onto the SD card for Raspberry Pi\.

   1. Using an SD card\-writing tool such as [Etcher](https://www.balena.io/etcher), flash the downloaded `image-name.rpi-sdimg` file onto the SD card\. Because the operating system image is large, this step might take some time\. Then eject your SD card from your computer and insert the microSD card into your Raspberry Pi\.

1. Configure your Raspberry Pi\.

   1. For the first boot, we recommend that you connect the Raspberry Pi to a monitor, keyboard, and mouse\.

   1. Connect your Raspberry Pi to a micro USB power source\.

   1. Sign in using the default credentials\. For user ID, enter **root**\. For password, enter **idtafr**\.

   1. Using an Ethernet or Wi\-Fi connection, connect the Raspberry Pi to your network\.

      1. To connect your Raspberry Pi over Wi\-Fi, open `/etc/wpa_supplicant.conf` on the Raspberry Pi and add your Wi\-Fi credentials to the `Network` configuration\.

         ```
         ctrl_interface=/var/run/wpa_supplicant
         ctrl_interface_group=0
         update_config=1
         
         network={
                 scan_ssid=1
                 ssid="your-wifi-ssid"
                 psk="your-wifi-password"
                 }
         ```

      1. Run `ifup wlan0` to start the Wi\-Fi connection\. It might take a minute to connect to your Wi\-Fi network\.

   1. For an Ethernet connection, run `ifconfig eth0`\. For a Wi\-Fi connection, run `ifconfig wlan0`\. Make a note of the IP address, which appears as `inet addr` in the command output\. You need the IP address later in this procedure\.

   1. \(Optional\) The tests execute commands on the Raspberry Pi over SSH using the default credentials for the yocto image\. For additional security, we recommend that you set up public key authentication for SSH and disable password\-based SSH\.

      1. Create an SSH key using the OpenSSL `ssh-keygen` command\. If you already have an SSK key pair on your host computer, it is a best practice to create a new one to allow AWS IoT Device Tester for FreeRTOS to sign in to your Raspberry Pi\.
**Note**  
Windows does not come with an installed SSH client\. For information about how to install an SSH client on Windows, see [Download SSH Software](https://www.ssh.com/ssh/#sec-Download-client-software)\.

      1. The `ssh-keygen` command prompts you for a name and path to store the key pair\. By default, the key pair files are named `id_rsa` \(private key\) and `id_rsa.pub` \(public key\)\. On macOS and Linux, the default location of these files is `~/.ssh/`\. On Windows, the default location is `C:\Users\user-name`\.

      1. When you are prompted for a key phrase, just press ENTER to continue\.

      1. To add your SSH key onto your Raspberry Pi so AWS IoT Device Tester for FreeRTOS can sign into the device, use the `ssh-copy-id` command from your host computer\. This command adds your public key into the `~/.ssh/authorized_keys` file on your Raspberry Pi\.

         `ssh-copy-id root@raspberry-pi-ip-address`

      1. When prompted for a password, enter **idtafr**\. This is the default password for the yocto image\.
**Note**  
The `ssh-copy-id` command assumes the public key is named `id_rsa.pub`\. On macOS and Linux, the default location is ` ~/.ssh/`\. On Windows, the default location is `C:\Users\user-name\.ssh`\. If you gave the public key a different name or stored it in a different location, you must specify the fully qualified path to your SSH public key using the `-i` option to `ssh-copy-id` \(for example, `ssh-copy-id -i ~/my/path/myKey.pub`\)\. For more information about creating SSH keys and copying public keys, see [SSH\-COPY\-ID](https://www.ssh.com/ssh/copy-id)\.

      1. To test that the public key authentication is working, run `ssh -i /my/path/myKey root@raspberry-pi-device-ip`\.

         If you are not prompted for a password, your public key authentication is working\.

      1. Verify that you can sign in to your Raspberry Pi using a public key, and then disable password\-based SSH\.

         1. On the Raspberry Pi, edit the `/etc/ssh/sshd_config` file\.

         1. Set the `PasswordAuthentication` attribute to `no`\.

         1. Save and close the `sshd_config` file\.

         1. Reload the SSH server by running `/etc/init.d/sshd reload`\.

   1. Create a `resource.json` file\.

      1. In the directory in which you extracted AWS IoT Device Tester, create a file named `resource.json`\.

      1. Add the following information about your Raspberry Pi to the file, replacing *rasp\-pi\-ip\-address* with the IP address of your Raspberry Pi\.

         ```
         [
             {
                 "id": "ble-test-raspberry-pi",
                 "features": [
                     {"name":"ble", "version":"4.2"}
                 ],
                 "devices": [
                     {
                         "id": "ble-test-raspberry-pi-1",
                         "connectivity": {
                             "protocol": "ssh",
                             "ip": "rasp-pi-ip-address"
                         }
                     }
                 ]
             }
         ]
         ```

      1. If you didn't choose to use public key authentication for SSH, add the following to the `connectivity` section of the `resource.json` file\.

         ```
         "connectivity": {
             "protocol": "ssh",
             "ip": "rasp-pi-ip-address",
             "auth": {
                 "method": "password",
                 "credentials": {
                     "user": "root",
                     "password": "idtafr"
                 }
             }
         }
         ```

      1. \(Optional\) If you chose to use public key authentication for SSH, add the following to the `connectivity` section of the `resource.json` file\.

         ```
         "connectivity": {
             "protocol": "ssh",
             "ip": "rasp-pi-ip-address",
             "auth": {
                 "method": "pki",
                 "credentials": {
                     "user": "root",
                     "privKeyPath": "location-of-private-key"
                 }
             }
         }
         ```

## FreeRTOS device setup<a name="afr-device-setup"></a>

In your `device.json` file, set the `BLE` feature to `Yes`\. If you are starting with a `device.json` file from before Bluetooth tests were available, you need to add the feature for BLE to the `features` array:

```
{
    ...
    "features": [
        {
            "name": "BLE",
            "value": "Yes"
        },
    ...
}
```

## Running the BLE tests<a name="running-ble-test"></a>

After you have enabled the BLE feature in `device.json`, the BLE tests run when you run `devicetester_[linux | mac | win_x86-64] run-suite` *without specifying a group\-id*\.

If you want to run the BLE tests separately, you can specify the group ID for BLE: `devicetester_[linux | mac | win_x86-64] run-suite --userdata path-to-userdata/userdata.json --group-id FullBLE`\.

For the most reliable performance, place your Raspberry Pi close to the device under test \(DUT\)\.

## Troubleshooting BLE tests<a name="troubleshooting-ble"></a>

Make sure you have followed the steps in [Preparing to test your microcontroller board for the first time](qual-steps.md)\. If tests other than BLE are failing, then the problem is most likely not due to the Bluetooth configuration\.