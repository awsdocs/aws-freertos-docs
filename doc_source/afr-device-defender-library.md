# Amazon FreeRTOS Device Defender Library<a name="afr-device-defender-library"></a>

AWS IoT Device Defender is an AWS IoT service that allows you to audit the configuration of your devices, monitor connected devices to detect abnormal behavior, and to mitigate security risks\. It gives you the ability to enforce consistent IoT configurations across your AWS IoT device fleet and respond quickly when devices are compromised\.

Amazon FreeRTOS provides a library that allows your Amazon FreeRTOS\-based devices to work with AWS IoT Device Defender\. You can download the Amazon FreeRTOS Device Defender library using the [Amazon FreeRTOS Console](http://console.aws.amazon.com/freertos) by adding the Device Defender library to your software configuration\. You can also clone the Amazon FreeRTOS GitHub repository and find the library in the `lib` directory\.

## Amazon FreeRTOS Device Defender API<a name="afr-device-defender-api"></a>

This section contains information about the Device Defender API\.

### DEFENDER\_MetricsInit<a name="dd_metrics_init"></a>

Specifies the Device Defender metrics your device will send to AWS IoT Device Defender\.

```
DefenderErr_t DEFENDER_MetricsInit(DefenderMetric_t * pxMetricsList);
```Arguments

`metrics_list`  
A list of Device Defender metrics\. Valid values are:  
+ `DEFENDER_tcp_connections` \- tracks the number of TCP connections\.Return Value

  
Returns one of the `DefenderErr_t` enums\. For more information, see [Amazon FreeRTOS Device Defender API Error Codes](#afr-device-defender-error-codes)\.

### DEFENDER\_ReportPeriodSet<a name="dd_reportperiodset"></a>

Sets the report period interval in seconds\. Device Defender provides metric reports on an interval\. If the device is awake, and the interval has elapsed, the device reports the metrics\.

```
DefenderErr_t DEFENDER_ReportPeriodSet(int32_t LPeriodSec);
```Arguments

`period_sec`  
The number of seconds after which a report is sent to AWS IoT Device Defender\.Return Value

  
Returns one of the `DefenderErr_t` enums\. For more information, see [Amazon FreeRTOS Device Defender API Error Codes](#afr-device-defender-error-codes)\.

### DEFENDER\_Start<a name="dd_start"></a>

Starts the Device Defender agent\.

```
 DefenderErr_t DEFENDER_Start(void);
```Return Value

  
Returns one of the `DefenderErr_t` enums\. For more information, see [Amazon FreeRTOS Device Defender API Error Codes](#afr-device-defender-error-codes)\.

### DEFENDER\_Stop<a name="dd_start"></a>

Stops the Device Defender agent\.

```
DefenderErr_t DEFENDER_Stop(void);
```Return Value

  
Returns one of the `DefenderErr_t` enums\. For more information, see [Amazon FreeRTOS Device Defender API Error Codes](#afr-device-defender-error-codes)\.

### DEFENDER\_ReportStatusGet<a name="dd_reportstatusget"></a>

Gets the status of the last Device Defender report\. Valid status code values are:

`eDefenderRepSuccess`  
The last report was successfully sent and acknowledged\.

`eDefenderRepInit`  
Device Defender has been started, but no report has been sent\.

`eDefenderRepRejected`  
The last report was rejected\.

`eDefenderRepNoAck`  
The last report was not acknowledged\.

`eDefenderRepNotSent`  
The last report was not sent, likely due to a connectivity issue\.

```
DefenderReportStatus_t DEFENDER_ReportStatusGet(void);
```

### Amazon FreeRTOS Device Defender API Error Codes<a name="afr-device-defender-error-codes"></a>

`eDefenderErrSuccess`  
The operation was successful\.

`eDefenderErrFailedToCreateTask`  
The operation could not be started\.

`eDefenderErrAlreadyStarted`  
The operation is already in progress\.

`eDefenderErrNotStarted`  
The Device Defender agent has not been started\.

`eDefenderErrOther`  
An unspecified error occurred\.

### Using Device Defender in Your Embedded Application<a name="calling_dd"></a>

The following code shows how to configure and start the Device Defender agent from your embedded application:

```
void MyDefenderInit(void)
{
	// Specify metrics to send to Device Defender
    defender_metric_t metrics_list[] = {
        DEFENDER_tcp_connections
    };
    ( void ) DEFENDER_MetricsInit( metrics_list );
 
 	// Set the reporting interval
 	// You can use a shorter period to trigger the violation faster, however 
 	// the Device Defender service is not guaranteed to accept reports faster 
 	// than every 300 seconds (5 minutes) per device.
    int report_period_sec = 300;
    ( void ) DEFENDER_ReportPeriodSet( report_period_sec );
 
 	// Start the Device Defender agent
    DEFENDER_Start();
}
```