# Troubleshooting getting started<a name="gsg-troubleshooting"></a>

The following topics can help you troubleshoot issues that you encounter while getting started with FreeRTOS:

**Topics**
+ [General getting started troubleshooting tips](#gsg-troubleshooting-general)
+ [Installing a terminal emulator](#uart-term)

For board\-specific troubleshooting, see the [Getting Started with FreeRTOS](freertos-getting-started.md) guide for your board\.

## General getting started troubleshooting tips<a name="gsg-troubleshooting-general"></a>

**No messages appear in the AWS IoT console after I run the Hello World demo project\. What do I do?**  
Try the following:  

1. Open a terminal window to view the logging output of the sample\. This can help you determine what is going wrong\.

1. Check that your network credentials are valid\.

**The logs shown in my terminal when running a demo are truncated\. How can I increase their length?**  
Increase the value of `configLOGGING_MAX_MESSAGE_LENGTH` to 255 in the `FreeRTOSconfig.h` file for the demo you are running:  

```
#define configLOGGING_MAX_MESSAGE_LENGTH    255
```

## Installing a terminal emulator<a name="uart-term"></a>

A terminal emulator can help you diagnose problems or verify that your device code is running properly\. There are a variety of terminal emulators available for Windows, macOS, and Linux\.

You must connect your board to your computer before you attempt to establish a serial connection to your board with a terminal emulator\.

Use the following settings to configure your terminal emulator:


| Terminal Setting | Value | 
| --- | --- | 
|  BAUD rate  |  115200  | 
|  Data  |  8 bit  | 
|  Parity  |  none  | 
|  Stop  |  1 bit  | 
|  Flow control  |  none  | 

### Finding your board's serial port<a name="serial-port-ts"></a>

If you do not know your board's serial port, you can issue one of the following commands from the command line or terminal to return the serial ports for all devices connected to your host computer:

**Windows**  

```
chgport
```

**Linux**  

```
ls /dev/tty*
```

**macOS**  

```
ls /dev/cu.*
```