# Amazon FreeRTOS Bluetooth Low Energy Library \(Beta\)<a name="freertos-ble-library"></a>

## Overview<a name="freertos-ble-overview"></a>


|  | 
| --- |
| The Bluetooth Low Energy \(BLE\) Library is in public beta release for Amazon FreeRTOS and is subject to change\. | 

Amazon FreeRTOS supports publishing and subscribing to MQTT topics over Bluetooth Low Energy \(BLE\) through a proxy device, such as a mobile phone\. With the Amazon FreeRTOS BLE library, your microcontroller can securely communicate with the AWS IoT MQTT broker\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/blediagram.png)

Using the Amazon FreeRTOS BLE Mobile SDKs, you can write native mobile applications that communicate with the embedded applications on your microcontroller over BLE\. For more information about the Amazon FreeRTOS BLE Mobile SDKs, see [Mobile SDKs for Amazon FreeRTOS Bluetooth Devices](freertos-ble-mobile.md)\. Amazon FreeRTOS BLE uses Amazon Cognito for user authentication on mobile devices\. 

In addition to supporting MQTT, the Amazon FreeRTOS BLE library includes services for configuring Wi\-Fi networks\. The Amazon FreeRTOS BLE library also includes some middleware and lower\-level APIs for more direct control over your BLE stack\. The source files for the Amazon FreeRTOS BLE library are located in [https://github.com/aws/amazon-freertos/tree/feature/ble-beta/lib/bluetooth_low_energy](https://github.com/aws/amazon-freertos/tree/feature/ble-beta/lib/bluetooth_low_energy)\. 

### Amazon FreeRTOS BLE Architecture<a name="freertos-ble-arch"></a>

The Amazon FreeRTOS BLE library is made up of three layers: services, middleware, and low\-level wrappers\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/BLE-architecture.png)

#### Services<a name="freertos-ble-arch-services"></a>

