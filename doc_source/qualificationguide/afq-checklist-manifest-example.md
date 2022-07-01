# Example manifest\.yml<a name="afq-checklist-manifest-example"></a>

```
---
# This is an example of the manifest file that is included at the root of all FreeRTOS GitHub repositories.

name : "Project_Name"
version: "202012.00-LTS"
description: "Clear concise description of this project."         

dependencies:
  - name: "dependency_1"
    version: "v1.0.0"
    repository:
      type: "git"
      url: "https://github.com/account/dependency_1"
      path: "/relative/path/from/project/root/to/dependency_1"
      branch: "1.x"
  - name: "dependency_2"
    version: "v1.0.1_LTS"
    repository:
      type: "git"
      url: "https://github.com/account/dependency_1"
      path: "/relative/path/from/project/root/to/dependency_2"

license: "MIT"
```