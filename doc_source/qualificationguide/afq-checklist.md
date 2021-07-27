# FreeRTOS qualification checklist<a name="afq-checklist"></a>

Use the following checklists to keep track of qualification items\.

You must pass each of these items in order to be listed in the [AWS Partner Device Catalog](https://devices.amazonaws.com/)\.
+ Review the steps you must follow for Porting FreeRTOS to your device\. These steps are summarized in the [FreeRTOS porting flowchart](https://docs.aws.amazon.com/freertos/latest/portingguide/porting-chart.html)\. For more information, see the [FreeRTOS Porting Guide](https://docs.aws.amazon.com/freertos/latest/portingguide/index.html)\. 
  + You must port a FreeRTOS qualified kernel architecture and can't make modifications to it on your own\. For more information, see [ Configuring a FreeRTOS kernel port](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-kernel.html) in the *FreeRTOS Porting Guide*\.
+ Validate your FreeRTOS port with AWS IoT Device Tester\.
  + A successful IDT log \(with all test groups passing on one log\) is required in your Device Qualification Portal \(DQP\) submission\.
  + All qualification submissions must be made through the Device Listing Portal on APN Partner Central\.
+ Create a Hello World demo\.
  + See the [Setting up a hello world demo](afq-hw-demo.md)\.
+ Create a *Getting Started Guide* \(GSG\) for your device 
  + See the [Creating a getting started with FreeRTOS guide for your device](afq-gsg.md)\.
+ Create an appropriate [open source license](https://opensource.org/licenses) text file and place it with your code\. 
  + FreeRTOS is distributed under the [MIT license](https://opensource.org/licenses/MIT)\. 
+ Provide an accessible location to download your code\.
  + We recommend that you use a GitHub repository, but don't use a personal GitHub repository\. Use an official company GitHub repository\. 
+ Mitigate the following threat in regard to the random number generator \(RNG\):
  + To mitigate the risk of network spoofing and man\-in\-the\-middle attacks that can result in unauthorized data disclosure, a true hardware random number generator \(TRNG\) is required for FreeRTOS qualification\. The TRNG is recommended by the FreeRTOS libraries that implement protocols such as DHCP, DNS, TCP/IP, and TLS\. Consistent with the guidance published by NIST, the TRNG on your board is used by FreeRTOS as the entropy source for a standard implementation CTR\_DRBG\. For more information, see page 50 in the[ NIST SP 800\-90A](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-90Ar1.pdf)\. 

    Per the [NIST SP 800\-90B](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-90B.pdf) description, a TRNG is a "physical noise source" \(section 2\.2\.1\) that produces "independent and identically distributed" \(IID\) samples \(section 5\), for example, a ring oscillator\. 
  + To control BOM costs and for some customer use cases, certain boards will not have a dedicated TRNG\. If you are qualifying these boards, add the following advisory notice in the header of the file `core_pkcs11.h` that you have ported for the [ core\_pkcs11\_pal\.h](https://github.com/FreeRTOS/corePKCS11/blob/main/source/include/core_pkcs11_pal.h) API\. View our changelog for examples of boards that are similar\. 
**Note**  
For best security practice, we recommend that you use a random number generation solution that is truly randomized and conforms to the guidelines provided in the [FreeRTOS qualification checklist](https://docs.aws.amazon.com/freertos/latest/qualificationguide/afq-checklist.html)\. The random number generator method presented in this file by the silicon vendor is not truly random in nature\. Contact the silicon vendor for details regarding the method implemented\.
+ If you're qualifying for OTA, verify that you mitigate the risks defined in the OTA Threat Model described in [Porting the OTA library](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-ota.html) in the *FreeRTOS Porting Guide*\.

To be designated as supporting the long\-term support \(LTS\) version of FreeRTOS in the [AWS Partner Device Catalog](https://devices.amazonaws.com/), you must provide a manifest file\. This isn't required for standard qualification\. You must use a LTS version of FreeRTOS and include a `manifest.yml` file in the root directory\. The requirements for the manifest file are described in [FreeRTOS manifest file instructions](afq-checklist-manifest-instr.md) and a template is provided as a [Example manifest\.yml](afq-checklist-manifest-example.md)\. The FreeRTOS repository includes a compatible manifest file by default\.

To be listed in the Online Configuration Wizard, contact your APN representative and provide the following items:
+ Create a CMake list file, and build the test and demo applications with this file\. 
  + For instructions, see [Creating a CMakeLists\.txt file for your platform](afq-cmake.md)\.
**Note**  
A CMake list file isn't required to qualify a board through the AWS Device Qualification Program\. The file is only required for listing devices on the FreeRTOS console\. 
+ Provide the following hardware information for your device:
  + The compiler options for optimizations\.
  + The Supported IDE, with latest supported version number\. 
  + The CLI command to build target executables\. 
  + The CLI command to flash the target\. 