The Amazon FreeRTOS BLE services layer consists of three Generic Attributes \(GATT\) services that leverage the middleware APIs: Device Information, Wi\-Fi Provisioning, and MQTT Communications over BLE\. For more information, see [Services](#freertos-ble-services)\.

#### Middleware<a name="freertos-ble-arch-middleware"></a>

Amazon FreeRTOS BLE middleware is an abstraction from the lower\-level APIs\. The middleware APIs make up a more user\-friendly interface to the BLE stack\. For more information, see [Middleware](#freertos-ble-middleware)\.

#### Low\-level Wrappers<a name="freertos-ble-arch-lowlevel"></a>

The low\-level Amazon FreeRTOS BLE wrappers are an abstraction from the manufacturer's BLE stack\. Low\-level wrappers offer a common set of APIs for direct control over the hardware\. The low\-level APIs optimize RAM usage, but are limited in functionality\. To use the Amazon FreeRTOS BLE services, you interact with the BLE service APIs, which demand more resources than the low\-level APIs\. 

## Dependencies and Requirements<a name="freertos-ble-dependencies"></a>

Only the MQTT over BLE and Wi\-Fi Provisioning services have library dependencies\.


****  

| GATT Service | Dependency | 
| --- | --- | 
| MQTT over BLE | [Amazon FreeRTOS MQTT Library \(Beta\)](freertos-mqtt-2.md) | 
| Wi\-Fi Provisioning | [Amazon FreeRTOS Wi\-Fi Library](freertos-wifi.md) | 

To communicate with the AWS IoT MQTT broker, you must have an AWS account and you must register your devices as AWS IoT things\. For more information about setting up, see the [AWS IoT Developer Guide](https://docs.aws.amazon.com/iot/latest/developerguide/)\.

Amazon FreeRTOS BLE uses Amazon Cognito for user authentication on your mobile device\. To use MQTT proxy services, you must create an Amazon Cognito identity and user pools\. Each Amazon Cognito Identity must have the appropriate policy attached to it\. For more information, see the [Amazon Cognito Developer Guide](https://docs.aws.amazon.com/cognito/latest/developerguide/)\.

## Features<a name="freertos-ble-features"></a>

### Services<a name="freertos-ble-services"></a>

#### Device Information<a name="ble-device-information"></a>

The Device Information service gathers information about your microcontroller, including:
+ The version of Amazon FreeRTOS that your device is using\.
+ The AWS IoT endpoint of the account for which the device is registered\.
+ BLE Maximum Transmission Unit \(MTU\)\.

#### Wi\-Fi Provisioning<a name="ble-wifi-provisioning"></a>

The Wi\-Fi Provisioning service enables microcontrollers with Wi\-Fi capabilities to do the following:
+ List networks in range\.
+ Save networks and network credentials to flash memory\.
+ Set network priority\.
+ Delete networks and network credentials from flash memory\.

#### MQTT over BLE<a name="ble-device-information"></a>

The MQTT over BLE service connects your microcontroller to Bluetooth\-enabled mobile devices to indirectly connect to the AWS IoT cloud with AWS Mobile SDKs\. The microcontroller functions as an MQTT client, the mobile device as an MQTT proxy, and the AWS IoT cloud as the MQTT server\.

### Middleware<a name="freertos-ble-middleware"></a>

Using middleware APIs, you can register several callbacks, across multiple layers, to a single event\.

#### Flexible Callback Subscription<a name="freertos-ble-flexible-callbacks"></a>

Suppose your BLE hardware disconnects, and the MQTT over BLE service needs to detect the disconnection\. An application that you wrote might also need to detect the same disconnection event\. The BLE middleware can route the event to different parts of the code where you have registered callbacks, without making the higher layers compete for lower\-level resources\.

## Source and Header Files<a name="freertos-ble-source"></a>

The following tree diagram shows the required source and header files, along with their location in the Amazon FreeRTOSdirectory structure\. The project must also build the source files of the dependent libraries\.

```
Amazon FreeRTOS
  |
  + - lib
      + - bluetooth_low_energy
      |   + - aws_ble_event_manager.c 
      |   + - aws_ble_gap.c   [Middleware GAP]
      |   + - aws_ble_gatt.c  [Middleware GATT]
      |   + - portable      [Wrappers, wrapping APIs in lib/include/bluetooth_low_energy]
      |    + - services 
      |        + - device_information  [Service providing device info to the phone APP]
      |        |   + - aws_ble_device_information.c
      |        + - mqtt_ble                          [Used to do MQTT over BLE]
      |        |   + - aws_mqtt_proxy.c
      |        + - wifi_provisioning            [WIFI provisioning service over BLE]  
      |            + - aws_ble_wifi_provisioning.c     
      + - include
          + - bluetooth_low_energy [Wrapping APIs in lib/include/bluetooth_low_energy]
          |   + - bt_hal_avsrc_profile.h
          + ─ bt_hal_gatt_client.h
          + ─ bt_hal_gatt_server.h
          + ─ bt_hal_gatt_types.h
          + ─ bt_hal_manager_adapter_ble.h
          + ─ bt_hal_manager_adapter_classic.h
          + ─ bt_hal_manager.h
          + ─ bt_hal_manager_types.h       
          + - private                            [For internal library use only!]
          |   + - aws_ble_internals.h
          |   + - aws_ble_config_defaults.h
          |   + - aws_ble_event_manager.h
          + - aws_ble.h                 
          + - aws_ble_device_information.h
          + - aws_ble_services_init.h                 
          + - aws_ble_wifi_provisioning.h
```

## Amazon FreeRTOS BLE Library Configuration File<a name="freertos-ble-configuration"></a>

Applications that use the Amazon FreeRTOS MQTT over BLE service must provide an `aws_ble_config.h` header file, in which configuration parameters are defined\. Undefined configuration parameters take the default values specified in `lib\include\private\aws_ble_config_defaults.h`\.

## Optimization<a name="freertos-ble-optimization"></a>

When optimizing your board's performance, consider the following:
+ Low\-level APIs use less RAM, but offer limited functionality\.
+ You can set the `bleconfigMAX_NETWORK` parameter in the `aws_ble_config.h` header file to a lower value to reduce the amount of stack consumed\.
+ You can delete unused services to save RAM\.
+ You can increase the MTU size to its maximum value to limit message buffering, and make code run faster and consume less RAM\.

## Usage Restrictions<a name="freertos-ble-restrictions"></a>

By default, the Amazon FreeRTOS BLE library sets the `eBTpropertySecureConnectionOnly` property to TRUE, which places the device in a Secure Connections Only mode\. As specified by the [Bluetooth Core Specification](https://www.bluetooth.com/specifications/bluetooth-core-specification) v5\.0, Vol 3, Part C, 10\.2\.4, when a device is in a Secure Connections Only mode, the highest LE security mode 1 level, level 4, is required for access to any attribute that has permissions higher than the lowest LE security mode 1 level, level 1\. At the LE security mode 1 level 4, a device must have input and output capabilities for numeric comparison\.

To use a lower LE security level, set `eBTpropertySecureConnectionOnly` to FALSE, by calling the API `pxSetDeviceProperty` with the property `eBTpropertySecureConnectionOnly`\.

For information about LE security modes, see the [Bluetooth Core Specification](https://www.bluetooth.com/specifications/bluetooth-core-specification) v5\.0, Vol 3, Part C, 10\.2\.1\.

## Initialization<a name="freertos-ble-init"></a>

If your application interacts with the BLE stack through middleware, you only need to initialize the middleware\.

### Middleware<a name="freertos-ble-init-middle"></a>

 Middleware takes care of initializing the lower layers of the stack\.

**To initialize the middleware**

1. You must initialize any BLE hardware drivers before you call the BLE middleware API\.

1. Enable BLE\.

   ```
   const BTInterface_t * pxIface = BTGetBluetoothInterface();
   xStatus = pxIface->pxEnable( 0 );
   ```

1. 

****

   To initialize BLE, call `BLE_Init`, along with a set of desired properties, such as secure connection mode, device name, and MTU size\.

   ```
   xStatus = BLE_Init( &xServerUUID, xDeviceProperties, MAX_PROPERTIES );
   ```

### Low\-level APIs<a name="freertos-ble-init-low"></a>

If you don't want to use the Amazon FreeRTOS BLE GATT services, you can bypass the middleware and interact directly with the low\-level APIs to save resources\.

**To initialize the low\-level APIs**

1. 

****

   Driver initialization is not part of the BLE low\-level APIs\. You must initialize any BLE hardware drivers before you call the APIs\.

1. 

****

   The BLE low\-level API provides an enable/disable call to the BLE stack for optimizing power and resources\. Before calling the APIs, you must enable BLE\.

   ```
   const BTInterface_t * pxIface = BTGetBluetoothInterface();
   xStatus = pxIface->pxEnable( 0 );
   ```

1. 

****

   The Bluetooth manager contains APIs that are common to both BLE and Bluetooth classic\. The callbacks for the common manager must be initialized second\.

   ```
   xStatus = xBTInterface.pxBTInterface->pxBtManagerInit( &xBTManagerCb );
   ```

1. 

****

   The BLE adapter fits on top of the common API\. You must initialize its callbacks like you initialized the common API\.

   ```
   xBTInterface.pxBTLeAdapterInterface = ( BTBleAdapter_t * ) xBTInterface.pxBTInterface->pxGetLeAdapter();
   xStatus = xBTInterface.pxBTLeAdapterInterface->pxBleAdapterInit( &xBTBleAdapterCb );
   ```

1. 

****

   Register your new user application\.

   ```
   xBTInterface.pxBTLeAdapterInterface->pxRegisterBleApp( pxAppUuid );
   ```

1. 

****

   Initialize the callbacks to the GATT servers\.

   ```
   xBTInterface.pxGattServerInterface = ( BTGattServerInterface_t * ) xBTInterface.pxBTLeAdapterInterface->ppvGetGattServerInterface();
   xBTInterface.pxGattServerInterface->pxGattServerInit( &xBTGattServerCb );
   ```

   After you initialize the BLE adapter, you can add a GATT server\. You can register only one GATT server at a time\.

   ```
   xStatus = xBTInterface.pxGattServerInterface->pxRegisterServer( pxAppUuid );
   ```

1. 

****

   Set application properties like secure connection only and MTU size\.

   ```
   xStatus = xBTInterface.pxBTInterface->pxSetDeviceProperty( &pxProperty[ usIndex ] );
   ```

## API Reference<a name="freertos-ble-api"></a>

For a full API reference, see [Bluetooth Low Energy \(BLE\) API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/html4/bt__hal__gatt__client_8h.html)\.

## Example Usage<a name="freertos-ble-examples"></a>

### Advertising<a name="freertos-ble-advertising"></a>

1. Set advertisement parameters\.

   ```
   BLEAdvertismentParams_t xAdvParams =
   {
   	.bIncludeTxPower    = true,
   	.bIncludeName       = true,
   	.bSetScanRsp        = true,
   	.ulAppearance       =                   0,
   	.ulMinInterval      =                0x20,
   	.ulMaxInterval      =                0x40,
   	.usManufacturerLen  =                   0,
   	.pcManufacturerData = NULL,
   	.pxUUID1            = &xDeviceInfoSvcUUID,
   	.pxUUID2            = NULL
   };
   
   if( xStatus == eBTStatusSuccess )
   {
   	( void ) BLE_SetAdvData( BTAdvInd, &xAdvParams, vSetAdvCallback );
   }
   ```

1. Start advertisement\.

   ```
   void vSetAdvCallback ( BTStatus_t xStatus )
   {
       if( xStatus == eBTStatusSuccess )
       {
           ( void ) BLE_StartAdv( vStartAdvCallback );
       }
   }
   ```

### Adding a New Service<a name="freertos-ble-add-service"></a>

1. Allocate memory for new service\.

   ```
   xStatus = BLE_CreateService( &pxGattDemoService, gattDemoNUM_CHARS, gattDemoNUM_CHAR_DESCRS, xNumDescrsPerChar, gattDemoNUM_INCLUDED_SERVICES );
   ```

1. Create the service\.

   ```
   pxGattDemoService->xAttributeData.xUuid = xServiceUUID;
   
   pxGattDemoService->pxDescriptors[ egattDemoCharCounterCCFGDESCR ].xAttributeData.xUuid = xClientCharCfgUUID;
   pxGattDemoService->pxDescriptors[ egattDemoCharCounterCCFGDESCR ].xAttributeData.pucData = NULL;
   pxGattDemoService->pxDescriptors[ egattDemoCharCounterCCFGDESCR ].xAttributeData.xSize = 0;
   pxGattDemoService->pxDescriptors[ egattDemoCharCounterCCFGDESCR ].xPermissions = ( eBTPermReadEncryptedMitm | eBTPermWriteEncryptedMitm );
   pxGattDemoService->pxDescriptors[ egattDemoCharCounterCCFGDESCR ].pxAttributeEventCallback = vEnableNotification;
   
   xCharUUID.uu.uu16 = gattDemoCHAR_COUNTER_UUID;
   pxGattDemoService->pxCharacteristics[ egattDemoCharCounter ].xAttributeData.xUuid = xCharUUID;
   pxGattDemoService->pxCharacteristics[ egattDemoCharCounter ].xAttributeData.pucData = NULL;
   pxGattDemoService->pxCharacteristics[ egattDemoCharCounter ].xAttributeData.xSize = 0;
   pxGattDemoService->pxCharacteristics[ egattDemoCharCounter ].xPermissions = ( eBTPermRead );
   pxGattDemoService->pxCharacteristics[ egattDemoCharCounter ].xProperties = ( eBTPropRead | eBTPropNotify );
   pxGattDemoService->pxCharacteristics[ egattDemoCharCounter ].pxAttributeEventCallback = vReadCounter;
   pxGattDemoService->pxCharacteristics[ egattDemoCharCounter ].xNbDescriptors = 1;
   pxGattDemoService->pxCharacteristics[ egattDemoCharCounter ].pxDescriptors[ 0 ] = &pxGattDemoService->pxDescriptors[ egattDemoCharCounterCCFGDESCR ];
   
   xCharUUID.uu.uu16 = gattDemoCHAR_CONTROL_UUID;
   pxGattDemoService->pxCharacteristics[ egattDemoCharControl ].xAttributeData.xUuid = xCharUUID;
   pxGattDemoService->pxCharacteristics[ egattDemoCharControl ].xAttributeData.pucData = NULL;
   pxGattDemoService->pxCharacteristics[ egattDemoCharControl ].xAttributeData.xSize = 0;
   pxGattDemoService->pxCharacteristics[ egattDemoCharControl ].xPermissions = ( eBTPermReadEncryptedMitm | eBTPermWriteEncryptedMitm );
   pxGattDemoService->pxCharacteristics[ egattDemoCharControl ].xProperties = ( eBTPropRead | eBTPropWrite );
   pxGattDemoService->pxCharacteristics[ egattDemoCharControl ].pxAttributeEventCallback = vWriteCommand;
   pxGattDemoService->pxCharacteristics[ egattDemoCharControl ].xNbDescriptors = 0;
   pxGattDemoService->pxCharacteristics[ egattDemoCharControl ].pxDescriptors = NULL;
   
   pxGattDemoService->xServiceType = eBTServiceTypePrimary;
   pxGattDemoService->ucInstId = 0;
   
   xStatus = BLE_AddService( pxGattDemoService );
   ```

1. Start the service\.

   ```
   xStatus = BLE_StartService( pxGattDemoService, vServiceStartedCb );
   ```

1. Subscribe to any event required for the service\. In this example, we subscribe to a connection event\.

   ```
   xCallback.pxConnectionCb = vConnectionCallback;
   BLE_RegisterEventCb( eBLEConnection, xCallback );
   ```

For full Amazon FreeRTOS BLE demo applications, see [Bluetooth Low Energy Demo Applications](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html)\.

## Porting<a name="freertos-ble-porting"></a>

### User Input and Output Peripheral<a name="freertos-ble-porting-io"></a>

A secure connection requires both input and output for numeric comparison\. The `eBLENumericComparisonCallback` event can be registered using the event manager:

```
xEventCb.pxNumericComparisonCb = &prvNumericComparisonCb;
xStatus = BLE_RegisterEventCb( eBLENumericComparisonCallback, xEventCb );
```

The peripheral must display the numeric passkey and take the result of the comparison as an input\.

### Porting API Implementations<a name="freertos-ble-porting-apis"></a>

To port Amazon FreeRTOS to a new target, you must implement some APIs for the Wi\-Fi Provisioning service and BLE functionality\.

#### Wi\-Fi Provisioning APIs<a name="freertos-ble-porting-wifi"></a>

To use the Wi\-Fi provisioning service you must implement the following APIs:
+ `WIFI_NetworkGet`
+ `WIFI_NetworkDelete`
+ `WIFI_NetworkAdd`

#### BLE APIs<a name="freertos-ble-porting-ble"></a>

To use the Amazon FreeRTOS BLE middleware, you must implement some APIs\.

##### APIs Common Between GAP for Bluetooth Classic and GAP for BLE<a name="gap-common-apis"></a>
+ `pxBtManagerInit`
+ `pxEnable`
+ `pxDisable`
+ `pxGetDeviceProperty`
+ `pxSetDeviceProperty` \(All options are mandatory expect `eBTpropertyRemoteRssi` and `eBTpropertyRemoteVersionInfo`\)
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

##### APIs Specific to GAP for BLE<a name="gap-common-apis"></a>
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

##### GATT Server<a name="gap-common-apis"></a>
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