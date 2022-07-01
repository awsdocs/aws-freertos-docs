# Qualification FAQs<a name="afr-qualification-faqs"></a>

Q: *Can I qualify a board that contains a microcontroller \(MCU\) without built\-in cloud connectivity?*  
Yes\. However, the board that uses the MCU should have direct or indirect cloud connectivity \(for example \- using a separate communications module\)\. 

Q: *Which FreeRTOS versions are eligible for qualification?*  
Use either the latest FreeRTOS Long Term Support \(LTS\) version \(recommended, [GitHub](https://github.com/FreeRTOS/FreeRTOS-LTS)\) or the latest officially released FreeRTOS libraries \([GitHub](https://github.com/FreeRTOS/FreeRTOS)\) version\.

Q: *What are the mandatory tests for qualification?*  
The [Porting flowchart](https://docs.aws.amazon.com/freertos/latest/portingguide/porting-chart.html) describes the software libraries and tests required to qualify your board\.

Q: *Can I mix and match library versions for qualification? For example, use coreMQTT from the LTS package and the FreeRTOS\+TCP version from latest FreeRTOS releases\.*  
No\. We test libraries with a specific version combination for interoperability, and release these combinations as version\-tagged bundles \(for example, [FreeRTOS 202012\.xx LTS](https://github.com/FreeRTOS/FreeRTOS-LTS/tree/202012-LTS), [FreeRTOS 202112\.00](https://github.com/FreeRTOS/FreeRTOS/tree/202112.00)\)\. You can find information on these combinations in `manifest.yml` files in corresponding repositories \(for example, [FreeRTOS 202012\.xx LTS manifest file](https://github.com/FreeRTOS/FreeRTOS-LTS/blob/202012-LTS/manifest.yml)\)\.

Q: *What happens to my existing qualified boards?*  
The existing qualified boards will continue to be listed in the AWS Partner Device Catalog\. When needed, we will directly communicate any changes required to the existing qualifications\. If you want to upgrade your qualified boards to the latest FreeRTOS libraries or FreeRTOS LTS versions, you must re\-verify against the new [tests](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests)\.

Q: *Do I need to test using abstraction layers in FreeRTOS, including secure\-sockets and Wi\-Fi management?*  
No\. See the required software libraries and tests in the [Porting flowchart](https://docs.aws.amazon.com/freertos/latest/portingguide/porting-chart.html) in the *FreeRTOS Porting Guide*\.

Q: *Do I need to start over if a new FreeRTOS version is released while I am porting the previous version?*  
No\. You can still qualify using the previous version\. However, we strongly recommend that you use the latest FreeRTOS or FreeRTOS LTS version available at the time you start porting\.

Q: *My board uses a kernel architecture that I have modified and is not part of the official FreeRTOS release\. Can I still qualify?*  
No, only official kernel ports available from [GitHub](https://github.com/FreeRTOS/FreeRTOS-Kernel) are accepted for qualification\. If you have an unsupported architecture or additional functionality to add to an existing kernel port, you can follow our [Contributing Guidelines](https://github.com/aws/amazon-freertos/blob/main/CONTRIBUTING.md) to submit a pull request to [GitHub](https://github.com/FreeRTOS/FreeRTOS-Kernel)\. After the pull request is reviewed and merged, it will become official and you will be able to qualify with the kernel port\. For more information, contact your local APN representative\.

Q: *My board does not offload TCP/IP to hardware\. Is a particular TCP/IP stack required for FreeRTOS qualification?*  
If your board does not have on\-chip TCP/IP functionality, you can use either the FreeRTOS\+TCP TCP/IP stack or the latest version of the lwIP TCP/IP stack\. For more information, see [Porting a TCP/IP Stack](https://docs.aws.amazon.com/freertos/latest/portingguide/afr-porting-tcp.html) in the *FreeRTOS Porting Guide*\.

Q: *Do we need to implement PKCS11 even though the TLS stack is offloaded to the communications chip?*  
No, you don’t need to implement or test PKCS11\.

Q: *My device uses only one of the protocols \(HTTP, MQTT\) and only one of the available communication channels \(Wi\-Fi, Ethernet, BLE\)\. If all the OTA related IDT tests pass using just one protocol\-communication channel combination, then will my device get qualified?*  
Yes\. However, we encourage you to get other combinations qualified on your device as well, if possible\. In this way, you can provide support for more customer use cases\.

Q: *We will be hosting our FreeRTOS port in our own repository as per the qualification requirements\. What should be included in the repository in terms of folders and demos for support?*  
Host all the files and folders necessary to make the port work as an out\-of\-the\-box experience for a customer who downloads it from the repository\. You can submodule the FreeRTOS kernel, FreeRTOS libraries, FreeRTOS tests, third\-party libraries, and vendor\-specific files, along with a docs folder for your documents and your demo folder\. The coreMQTT Agent demo must be supported\. Other demos are at your discretion\.

Q: *My device uses only cellular connectivity\. Can I still qualify?*  
Yes\. The Cellular Interface library supports the AT commands of a TCP offloaded Cellular abstraction layer\. These are available from [GitHub](https://github.com/FreeRTOS/FreeRTOS-Cellular-Interface)\. For more information, see [ Porting the Cellular Interface library](https://docs.aws.amazon.com/freertos/latest/portingguide/freertos-porting-cellular.html) in the *FreeRTOS Porting Guide*\.

Q: *Do we still use the [amazon\-freertos](https://github.com/aws/amazon-freertos) repository for qualification?*  
No, [AWS Device Qualification Program for FreeRTOS](https://aws.amazon.com/partners/programs/dqp/) does not enforce a specific project structure, and gives you the flexibility to use your own project structure and repository\. You can submodule the required FreeRTOS libraries, qualification tests, and third\-party libraries into your workspace, and run [AWS IoT Device Tester](https://aws.amazon.com/freertos/device-tester/) for FreeRTOS to validate the integration\.

Q: *If [amazon\-freertos](https://github.com/aws/amazon-freertos) is not required for qualification, how do we qualify? What is the directory structure required?*  
From the FreeRTOS LTS [\(GitHub\)](https://github.com/FreeRTOS/FreeRTOS-LTS) or the latest FreeRTOS release [\(GitHub\)](https://github.com/FreeRTOS/FreeRTOS), use the FreeRTOS kernel and select the FreeRTOS libraries relevant to your project\. There is a separate GitHub repository for [tests](https://github.com/FreeRTOS/FreeRTOS-Libraries-Integration-Tests), so you can select the repository and libraries you need \(for example, FreeRTOS/coreMQTT, MCU vendor SDK, third\-party libs such as lwIP\)\. You can also use any project structure and run the tests relevant to the features of your board\.

Q: *Where do we host the ported/qualified code?*  
You can host the ported code in any repository based on the application and needs of your customers\. The repository link must be publicly available and linked to the AWS Partner Device Catalog product page\.

Q: *Is passing the OTA tests needed for FreeRTOS qualification?*  
Yes\. Customers want their deployed AWS IoT devices to have the functionality to be remotely updated, so all new qualifications will need to pass the OTA tests\.

Q: *How long is my qualification valid?*  
An existing FreeRTOS qualification is valid as long as the board or the software components \(for example, FreeRTOS libraries, drivers, third\-party libraries\) are not discontinued\. FreeRTOS LTS based qualifications are not valid after the corresponding LTS period ends\.

Q: *When does AWS recommend renewal of qualification?*  
We recommend you periodically re\-qualify with the latest FreeRTOS LTS or FreeRTOS versions so that customers get the latest security patches, valid LTS libraries, or new FreeRTOS features\.

Q: *Can I use AWS IoT Device Tester to test my FreeRTOS implementation but not to qualify my board?*  
Yes, we encourage you to use AWS IoT Device Tester and [AWS IoT Device Advisor](https://aws.amazon.com/iot-core/features/) to test your FreeRTOS implementations\.

Q: *Do I need to pay to use AWS IoT Device Tester?*  
No, it is free to use\. However, you may incur some charges due to the use of AWS services \(for example, for MQTT messages, connectivity, OTA execution\)\.

If you have questions about qualification that are not answered on this page or in the rest of the *FreeRTOS Qualification Guide*, contact your AWS representative or [the FreeRTOS engineering team](https://freertos.org/RTOS-contact-and-support.html)\.