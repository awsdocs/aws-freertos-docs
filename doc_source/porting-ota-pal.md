# OTA Portable Abstraction Layer<a name="porting-ota-pal"></a>

Amazon FreeRTOS defines an OTA portable abstraction layer \(PAL\) in order to ensure that the OTA library is useful on a wide variety of hardware\. The OTA PAL interface is listed below\.

`prvAbort`  
Aborts an OTA update\.

`prvCreateFileForRx`  
Creates a new file to store the data chunks as they are received\.

`prvCloseFile`  
Closes the specified file\. This may authenticate the file if it is marked as secure\.

`prvCheckFileSignature`  
Verifies the signature of the specified file\. For device file systems with built\-in signature verification enforcement, this may be redundant and should therefore be implemented as a no\-op\.

`prvWriteBlock`  
Writes a block of data to the specified file at the given offset\. Returns the number of bytes written on success or negative error code\.

`prvActivateNewImage`  
Activates the new firmware image\. For some ports, this function may not return\.

`prvSetImageState`  
Does whatever is required by the platform to accept or reject the last firmware image \(or bundle\)\. Refer to the platform implementation to determine what happens on your platform\. 

`prvReadAndAssumeCertificate`  
Reads the specified signer certificate from the file system and returns it to the caller\. This is optional on some platforms\.