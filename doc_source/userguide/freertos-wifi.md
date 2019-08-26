# Amazon FreeRTOS Wi\-Fi Library<a name="freertos-wifi"></a>

## Overview<a name="freertos-wifi-overview"></a>

The Amazon FreeRTOS Wi\-Fi library abstracts port\-specific Wi\-Fi implementations into a common API that simplifies application development and porting for all Amazon FreeRTOS\-qualified boards with Wi\-Fi capabilities\. Using this common API, applications can communicate with their lower\-level wireless stack through a common interface\.

## Dependencies and Requirements<a name="freertos-wifi-dependencies"></a>

The Amazon FreeRTOS Wi\-Fi library requires the [FreeRTOS\+TCP](https://freertos.org/FreeRTOS-Plus/FreeRTOS_Plus_TCP/index.html) core\.

## Features<a name="freertos-wifi-features"></a>

The Wi\-Fi library includes the following features:
+ Support for WEP, WPA, and WPA2 authentication
+ Access Point Scanning
+ Power management
+ Network profiling

For more information about the features of the Wi\-Fi library, see below\.

### Wi\-Fi Modes<a name="freertos-wifi-setup"></a>

Wi\-Fi devices can be in one of three modes: Station, Access Point, or P2P\. You can get the current mode of a Wi\-Fi device by calling `WIFI_GetMode`\. You can set a device's wi\-fi mode by calling `WIFI_SetMode`\. Switching modes by calling `WIFI_SetMode` disconnects the device, if it is already connected to a network\.

Station mode  
Set your device to Station mode to connect the board to an existing access point\.

Access Point \(AP\) mode  
Set your device to AP mode to make the device an access point for other devices to connect to\. When your device is in AP mode, you can connect another device to your FreeRTOS device and configure the new Wi\-Fi credentials\. To configure AP mode, call `WIFI_ConfigureAP`\. To put your device into AP mode, call `WIFI_StartAP`\. To turn off AP mode, call `WIFI_StopAP`\.

P2P mode  
Set your device to P2P mode to allow multiple devices to connect to each other directly, without an access point\.

### Security<a name="freertos-wifi-security"></a>

The Wi\-Fi API supports WEP, WPA, and WPA2 security types\. When a device is in Station mode, you must specify the network security type when calling the `WIFI_ConnectAP` function\. When a device is in AP mode, the device can be configured to use any of the supported security types:
+ `eWiFiSecurityOpen`
+ `eWiFiSecurityWEP`
+ `eWiFiSecurityWPA`
+ `eWiFiSecurityWPA2`

### Scanning and Connecting<a name="freertos-wifi-connection"></a>

To scan for nearby access points, set your device to Station mode, and call the `WIFI_Scan` function\. If you find a desired network in the scan, you can connect to the network by calling `WIFI_ConnectAP` and providing the network credentials\. You can disconnect a Wi\-Fi device from the network by calling `WIFI_Disconnect`\. For more information about scanning and connecting, see [Example Usage](#freertos-wifi-example) and [API Reference](#freertos-wifi-api)\.

### Power Management<a name="freertos-wifi-power-management"></a>

Different Wi\-Fi devices have different power requirements, depending on the application and available power sources\. A device might always be powered on to reduce latency or it might be intermittently connected and switch into a low power mode when Wi\-Fi is not required\. The interface API supports various power management modes like always on, low power, and normal mode\. You set the power mode for a device using the `WIFI_SetPMMode` function\. You can get the current power mode of a device by calling the `WIFI_GetPMMode` function\.

### Network Profiles<a name="freertos-wifi-profiles"></a>

The Wi\-Fi library enables you to save network profiles in the non\-volatile memory of your devices\. This allows you to save network settings so they can be retrieved when a device reconnects to a Wi\-Fi network, removing the need to provision devices again after they have been connected to a network\. `WIFI_NetworkAdd` adds a network profile\. `WIFI_NetworkGet` retrieves a network profile\. `WIFI_NetworkDel` deletes a network profile\. The number of profiles you can save depends on the platform\.

## Configuration<a name="freertos-wifi-config"></a>

To use the Wi\-Fi library, you need to define several identifiers in a configuration file\. For information about these identifiers, see the [API Reference](#freertos-wifi-api)\.

**Note**  
The library does not include the required configuration file\. You must create one\. When creating your configuration file, be sure to include any board\-specific configuration identifiers that your board requires\.

## Initialization<a name="freertos-wifi-init"></a>

Before you use the Wi\-Fi library, you need to initialize some board\-specific components, in addition to the FreeRTOS components\. Using the `vendors/<vendor>/boards/<board>/aws_demos/application_code/main.c` file as a template for initialization, do the following:

1. Remove the sample Wi\-Fi connection logic in `main.c` if your application handles Wi\-Fi connections\. Replace the following `DEMO_RUNNER_RunDemos()` function call:

   ```
   if( SYSTEM_Init() == pdPASS )
   	{
   	...
   		DEMO_RUNNER_RunDemos();
   	...
   		}
   ```

   With a call to your own application:

   ```
   if( SYSTEM_Init() == pdPASS )
   	{
   	...
   		// This function should create any tasks
   		// that your application requires to run.
   		YOUR_APP_FUNCTION();
   	...
   		}
   ```

1. Call `WIFI_On()` to initialize and power on your Wi\-Fi chip\.
**Note**  
Some boards might require additional hardware initialization\.

1. Pass a configured `WFINetworkParams_t` structure to `WIFI_ConnectAP()` to connect your board to an available Wi\-Fi network\. For more information about the `WFINetworkParams_t` structure, see [Example Usage](#freertos-wifi-example) and [API Reference](#freertos-wifi-api)\.

## API Reference<a name="freertos-wifi-api"></a>

For a full API reference, see [Wi\-Fi API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html2/wifi/index.html)\.

## Example Usage<a name="freertos-wifi-example"></a>

### Connecting to a Known AP<a name="w3aab9c39c17b3"></a>

```
#define clientcredentialWIFI_SSID    "MyNetwork"
#define clientcredentialWIFI_PASSWORD   "hunter2"

INetworkParams_t xNetworkParams;
WIFIReturnCode_t xWifiStatus;

xWifiStatus = WIFI_On(); // Turn on Wi-Fi module

// Check that Wi-Fi initialization was successful
if( xWifiStatus == eWiFiSuccess )
{
    configPRINT( ( "WiFi library initialized.\n") );
}
else
{
    configPRINT( ( "WiFi library failed to initialize.\n" ) );
    // Handle module init failure
}

/* Setup parameters. */
xNetworkParams.pcSSID = clientcredentialWIFI_SSID;
xNetworkParams.ucSSIDLength = sizeof( clientcredentialWIFI_SSID );
xNetworkParams.pcPassword = clientcredentialWIFI_PASSWORD;
xNetworkParams.ucPasswordLength = sizeof( clientcredentialWIFI_PASSWORD );
xNetworkParams.xSecurity = eWiFiSecurityWPA2;

// Connect!
xWifiStatus = WIFI_ConnectAP( &( xNetworkParams ) );

if( xWifiStatus == eWiFiSuccess )
{
    configPRINT( ( "WiFi Connected to AP.\n" ) );
    // IP Stack will receive a network-up event on success
}
else
{
    configPRINT( ( "WiFi failed to connect to AP.\n" ) );
    // Handle connection failure
}
```

### Scanning for nearby APs<a name="w3aab9c39c17b5"></a>

```
WIFINetworkParams_t xNetworkParams;
WIFIReturnCode_t xWifiStatus;

configPRINT( ("Turning on wifi...\n") );
xWifiStatus = WIFI_On();

configPRINT( ("Checking status...\n") );
if( xWifiStatus == eWiFiSuccess )
{
    configPRINT( ("WiFi module initialized.\n") );
}
else
{
    configPRINTF( ("WiFi module failed to initialize.\n" ) );
    // Handle module init failure
}

WIFI_SetMode(eWiFiModeStation);

/* Some boards might require additional initialization steps to use the Wi-Fi library. */

while (1)
{
    configPRINT( ("Starting scan\n") );
    const uint8_t ucNumNetworks = 12; //Get 12 scan results
    WIFIScanResult_t xScanResults[ ucNumNetworks ];
    xWifiStatus = WIFI_Scan( xScanResults, ucNumNetworks ); // Initiate scan

    configPRINT( ("Scan started\n") );

    // For each scan result, print out the SSID and RSSI
    if ( xWifiStatus == eWiFiSuccess )
    {
        configPRINT( ("Scan success\n") );
        for ( uint8_t i=0; i<ucNumNetworks; i++ ) 
        {
            configPRINTF( ("%s : %d \n", xScanResults[i].cSSID, xScanResults[i].cRSSI) );
        }
    } else {
        configPRINTF( ("Scan failed, status code: %d\n", (int)xWifiStatus) );
    }
    
    vTaskDelay(200);
}
```

## Porting<a name="freertos-wifi-porting"></a>

The `aws_wifi.c` implementation needs to implement the functions defined in `aws_wifi.h`\. At the very least, the implementation needs to return `eWiFiNotSupported` for any non\-essential or unsupported functions\.

For more information about porting the Wi\-Fi library, see [Porting the Wi\-Fi Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-wifi.html) in the Amazon FreeRTOS Porting Guide\.