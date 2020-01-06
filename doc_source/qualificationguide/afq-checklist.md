# Amazon FreeRTOS Qualification Checklist<a name="afq-checklist"></a>

Use following checklists to help you keep track of qualification items\.

You must pass each of these items in order to be listed in the [AWS Partner Device Catalog](https://devices.amazonaws.com/)\.
+ Review the steps you must follow for Porting Amazon FreeRTOS to your device; these are summarized in the [Amazon FreeRTOS Porting Flowchart](https://docs.aws.amazon.com/freertos/latest/portingguide/porting-chart.html)\. See the [Amazon FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/index.html) for additional details\. 
  + You must port an Amazon FreeRTOS qualified kernel architecture and cannot make modifications to it on your own\. See [Configuring a Amazon FreeRTOS Kernel Port](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-kernel.html) in the *Amazon FreeRTOS Porting Guide* for more information\.
+ Validate your Amazon FreeRTOS port with AWS IoT Device Tester\.
  + A complete successful IDT log \(with all Test Groups passing on ONE log\) is required in your Device Qualification Portal \(DQP\) submission\.
  + All qualification submissions must be made through the Device Listing Portal on APN Partner Central\.
+ Create a Hello World demo
  + Please refer to [Setting Up a Hello World Demo](afq-hw-demo.md)\.
+ Create a Getting Started Guide \(GSG\) for your device 
  + Please refer to [Creating a Getting Started with Amazon FreeRTOS Guide for Your Device](afq-gsg.md)\.
+ Create an appropriate [open source license](https://opensource.org/licenses) text file and place it with your code\. 
  + Amazon FreeRTOS is distributed under the [MIT license](https://opensource.org/licenses/MIT)\. 
+ Provide an accessible location to download your code\.
  + We recommend you use a github repo, but please do not use a personal GitHub repository\. Use an official company GitHub repo\. 
+ Mitigate the following threat in regards to the random number generator \(RNG\)
  + In order to mitigate the risk of network spoofing and man\-in\-the\-middle attacks that can result in unauthorized data disclosure, a true hardware random number generator \(TRNG\) is required for Amazon FreeRTOS qualification\. The TRNG is recommended by the Amazon FreeRTOS libraries that implement protocols such as DHCP, DNS, TCP/IP, and TLS\. Consistent with the guidance published by NIST, the TRNG on your board is used by Amazon FreeRTOS as the entropy source for a standard implementation CTR\_DRBG \(as described in [ NIST SP 800\-90A](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-90Ar1.pdf) \- Page50\)\. 

    Per the [NIST SP 800\-90B](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-90B.pdf) description, a TRNG is a "physical noise source" \(section 2\.2\.1\) that produces "independent and identically distributed" \(IID\) samples \(section 5\), for example, a ring oscillator\. 
  + To control BOM costs and for some customer use cases, certain boards will not have a dedicated TRNG\. If you are qualifying these boards, please add the following advisory notice in the header of the file [ `iot_pkcs11.h`](https://github.com/aws/amazon-freertos/blob/master/libraries/freertos_plus/standard/pkcs11/include/iot_pkcs11.h) that you have ported\. Please view our changelog for examples of boards that are similar in this regard\. 

    **Notice**: *For best security practice, it is recommended to utilize a random number generation solution that is truly randomized and conforms to the guidelines provided in the [ Amazon FreeRTOS Qualification Checklist](https://docs.aws.amazon.com/freertos/latest/qualificationguide/afq-checklist.html)\. The random number generator method presented in this file by the silicon vendor is not truly random in nature\. Please contact the silicon vendor for details regarding the method implemented\.*
+ If you are qualifying for OTA, verify that you mitigate the risks defined in the OTA Threat Model described in [ Porting the OTA Library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ota.html) in the *Amazon FreeRTOS Porting Guide*\.

To be listed in the Online Configuration Wizard, please contact your APN representative and provide the following items\.
+ Create a CMake list file, and build the test and demo applications with this file\. 
  + For instructions, see [Creating a CMakeLists\.txt File for Your Platform](afq-cmake.md)\.
**Note**  
A CMake list file is not required to qualify a board through the AWS Device Qualification Program\. The file is only required for listing devices on the Amazon FreeRTOS Console\. 
+ Provide the following hardware information for your device\.
  + The compiler options for optimizations\.
  + The Supported IDE, with latest supported version number\. 
  + The CLI command to build target executables\. 
  + The CLI command to flash the target\. 