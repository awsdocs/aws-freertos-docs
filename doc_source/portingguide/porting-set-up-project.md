# Setting Up Your Amazon FreeRTOS Source Code for Porting<a name="porting-set-up-project"></a>

After you download Amazon FreeRTOS, you need to configure some of the files and folders in the Amazon FreeRTOS download before you can begin porting\.

To prepare your Amazon FreeRTOS download for porting, you need to follow the instructions in [Configuring the Amazon FreeRTOS Download](porting-set-up-directory.md) to configure the directory structure of your Amazon FreeRTOS download to fit your device\.

If you plan to test the ported libraries as you implement them for debugging purposes, you also need to configure some files for testing before you begin porting\. For instructions on test set up, see [Setting Up Your Amazon FreeRTOS Source Code for Testing](testing-set-up.md)\.

**Note**  
You must use the AWS IoT Device Tester for Amazon FreeRTOS to officially validate your ports for qualification\. For more information about AWS IoT Device Tester for Amazon FreeRTOS, see [Using AWS IoT Device Tester for Amazon FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the Amazon FreeRTOS User Guide\.  
For information about qualifying your device for Amazon FreeRTOS, see the [Amazon FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\.

After you configure your Amazon FreeRTOS download and set up your testing environment, you can beginning porting Amazon FreeRTOS\. For porting and testing instructions, see [Porting the Amazon FreeRTOS Libraries](afr-porting.md)\.