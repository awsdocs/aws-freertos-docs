# FreeRTOS manifest file instructions<a name="afq-checklist-manifest-instr"></a>

A manifest file is required in all LTS\-based FreeRTOS projects to help customers delineate between dependency versions that are being used, which libraries are LTS, and other metadata that helps customers evaluate and use the software more quickly\.

The file should meet the following requirements:
+ Named `manifest.yml`
+ Placed in the `freertos/vendors/vendor/boards/board/` directory
+ Be in YAML format and follow the [YAML 1\.2 specification](https://yaml.org/spec/1.2/spec.html)

The parameters can be in any order, but we recommended that you put them in the order listed below for optimal readability\. Add comments to the file that help customers use or understand your package\.

**File path**  
Located at the root of a package or library\. There is only one manifest per package\. Dependencies that are brought in might have their own manifest files\.

**Parameters**    
**name**  
The name of the package\. All spaces should be replaced with an underscore \(\_\)\. For example, `My project name - 2020` should be changed to `My_project_name_-_2020`\.  
+ type: string
+ required: true
+ minLength: 1
+ maxLength: 40  
**version**  
The version of the package\. The version can be a release version or version tag\.  
+ type: string
+ required: true
+ minLength: 1
+ maxLength: 30  
**description**  
The human\-readable description of the package\. The description should clearly describe what the package is and what it provides\.  
+ type: string
+ required: true
+ minLength: 30
+ maxLength: 255  
**dependencies**  
A list of all first level dependencies that are required for a user to successfully build this package and which can be retrieved by a Git, Subversion, or Mercurial source code host\. Don't include dependencies that are not available through Git, SVG, or hg\. Don't include dependencies used for tests, documentation generation, or development\. To ensure a good experience for the general public, we recommend that you avoid listing dependencies that are gated or private\.  
+ type: array
+ required: false
+ minLength: 0  
**dependencies\[\]\.name**  
The package name of a dependency\. This must match the package name found in the dependency's `name` parameter\.  
+ type: string
+ required: true
+ minLength: 1
+ maxLength: 40  
**dependencies\[\]\.version**  
The version of a dependency\. The version can be a release version or a version tag\. If any dependencies are included within the package itself, the version must match the manifest file that is in the dependency\.  
+ type: string
+ required: true
+ minLength: 1
+ maxLength: 30  
**dependencies\[\]\.repository**  
Describes the location of the dependency source code\.  
+ type: dictionary
+ required: true  
**dependencies\[\]\.repository\.type**  
The type of repository\.  
+ type: string
+ required: true
+ enum: \[git, svn, hg\]  
**dependencies\[\]\.repository\.url**  
The URL of the location of the repository\. This must be a full URL with a protocol prefix \(for example, https://github\.com/*ACCOUNT\_NAME*/*REPO\_NAME*\)\.  
+ type: string
+ required: true  
**dependencies\[\]\.repository\.branch**  
The branch of the dependency that is used\. If the package uses the default main branch, don't include this parameter to keep the length of the manifest at a minimum\.  
+ type: string
+ required: false  
**license**  
The SPDX license identifier of the library\. For the full list, see [https://spdx\.org/licenses/](https://spdx.org/licenses/)\. It should match the `LICENSE` file included in the root of the repository if it exists\.  
+ type: string
+ required: true