# Prerequisites<a name="building-cmake-prereqs"></a>

Make sure that your host machine meets the following prerequisites before you continue:
+ Your device's compilation toolchain must support the machine's operating system\. CMake supports all versions of Windows, macOS, and Linux\.

  Windows subsystem for Linux \(WSL\) is not supported\. Use native CMake on Windows machines\.
+ You must have CMake version 3\.13 or later installed\.

  You can download the binary distribution of CMake from [CMake\.org](https://cmake.org/download/)\.
**Note**  
If you download the binary distribution of CMake, make sure that you add the CMake executable to the PATH environment variable before you use CMake from command line\.

  You can also download and install CMake using a package manager, like [homebrew](https://brew.sh/) on macOS, and [ scoop](https://scoop.sh/) or [chocolatey](https://chocolatey.org/) on Windows\.
**Note**  
The CMake package versions in the package managers for many Linux distributions are out\-of\-date\. If your distribution's package manager does not include the the latest version of CMake, you can try `linuxbrew` or `nix`\.
+ You must have a compatible native build system\.

  CMake can target many native build systems, including [GNU Make](https://www.gnu.org/software/make/) or [Ninja](https://github.com/ninja-build/ninja/releases)\. Both Make and Ninja can be installed with package managers on Linux, macOS, and Windows\. If you are using Make on Windows, you can install a standalone version from [Equation](http://www.equation.com/servlet/equation.cmd?fa=make), or you can install [MinGW](https://sourceforge.net/projects/mingw-w64/files/), which bundles Make\.
**Note**  
The Make executable in MinGW is called `mingw32-make.exe`, instead of `make.exe`\.

  We recommend that you use Ninja, because it is faster than Make and also provides native support to all desktop operating systems\.