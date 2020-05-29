# Setting Up Your FreeRTOS Source Code for Porting<a name="porting-set-up-project"></a>

After you download FreeRTOS, you need to configure some of the files and folders in the FreeRTOS download before you can begin porting\.

To prepare your FreeRTOS download for porting, you need to follow the instructions in [Configuring the FreeRTOS download](porting-set-up-directory.md) to configure the directory structure of your FreeRTOS download to fit your device\.

If you plan to test the ported libraries as you implement them for debugging purposes, you also need to configure some files for testing before you begin porting\. For instructions on test set up, see [Setting up your FreeRTOS source code for testing](testing-set-up.md)\.

**Note**  
You must use the AWS IoT Device Tester for FreeRTOS to officially validate your ports for qualification\. For more information about AWS IoT Device Tester for FreeRTOS, see [Using AWS IoT Device Tester for FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/device-tester-for-freertos-ug.html) in the FreeRTOS User Guide\.  
For information about qualifying your device for FreeRTOS, see the [FreeRTOS Qualification Guide](https://docs.aws.amazon.com/freertos/latest/qualificationguide/)\.

After you configure your FreeRTOS download and set up your testing environment, you can beginning porting FreeRTOS\. For porting and testing instructions, see [Porting the FreeRTOS Libraries](afr-porting.md)\.