# Bluetooth Low Energy Library<a name="freertos-ble-library"></a>

## Overview<a name="freertos-ble-overview"></a>

FreeRTOS supports publishing and subscribing to MQTT topics over Bluetooth Low Energy through a proxy device, such as a mobile phone\. With the FreeRTOS Bluetooth Low Energy library, your microcontroller can securely communicate with the AWS IoT MQTT broker\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/blediagram.png)

Using the Mobile SDKs for FreeRTOS Bluetooth Devices, you can write native mobile applications that communicate with the embedded applications on your microcontroller over Bluetooth Low Energy\. For more information about the mobile SDKs, see [Mobile SDKs for FreeRTOS Bluetooth Devices](freertos-ble-mobile.md)\. 

The FreeRTOS Bluetooth Low Energy library includes services for configuring Wi\-Fi networks, transferring large amounts of data, and providing network abstractions over Bluetooth Low Energy\. The FreeRTOS Bluetooth Low Energy library also includes middleware and lower\-level APIs for more direct control over your Bluetooth Low Energy stack\.

## Architecture<a name="freertos-ble-arch"></a>

Three layers make up the FreeRTOS Bluetooth Low Energy library: services, middleware, and low\-level wrappers\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-architecture.png)

### Services<a name="freertos-ble-services"></a>

The FreeRTOS Bluetooth Low Energy services layer consists of four Generic Attributes \(GATT\) services that leverage the middleware APIs: Device Information, Wi\-Fi Provisioning, Network Abstraction, and Large Object Transfer\.

#### Device Information<a name="ble-device-information"></a>

The Device Information service gathers information about your microcontroller, including:
+ The version of FreeRTOS that your device is using\.
+ The AWS IoT endpoint of the account for which the device is registered\.
+ Bluetooth Low Energy Maximum Transmission Unit \(MTU\)\.

#### Wi\-Fi Provisioning<a name="ble-wifi-provisioning"></a>

The Wi\-Fi Provisioning service enables microcontrollers with Wi\-Fi capabilities to do the following:
+ List networks in range\.
+ Save networks and network credentials to flash memory\.
+ Set network priority\.
+ Delete networks and network credentials from flash memory\.

#### Network Abstraction<a name="ble-network-abstraction"></a>

The network abstraction service abstracts the network connection type for applications\. A common API interacts with your device's Wi\-Fi, Ethernet, and Bluetooth Low Energy hardware stack, enabling an application to be compatible with multiple connection types\.

#### Large Object Transfer<a name="ble-object-transfer"></a>

The Large Object Transfer service sends data to and receives data from a client\. Other services, like Wi\-Fi Provisioning and Network Abstraction, use the Large Object Transfer service to send and receive data\. You can also use the Large Object Transfer API to interact with the service directly\.

### Middleware<a name="freertos-ble-middleware"></a>

FreeRTOS Bluetooth Low Energy middleware is an abstraction from the lower\-level APIs\. The middleware APIs make up a more user\-friendly interface to the Bluetooth Low Energy stack\.

Using middleware APIs, you can register several callbacks, across multiple layers, to a single event\. Initializing the Bluetooth Low Energy middleware also initializes services and starts advertising\.

#### Flexible Callback Subscription<a name="freertos-ble-flexible-callbacks"></a>

Suppose your Bluetooth Low Energy hardware disconnects, and the MQTT over Bluetooth Low Energy service needs to detect the disconnection\. An application that you wrote might also need to detect the same disconnection event\. The Bluetooth Low Energy middleware can route the event to different parts of the code where you have registered callbacks, without making the higher layers compete for lower\-level resources\.

### Low\-level Wrappers<a name="freertos-ble-arch-lowlevel"></a>

The low\-level FreeRTOS Bluetooth Low Energy wrappers are an abstraction from the manufacturer's Bluetooth Low Energy stack\. Low\-level wrappers offer a common set of APIs for direct control over the hardware\. The low\-level APIs optimize RAM usage, but are limited in functionality\.

Use the Bluetooth Low Energy service APIs to interact with the Bluetooth Low Energy services\. The service APIs demand more resources than the low\-level APIs\.

## Dependencies and Requirements<a name="freertos-ble-dependencies"></a>

The Bluetooth Low Energy library has the following direct dependencies:
+ [Linear Containers Library](lib-linear.md)
+ A platform layer that interfaces with the operating system for thread management, timers, clock functions, and network access\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/ble-dependencies.png)

