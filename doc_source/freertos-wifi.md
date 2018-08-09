# FreeRTOS Wi\-Fi Interface<a name="freertos-wifi"></a>

The Wi\-Fi interface API provides a common abstraction layer that enables applications to communicate with the lower\-level wireless stack\. Wi\-Fi chip sets differ in features, driver implementations, and APIs\. The common Wi\-Fi interface simplifies application development and porting for all supported Wi\-Fi chip sets\. The interface provides a primary API for managing all aspects of Wi\-Fi devices\. 

## Setup, Provisioning, and Configuration<a name="freertos-wifi-setup"></a>

The setup APIs provide functionality to turn on Wi\-Fi by initializing the radio, peripherals, and drivers\. Your application must turn on Wi\-Fi by calling `WIFI_On` before calling any other API\. An application can turn off WI\-Fi by calling `WIFI_Off` to save power\. This is useful for power\-constrained devices that have intermittent connectivity\. Calling `WIFI_Reset` resets the Wi\-Fi radio\.

The Amazon FreeRTOS demos hard code Wi\-Fi credentials into the application\. If you cannot connect to your Wi\-FI network using these credentials, you can put your FreeRTOS device into soft Access Point \(AP\) mode\. This allows you to connect the FreeRTOS device and configure a different set of Wi\-Fi credentials \(SSID, password, security type, and channel\)\. To configure AP mode, call `WIFI_ConfigureAP`\. To put your device into soft AP mode, call `WIFI_StartAP`\. When your device is in soft AP mode, you can connect another device, using a web browser to your FreeRTOS device and configure the new Wi\-Fi credentials\. To turn off soft AP mode, call `WIFI_StopAP`\.

A Wi\-Fi device can be configured in a particular role at a time\. Device roles like Station, Access Point, P2P can be configured by calling `WIFI_SetMode`\. You can get the current mode of a Wi\-Fi device by calling `WIFI_GetMode`\. Switching modes by calling `WIFI_SetMode` disconnects the device, if it is already connected to a network\.

## Connection<a name="freertos-wifi-connection"></a>

A Wi\-Fi device turned on and switched to Station mode is ready to connect to the network using the connectivity API\. When calling the connection API, you pass network parameters like SSID, password, and security type to establish a connection\. You can perform a scan operation to look for networks\. The scan returns the SSID, BSSID, Channel, RSSI, and security type\. The scan can be performed for hidden networks\. If you find a desired network in the scan, you can connect to the network by calling and providing the network password\. You can disconnect a Wi\-Fi device from the network by calling `WIFI_Disconnect`\.

## Security<a name="freertos-wifi-security"></a>

The interface API supports several security types like WEP, WPA, WPA2, and Open \(no security\)\. When a device is in the Station role, you must specify the network security type when calling the `WIFI_ConnectAP` function\. When a device is in soft AP mode, the device can be configured to use any of the supported security types:
+ `eWiFiSecurityOpen`
+ `eWiFiSecurityWEP`
+ `eWiFiSecurityWPA`
+ `eWiFiSecurityWPA2`

## Power Management<a name="freertos-wifi-power-management"></a>

Different Wi\-Fi devices have different power requirements, depending on the application and available power sources\. A device might always be powered on to reduce latency or it might be intermittently connected and switch into a low power mode when Wi\-Fi is not required\. The interface API supports various power management modes like always on, low power, and normal mode\. You set the power mode for a device using the `WIFI_SetPMMode` function\. You can get the current power mode of a device by calling the `WIFI_GetPMMode` function\.

## Network Profiles<a name="freertos-wifi-profiles"></a>

The Wi\-Fi API enables you to save network profiles in the non\-volatile memory of your devices\. This allows you to save network settings so they can be retrieved when a device reconnects to a Wi\-Fi network, removing the need to provision devices again after they have been connected to a network\. `WIFI_NetworkAdd` adds a network profile\. `WIFI_NetworkGet` retrieves a network profile\. `WIFI_NetworkDel` deletes a network profile\. The number of profiles you can save depends on the platform\.

## Network Utilities<a name="freertos-wifi-utilities"></a>

The Wi\-Fi API also provides utility functions described in the following table:


****  

| API | Description | 
| --- | --- | 
|  `WIFI_GetIP`  |  Gets the IP address of a device\.  | 
|  `WIFI_GetHostIP`  |  Gets the host IP address\.  | 
|  `WIFI_GetMAC`  |  Gets the MAC address of a device\.  | 
|  `WIFI_Ping`  |  Sends a ping to a device on the network\.  | 
|  `WIFI_Scan`  |  Scans for available Wi\-Fi networks\.  | 