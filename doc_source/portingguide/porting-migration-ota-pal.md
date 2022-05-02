# Migrating from version 1 to version 3 for OTA Pal port<a name="porting-migration-ota-pal"></a>

The Over\-the\-air Updates Library introduced some changes in the folder structure and the placement of configurations required by the library and the demo applications\. For OTA applications designed to work with v1\.2\.0 to migrate to v3\.0\.0 of the library, you must update the PAL port function signatures and include additional configuration files as described in this migration guide\.

## Changes to OTA PAL<a name="porting-migration-ota-pal-changes"></a>
+ The OTA PAL port directory name has been updated from `ota` to `ota_pal_for_aws`\. This folder must contain 2 files: `ota_pal.c` and `ota_pal.h`\. The PAL header file `libraries/freertos_plus/aws/ota/src/aws_iot_ota_pal.h` has been deleted from the OTA library and must be defined inside the port\.
+ The return codes \(`OTA_Err_t`\) are translated into an enum `OTAMainStatus_t`\. Refer to [ ota\_platform\_interface\.h](https://github.com/aws/ota-for-aws-iot-embedded-sdk/blob/v3.0.0/source/include/ota_platform_interface.h#L68-L90) for translated return codes\. [ Helper macros](https://github.com/aws/ota-for-aws-iot-embedded-sdk/blob/666241d0f643b07d5146a3715b649d80f8135e0b/source/include/ota_platform_interface.h#L105-L111) are also provided to combine `OtaPalMainStatus` and `OtaPalSubStatus` codes and extract `OtaMainStatus` from `OtaPalStatus` and similar\.
+ Logging in the PAL 
  + Removed the `DEFINE_OTA_METHOD_NAME` macro\.
  + Earlier: `OTA_LOG_L1( "[%s] Receive file created.\r\n", OTA_METHOD_NAME );`\.
  + Updated: `LogInfo(( "Receive file created."));` Use `LogDebug`, `LogWarn` and `LogError` for the appropriate log\.
+ Variable `cOTA_JSON_FileSignatureKey` changed to `OTA_JsonFileSignatureKey`\.

### Functions<a name="porting-migration-ota-pal-functions"></a>

The function signatures are defined in `ota_pal.h` and start with the prefix `otaPal` instead of `prvPAL`\.

**Note**  
The exact name of the PAL is technically open ended, but to be compatible with the qualification tests, the name should conform to the ones specified below\.
+ Version 1: `OTA_Err_t prvPAL_CreateFileForRx( OTA_FileContext_t * const *C* );`

  Version 3: `OtaPalStatus_t otaPal_CreateFileForRx( OtaFileContext_t * const *pFileContext* );`

  Notes: Create a new receive file for the data chunks as they come in\.
+ Version 1: `int16_t prvPAL_WriteBlock( OTA_FileContext_t * const C, uint32_t ulOffset, uint8_t * const pcData, uint32_t ulBlockSize );`

  Version 3: `int16_t otaPal_WriteBlock( OtaFileContext_t * const pFileContext, uint32_t ulOffset, uint8_t * const pData, uint32_t ulBlockSize );`

  Notes: Write a block of data to the specified file at the given offset\. 
+ Version 1: `OTA_Err_t prvPAL_ActivateNewImage( void );`

  Version 3: `OtaPalStatus_t otaPal_ActivateNewImage( OtaFileContext_t * const *pFileContext* );`

  Notes: Activate the newest MCU image received via OTA\.
+ Version 1: `OTA_Err_t prvPAL_ResetDevice( void );`

  Version 3: `OtaPalStatus_t otaPal_ResetDevice( OtaFileContext_t * const *pFileContext* );`

  Notes: Reset the device\.
+ Version 1: `OTA_Err_t prvPAL_CloseFile( OTA_FileContext_t * const *C* );`

  Version 3: `OtaPalStatus_t otaPal_CloseFile( OtaFileContext_t * const *pFileContext* );`

  Notes: Authenticate and close the underlying receive file in the specified OTA context\.
+ Version 1: `OTA_Err_t prvPAL_Abort( OTA_FileContext_t * const *C* );`

  Version 3: `OtaPalStatus_t otaPal_Abort( OtaFileContext_t * const *pFileContext* );`

  Notes: Stop an OTA transfer\.
+ Version 1: `OTA_Err_t prvPAL_SetPlatformImageState( OTA_ImageState_t *eState* );`

  Version 3: `OtaPalStatus_t otaPal_SetPlatformImageState( OtaFileContext_t * const pFileContext, OtaImageState_t eState );`

  Notes: Attempt to set the state of the OTA update image\.
+ Version 1: `OTA_PAL_ImageState_t prvPAL_GetPlatformImageState( void );`

  Version 3: `OtaPalImageState_t otaPal_GetPlatformImageState( OtaFileContext_t * const *pFileContext* );`

  Notes: Get the state of the OTA update image\.

### Data Types<a name="porting-migration-ota-pal-data-types"></a>
+ Version 1: `OTA_PAL_ImageState_t`

  File: `aws_iot_ota_agent.h`

  Version 3: `OtaPalImageState_t`

  File: `ota_private.h`

  Notes: *The image state set by platform implementation\.*
+ Version 1: `OTA_Err_t`

  File: `aws_iot_ota_agent.h`

  Version 3: `OtaErr_t OtaPalStatus_t (combination of OtaPalMainStatus_t and OtaPalSubStatus_t)`

  File: `ota.h`, `ota_platform_interface.h`

  Notes: v1: These were macros defining a 32 unsigned integer\. v3: Specialized enum representing the type of error and associated with an error code\.
+ Version 1: `OTA_FileContext_t`

  File: `aws_iot_ota_agent.h`

  Version 3: `OtaFileContext_t`

  File: `ota_private.h`

  Notes: v1: Contains an enum and buffers for the data\. v3: Contains additional data\-length variables\.
+ Version 1: `OTA_ImageState_t`

  File: `aws_iot_ota_agent.h`

  Version 3: `OtaImageState_t`

  File: `ota_private.h`

  Notes: *OTA Image states*

### Configuration changes<a name="porting-migration-ota-pal-config-changes"></a>

The file `aws_ota_agent_config.h` was renamed to [https://github.com/aws/amazon-freertos/blob/main/vendors/pc/boards/windows/aws_demos/config_files/ota_config.h](https://github.com/aws/amazon-freertos/blob/main/vendors/pc/boards/windows/aws_demos/config_files/ota_config.h) which changes the include guards from `_AWS_OTA_AGENT_CONFIG_H_` to `OTA_CONFIG_H_`\.
+ The file `aws_ota_codesigner_certificate.h` has been deleted\.
+ Included the new logging stack to print debug messages:

  ```
  /**************************************************/
  /******* DO NOT CHANGE the following order ********/
  /**************************************************/
  
  /* Logging related header files are required to be included in the following order:
   * 1. Include the header file "logging_levels.h".
   * 2. Define LIBRARY_LOG_NAME and  LIBRARY_LOG_LEVEL.
   * 3. Include the header file "logging_stack.h".
   */
  
  /* Include header that defines log levels. */
  #include "logging_levels.h"
  
  /* Configure name and log level for the OTA library. */
  #ifndef LIBRARY_LOG_NAME
      #define LIBRARY_LOG_NAME     "OTA"
  #endif
  #ifndef LIBRARY_LOG_LEVEL
      #define LIBRARY_LOG_LEVEL    LOG_INFO
  #endif
  
  #include "logging_stack.h"
  
  /************ End of logging configuration ****************/
  ```
+ Added the constant config:

  ```
  /** * @brief Size of the file data block message (excluding the header). */   
  #define otaconfigFILE_BLOCK_SIZE ( 1UL << otaconfigLOG2_FILE_BLOCK_SIZE )
  ```

**New File:** [https://github.com/aws/amazon-freertos/blob/main/vendors/pc/boards/windows/aws_demos/config_files/ota_demo_config.h](https://github.com/aws/amazon-freertos/blob/main/vendors/pc/boards/windows/aws_demos/config_files/ota_demo_config.h) contains the configs that are required by the OTA demo such as the code signing certificate and application version\.
+ `signingcredentialSIGNING_CERTIFICATE_PEM` which was defined in `demos/include/aws_ota_codesigner_certificate.h` has been moved to `ota_demo_config.h` as `otapalconfigCODE_SIGNING_CERTIFICATE` and can can be accessed from the PAL files as: 

  ```
  static const char codeSigningCertificatePEM[] = otapalconfigCODE_SIGNING_CERTIFICATE;
  ```

  The file `aws_ota_codesigner_certificate.h` has been deleted\.
+ The macros `APP_VERSION_BUILD`, `APP_VERSION_MINOR`, `APP_VERSION_MAJOR` have been added to `ota_demo_config.h`\. The old files containing the version information have been removed, for example `tests/include/aws_application_version.h`, `libraries/c_sdk/standard/common/include/iot_appversion32.h`, `demos/demo_runner/aws_demo_version.c`\.

## Changes to the OTA PAL tests<a name="porting-migration-ota-pal-test-changes"></a>
+ Removed the "Full\_OTA\_AGENT" test group along with all related files\. This test group was previously required for qualification\. These tests were for the OTA library and not specific to the OTA PAL port\. The OTA library now has full test coverage that is hosted in the OTA repository so this test group is no longer required\.
+ Removed the "Full\_OTA\_CBOR" and "Quarantine\_OTA\_CBOR" test groups as well as all related files\. These tests were not part of the qualification tests\. The functionalities these tests covered are now being tested in the OTA repository\.
+ Moved the testing files from the library directory to the `tests/integration_tests/ota_pal` directory\.
+ Updated the OTA PAL qualification tests to use v3\.0\.0 of the OTA library API\.
+ Updated how the OTA PAL tests access the code signing certificate for tests\. Previously there was a dedicated header file for the code signing credential\. This is no longer the case for the new version of the library\. The test code expects this variable to be defined in `ota_pal.c`\. The value is assigned to a macro that is defined in the platform specific OTA config file\.

## Checklist<a name="porting-migration-ota-pal-test-checklist"></a>

Use this checklist to make sure you follow the steps required for migration:
+ Update the name of the ota pal port folder from `ota` to `ota_pal_for_aws`\.
+ Add the file `ota_pal.h` with the functions mentioned above\. For an example `ota_pal.h` file, see [ GitHub](https://github.com/aws/amazon-freertos/blob/main/vendors/pc/boards/windows/ports/ota_pal_for_aws/ota_pal.h)\.
+ Add the configuration files:
  + Change the name of the file from `aws_ota_agent_config.h` to \(or create\) `ota_config.h`\.
    + Add:

      ```
      otaconfigFILE_BLOCK_SIZE ( 1UL << otaconfigLOG2_FILE_BLOCK_SIZE )
      ```
    + Include:

      ```
      #include "ota_demo_config.h"
      ```
  + Copy the above files to the `aws_test config` folder and substitute any includes of `ota_demo_config.h` with `aws_test_ota_config.h`\.
  + Add an `ota_demo_config.h` file\.
  + Add an `aws_test_ota_config.h` file\.
+ Make the following changes to `ota_pal.c`: 
  + Update the includes with the latest OTA library file names\.
  + Remove the `DEFINE_OTA_METHOD_NAME` macro\.
  + Update the signatures of the OTA PAL functions\.
  + Update the name of the file context variable from `C` to `pFileContext`\.
  + Update the `OTA_FileContext_t` struct and all related variables\.
  + Update `cOTA_JSON_FileSignatureKey` to `OTA_JsonFileSignatureKey`\.
  + Update the `OTA_PAL_ImageState_t` and `Ota_ImageState_t` types\.
  + Update the error type and values\.
  + Update the printing macros to use the logging stack\.
  + Update the `signingcredentialSIGNING_CERTIFICATE_PEM` to be `otapalconfigCODE_SIGNING_CERTIFICATE`\.
  + Update `otaPal_CheckFileSignature` and `otaPal_ReadAndAssumeCertificate` function comments\.
+ Update the [https://github.com/aws/amazon-freertos/pull/3208/commits/432e13fcb8dfbfeb6de25110a3d2ea14ccaf1b9a](https://github.com/aws/amazon-freertos/pull/3208/commits/432e13fcb8dfbfeb6de25110a3d2ea14ccaf1b9a) file\.
+ Update the IDE projects\.