Only the Wi\-Fi Provisioning service has FreeRTOS library dependencies:


****  

| GATT Service | Dependency | 
| --- | --- | 
| Wi\-Fi Provisioning | [Wi\-Fi Library](freertos-wifi.md) | 

To communicate with the AWS IoT MQTT broker, you must have an AWS account and you must register your devices as AWS IoT things\. For more information about setting up, see the [AWS IoT Developer Guide](https://docs.aws.amazon.com/iot/latest/developerguide/)\.

FreeRTOS Bluetooth Low Energy uses Amazon Cognito for user authentication on your mobile device\. To use MQTT proxy services, you must create an Amazon Cognito identity and user pools\. Each Amazon Cognito Identity must have the appropriate policy attached to it\. For more information, see the [Amazon Cognito Developer Guide](https://docs.aws.amazon.com/cognito/latest/developerguide/)\.

## Library Configuration File<a name="freertos-ble-configuration"></a>

Applications that use the FreeRTOS MQTT over Bluetooth Low Energy service must provide an `iot_ble_config.h` header file, in which configuration parameters are defined\. Undefined configuration parameters take the default values specified in `iot_ble_config_defaults.h`\.

Some important configuration parameters include:

`IOT_BLE_ADD_CUSTOM_SERVICES`  
Allows users to create their own services\.

`IOT_BLE_SET_CUSTOM_ADVERTISEMENT_MSG`  
Allows users to customize the advertisement and scan response messages\.

For more information, see [Bluetooth Low Energy API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/ble/index.html)\. 

## Optimization<a name="freertos-ble-optimization"></a>

When optimizing your board's performance, consider the following:
+ Low\-level APIs use less RAM, but offer limited functionality\.
+ You can set the `bleconfigMAX_NETWORK` parameter in the `iot_ble_config.h` header file to a lower value to reduce the amount of stack consumed\.
+ You can increase the MTU size to its maximum value to limit message buffering, and make code run faster and consume less RAM\.

## Usage Restrictions<a name="freertos-ble-restrictions"></a>

By default, the FreeRTOS Bluetooth Low Energy library sets the `eBTpropertySecureConnectionOnly` property to TRUE, which places the device in a Secure Connections Only mode\. As specified by the [Bluetooth Core Specification](https://www.bluetooth.com/specifications/bluetooth-core-specification) v5\.0, Vol 3, Part C, 10\.2\.4, when a device is in a Secure Connections Only mode, the highest LE security mode 1 level, level 4, is required for access to any attribute that has permissions higher than the lowest LE security mode 1 level, level 1\. At the LE security mode 1 level 4, a device must have input and output capabilities for numeric comparison\.

Here are the supported modes, and their associated properties:

Mode 1, Level 1 \(No security\)  

```
/* Disable numeric comparison */
#define IOT_BLE_ENABLE_NUMERIC_COMPARISON        ( 0 )
#define IOT_BLE_ENABLE_SECURE_CONNECTION         ( 0 )
#define IOT_BLE_INPUT_OUTPUT                     ( eBTIONone )
#define IOT_BLE_ENCRYPTION_REQUIRED               ( 0 )
```

Mode 1, Level 2 \(Unauthenticated pairing with encryption\)  

```
#define IOT_BLE_ENABLE_NUMERIC_COMPARISON        ( 0 )
#define IOT_BLE_ENABLE_SECURE_CONNECTION         ( 0 )
#define IOT_BLE_INPUT_OUTPUT                     ( eBTIONone )
```

Mode 1, Level 3 \(Authenticated pairing with encryption\)  
This mode is not supported\.

Mode 1, Level 4 \(Authenticated LE Secure Connections pairing with encryption\)  
This mode is supported by default\.

For information about LE security modes, see the [Bluetooth Core Specification](https://www.bluetooth.com/specifications/bluetooth-core-specification) v5\.0, Vol 3, Part C, 10\.2\.1\.

## Initialization<a name="freertos-ble-init"></a>

If your application interacts with the Bluetooth Low Energy stack through middleware, you only need to initialize the middleware\. Middleware takes care of initializing the lower layers of the stack\.

### Middleware<a name="freertos-ble-init-middle"></a>

**To initialize the middleware**

1. Initialize any Bluetooth Low Energy hardware drivers before you call the Bluetooth Low Energy middleware API\.

1. Enable Bluetooth Low Energy\.

1. Initialize the middleware with `IotBLE_Init()`\.
**Note**  
This initialization step is not required if you are running the AWS demos\. Demo initialization is handled by the network manager, located at `<freertos>/demos/network_manager`\.

### Low\-level APIs<a name="freertos-ble-init-low"></a>

If you don't want to use the FreeRTOS Bluetooth Low Energy GATT services, you can bypass the middleware and interact directly with the low\-level APIs to save resources\.

**To initialize the low\-level APIs**

1. 

****

   Initialize any Bluetooth Low Energy hardware drivers before you call the APIs\. Driver initialization is not part of the Bluetooth Low Energy low\-level APIs\.

1. 

****

   The Bluetooth Low Energy low\-level API provides an enable/disable call to the Bluetooth Low Energy stack for optimizing power and resources\. Before calling the APIs, you must enable Bluetooth Low Energy\.

   ```
   const BTInterface_t * pxIface = BTGetBluetoothInterface();
   xStatus = pxIface->pxEnable( 0 );
   ```

1. 

****

   The Bluetooth manager contains APIs that are common to both Bluetooth Low Energy and Bluetooth classic\. The callbacks for the common manager must be initialized second\.

   ```
   xStatus = xBTInterface.pxBTInterface->pxBtManagerInit( &xBTManagerCb );
   ```

1. 

****

   The Bluetooth Low Energy adapter fits on top of the common API\. You must initialize its callbacks like you initialized the common API\.

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

   After you initialize the Bluetooth Low Energy adapter, you can add a GATT server\. You can register only one GATT server at a time\.

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

For a full API reference, see [Bluetooth Low Energy API Reference](https://docs.aws.amazon.com/freertos/latest/lib-ref/ble/index.html)\. 

## Example Usage<a name="freertos-ble-examples"></a>

The examples below demonstrate how to use the Bluetooth Low Energy library for advertising and creating new services\. For full FreeRTOS Bluetooth Low Energy demo applications, see [Bluetooth Low Energy Demo Applications](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html)\.

### Advertising<a name="freertos-ble-advertising"></a>

1. In your application, set the advertising UUID:

   ```
   static const BTUuid_t _advUUID =
   {
       .uu.uu128 = IOT_BLE_ADVERTISING_UUID,
       .ucType   = eBTuuidType128
   };
   ```

1. Then define the `IotBle_SetCustomAdvCb` callback function:

   ```
   void IotBle_SetCustomAdvCb( IotBleAdvertisementParams_t * pAdvParams,  IotBleAdvertisementParams_t * pScanParams)
   {
       memset(pAdvParams, 0, sizeof(IotBleAdvertisementParams_t));
       memset(pScanParams, 0, sizeof(IotBleAdvertisementParams_t));
   
       /* Set advertisement message */
       pAdvParams->pUUID1 = &_advUUID;
       pAdvParams->nameType = BTGattAdvNameNone;
   
       /* This is the scan response, set it back to true. */
       pScanParams->setScanRsp = true;
       pScanParams->nameType = BTGattAdvNameComplete;
   }
   ```

   This callback sends the UUID in the advertisement message and the full name in the scan response\.

1. Open `vendors/<vendor>/boards/<board>/aws_demos/config_files/iot_ble_config.h`, and set `IOT_BLE_SET_CUSTOM_ADVERTISEMENT_MSG` to `1`\. This triggers the `IotBle_SetCustomAdvCb` callback\.

### Adding a New Service<a name="freertos-ble-add-service"></a>

For full examples of services, see `<freertos>/.../ble/services`\.

1. Create UUIDs for the service's characteristic and descriptors:

   ```
   #define xServiceUUID_TYPE \
   {\
       .uu.uu128 = gattDemoSVC_UUID, \
       .ucType   = eBTuuidType128 \
   }
   #define xCharCounterUUID_TYPE \
   {\
       .uu.uu128 = gattDemoCHAR_COUNTER_UUID,\
       .ucType   = eBTuuidType128\
   }
   #define xCharControlUUID_TYPE \
   {\
       .uu.uu128 = gattDemoCHAR_CONTROL_UUID,\
       .ucType   = eBTuuidType128\
   }
   #define xClientCharCfgUUID_TYPE \
   {\
       .uu.uu16 = gattDemoCLIENT_CHAR_CFG_UUID,\
       .ucType  = eBTuuidType16\
   }
   ```

1. Create a buffer to register the handles of the characteristic and descriptors:

   ```
   static uint16_t usHandlesBuffer[egattDemoNbAttributes];
   ```

1. Create the attribute table\. To save some RAM, define the table as a `const`\.
**Important**  
Always create the attributes in order, with the service as the first attribute\.

   ```
   static const BTAttribute_t pxAttributeTable[] = {
        {    
            .xServiceUUID =  xServiceUUID_TYPE
        },
       {
            .xAttributeType = eBTDbCharacteristic,
            .xCharacteristic = 
            {
                 .xUuid = xCharCounterUUID_TYPE,
                 .xPermissions = ( IOT_BLE_CHAR_READ_PERM ),
                 .xProperties = ( eBTPropRead | eBTPropNotify )
             }
        },
        {
            .xAttributeType = eBTDbDescriptor,
            .xCharacteristicDescr =
            {
                .xUuid = xClientCharCfgUUID_TYPE,
                .xPermissions = ( IOT_BLE_CHAR_READ_PERM | IOT_BLE_CHAR_WRITE_PERM )
             }
        },
       {
            .xAttributeType = eBTDbCharacteristic,
            .xCharacteristic = 
            {
                 .xUuid = xCharControlUUID_TYPE,
                 .xPermissions = ( IOT_BLE_CHAR_READ_PERM | IOT_BLE_CHAR_WRITE_PERM  ),
                 .xProperties = ( eBTPropRead | eBTPropWrite )
             }
        }
   };
   ```

1. Create an array of callbacks\. This array of callbacks must follow the same order as the table array defined above\.

   For example, if `vReadCounter` gets triggered when `xCharCounterUUID_TYPE` is accessed, and `vWriteCommand` gets triggered when `xCharControlUUID_TYPE` is accessed, define the array as follows:

   ```
   static const IotBleAttributeEventCallback_t pxCallBackArray[egattDemoNbAttributes] =
       {
     NULL,
     vReadCounter,
     vEnableNotification,
     vWriteCommand
   };
   ```

1. Create the service:

   ```
   static const BTService_t xGattDemoService = 
   {
     .xNumberOfAttributes = egattDemoNbAttributes,
     .ucInstId = 0,
     .xType = eBTServiceTypePrimary,
     .pusHandlesBuffer = usHandlesBuffer,
     .pxBLEAttributes = (BTAttribute_t *)pxAttributeTable
   };
   ```

1. Call the API `IotBle_CreateService` with the structure that you created in the previous step\. The middleware synchronizes the creation of all services, so any new services need to already be defined when the `IotBle_AddCustomServicesCb` callback is triggered\.

   1. Set `IOT_BLE_ADD_CUSTOM_SERVICES` to `1` in `vendors/<vendor>/boards/<board>/aws_demos/config_files/iot_ble_config.h`\.

   1. Create IotBle\_AddCustomServicesCb in your application:

      ```
      void IotBle_AddCustomServicesCb(void)
      {
          BTStatus_t xStatus;
          /* Select the handle buffer. */
          xStatus = IotBle_CreateService( (BTService_t *)&xGattDemoService, (IotBleAttributeEventCallback_t *)pxCallBackArray );
      }
      ```

## Porting<a name="freertos-ble-porting"></a>

### User Input and Output Peripheral<a name="freertos-ble-porting-io"></a>

A secure connection requires both input and output for numeric comparison\. The `eBLENumericComparisonCallback` event can be registered using the event manager:

```
xEventCb.pxNumericComparisonCb = &prvNumericComparisonCb;
xStatus = BLE_RegisterEventCb( eBLENumericComparisonCallback, xEventCb );
```

The peripheral must display the numeric passkey and take the result of the comparison as an input\.

### Porting API Implementations<a name="freertos-ble-porting-apis"></a>

To port FreeRTOS to a new target, you must implement some APIs for the Wi\-Fi Provisioning service and Bluetooth Low Energy functionality\.

#### Bluetooth Low Energy APIs<a name="freertos-ble-porting-ble"></a>

To use the FreeRTOS Bluetooth Low Energy middleware, you must implement some APIs\.

##### APIs Common Between GAP for Bluetooth Classic and GAP for Bluetooth Low Energy<a name="gap-common-apis"></a>
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

##### APIs Specific to GAP for Bluetooth Low Energy<a name="gap-common-apis-ble"></a>
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

##### GATT Server<a name="gap-common-apis-gatt"></a>
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

For more information about porting the FreeRTOS Bluetooth Low Energy library to your platform, see [Porting the Bluetooth Low Energy Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ble.html) in the FreeRTOS Porting Guide\.