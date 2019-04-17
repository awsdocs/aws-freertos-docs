# Installing a Terminal Emulator<a name="uart-term"></a>

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

## Finding Your Board's Serial Port<a name="serial-port-ts"></a>

If you do not know your board's serial port, you can issue one of the following commands from the command line or terminal to return the serial ports for all devices connected to your host computer:

Windows  

```
chgport
```

Linux  

```
ls /dev/tty*
```

macOS  

```
ls /dev/cu.*
```