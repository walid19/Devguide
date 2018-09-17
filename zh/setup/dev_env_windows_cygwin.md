# Windows Cygwin 工具链

该工具链非常轻便，而且容易安装和使用。 它是目前Windows环境下用于PX4开发的最新和最好的工具。

> **提示** 这是官方唯一支持的在Windows环境下开发PX4的工具链（它已经在集成测试系统中经过测试）

该工具链支持：

* 编译/上传 PX4到Nuttx目标(Pixhawk系列飞控)
* JMAVSim/SITL 仿真会获得比其他Windows工具链更好的性能
* 类型校验，轻便安装，完整的命令行支持和许多[其他特性](#features)

这篇文章将解释怎样下载和使用该环境，并且在需要的时候怎样扩展和更新(比如，使用其他的编译器)。

<!--
## Ready to use MSI Installer Download {#installation}

Latest Download: [PX4 Windows Cygwin Toolchain 0.3 Download](https://s3-us-west-2.amazonaws.com/px4-tools/PX4+Windows+Cygwin+Toolchain/PX4+Windows+Cygwin+Toolchain+0.3.msi) (25.07.2018)


Legacy Versions (**deprecated**):

* [PX4 Windows Cygwin Toolchain 0.3 Download](https://s3-us-west-2.amazonaws.com/px4-tools/PX4+Windows+Cygwin+Toolchain/PX4+Windows+Cygwin+Toolchain+0.3.msi) (25.07.2018)
* [PX4 Windows Cygwin Toolchain 0.2 Download](https://s3-us-west-2.amazonaws.com/px4-tools/PX4+Windows+Cygwin+Toolchain/PX4+Windows+Cygwin+Toolchain+0.2.msi) (09.05.2018)
* [PX4 Windows Cygwin Toolchain 0.1 Download](https://s3-us-west-2.amazonaws.com/px4-tools/PX4+Windows+Cygwin+Toolchain/PX4+Windows+Cygwin+Toolchain+0.1.msi) (23.02.2018)
-->

## 安装说明

1. 下载最新的MSI安装文件：[PX4 Windows Cygwin Toolchain 0.3 Download](https://s3-us-west-2.amazonaws.com/px4-tools/PX4+Windows+Cygwin+Toolchain/PX4+Windows+Cygwin+Toolchain+0.3.msi)(25.07.2018)
2. 运行它，选择你需要的安装路径，执行安装 ![jMAVSimOnWindows](../../assets/toolchain/cygwin_toolchain_installer.PNG)
3. 在安装结束后勾选*clone the PX4 repository, build and run simulation with jMAVSim*(这简化了你的开始准备工作)
    
    > **注意**如果你错过了这一步，你需要[手动克隆PX4 Firmware库](#getting_started)

## Getting Started {#getting_started}

The toolchain uses a specially configured console window (started by running the **run-console.bat** script) from which you can call the normal PX4 build commands:

1. Browse to the toolchain installation directory (default **C:\PX4**)
2. Run **run-console.bat** (double click) to start the Cygwin bash console
3. Clone the PX4 Firmware repository from within the console:
    
    > **Note** Cloning only needs to be done once! Skip this step if you ticked the installer option to *clone the PX4 repository, build and run simulation with jMAVSim*.
    
    ```bash
    # Clone PX4 Firmware repository into the home folder & loads submodules in parallel
    git clone --recursive -j8 https://github.com/PX4/Firmware.git
    ```
    
    You can now use the console/Firmware repository to build PX4.

4. For example, to run JMAVSim:
    
    ```bash
    # Navigate to Firmware repo
    cd Firmware
    # Build and runs SITL simulation with jMAVSim to test the setup
    make posix jmavsim
    ```
    
    The console will then display:
    
    ![jMAVSimOnWindows](../../assets/simulation/jmavsim_windows_cygwin.PNG)

Continue next to [the detailed instructions on how to build PX4](../setup/building_px4.md) (or see the section below for more general usage instructions).

## Usage Instructions {#usage_instructions}

The installation directory (default: **C:\PX4**) contains batch scripts for launching console windows and starting different IDEs inside the Cygwin toolchain environment. The full list of scripts provided is.

* **run-console.bat** - Start the POSIX (linux like) bash console.
* **run-eclipse.bat** - Start the build in portable [eclipse for C++ IDE](http://www.eclipse.org/downloads/eclipse-packages/).
* **run-vscode.bat** - Start the [Visual Studio Code IDE](https://code.visualstudio.com/) (this must be installed separately) from its default install directory: `C:\Program Files\Microsoft VS Code`

> **Tip** The [Manual Setup](#manual_setup) section explains why you need to use the scripts and how it all works.

<span></span>

> **Tip** You can create desktop shortcuts to the batch scripts to have easier access, the installer does not yet create them for you (as of toolchain version 0.2).

The ordinary workflow consists of starting a console window by double clicking on the **run-console.bat** script to manually run terminal commands. Developers who prefer an IDE can start it with the corresponding **run-XXX.bat** script to edit code/run builds.

### Windows & Git Special Cases

#### Windows CR+LF vs Unix LF Line Endings

We recommend that you force Unix style LF endings for every repository you're working with using this toolchain (and use an editor which preserves them when saving your changes - e.g. Eclipse or VS Code). Compilation of source files also works with CR+LF endings checked out locally, but there are cases in Cygwin (e.g. execution of shell scripts) that require Unix line endings ( otherwise you get errors like `$'\r': Command not found.`). Luckily git can do this for you when you execute the two commands in the root directory of your repo:

    git config core.autocrlf false
    git config core.eol lf
    

If you work with this toolchain on multiple repositories you can also set these two configurations globally for your machine:

    git config --global ...
    

This is not recommended because it may affect any other (unrelated) git use on your Windows machine.

#### Unix Permissions Execution Bit

Under Unix there's a flag in the permissions of each file which tells the OS whether or not the file is allowed to be executed. *git* under Cygwin supports and cares about that bit (even though Windows has a different permission system). This often results in *git* finding differences in permissions even if there is no real diff which looks like this:

    diff --git ...
    old mode 100644
    new mode 100755
    

We recommend disabling this functionality by executing `git config core.fileMode false` in every repo where you use with this toolchain.

## Additional Information

### Features / Issues {#features}

The following features are known to work (version 2.0):

* Building and running SITL with jMAVSim with significantly better performance than a VM (it generates a native windows binary **px4.exe**).
* Building and uploading NuttX builds (e.g.: px4fmu-v2 and px4fmu-v4)
* Style check with *astyle* (supports the command: `make format`)
* Command line auto completion
* Non-invasive installer! The installer does NOT affect your system and global path (it only modifies the selected installation directory e.g. **C:\PX4** and uses a temporary local path).
* The installer supports updating to a new version keeping your personal changes inside the toolchain folder

Omissions:

* Simulation: Gazebo and ROS are not supported
* Only NuttX and JMAVSim/SITL builds are supported.
* [Known problems / Report your issue](https://github.com/orgs/PX4/projects/6)

### Shell Script Installation {#script_setup}

You can also install the environment using shell scripts in the Github project.

1. Make sure you have [Git for Windows](https://git-scm.com/download/win) installed.
2. Clone the repository https://github.com/PX4/windows-toolchain to the location you want to install the toolchain. Default location and naming is achieved by opening the `Git Bash` and executing:

    cd /c/
    git clone https://github.com/PX4/windows-toolchain PX4
    

1. If you want to install all components navigate to the freshly cloned folder and double click on the script `install-all-components.bat` located in the folder `toolchain`. If you only need certain components and want to safe Internet traffic and or disk space you can navigate to the different component folders like e.g. `toolchain\cygwin64` and click on the **install-XXX.bat** scripts to only fetch something specific.
2. Continue with [Getting Started](#getting_started) (or [Usage Instructions](#usage_instructions)) 

### Manual Installation (for Toolchain Developers) {#manual_setup}

This section describes how to setup the Cygwin toolchain manually yourself while pointing to the corresponding scripts from the script based installation repo. The result should be the same as using the scripts or MSI installer.

> **Note** The toolchain gets maintained and hence these instructions might not cover every detail of all the future changes.

1. Create the *folders*: **C:\PX4**, **C:\PX4\toolchain** and **C:\PX4\home**
2. Download the *Cygwin installer* file [setup-x86_64.exe](https://cygwin.com/setup-x86_64.exe) from the [official Cygwin website](https://cygwin.com/install.html)
3. Run the downloaded setup file
4. In the wizard choose to install into the folder: **C:\PX4\toolchain\cygwin64**
5. Select to install the default Cygwin base and the newest available version of the following additional packages:

* **Category:Packagename**
* Devel:cmake (3.3.2 gives no deprecated warnings, 3.6.2 works but has the warnings)
* Devel:gcc-g++
* Devel:git
* Devel:make
* Devel:ninja
* Devel:patch
* Editors:xxd
* Editors:nano (unless you're the vim pro)
* Python:python2
* Python:python2-pip
* Python:python2-numpy
* Python:python2-jinja2
* Archive:unzip
* Utils:astyle
* Shells:bash-completion
* Web:wget
    
    > **Note** Do not select as many packages as possible which are not on this list, there are some which conflict and break the builds.
    
    <span></span>
    
    > **Note** That's what [cygwin64/install-cygwin-px4.bat](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/cygwin64/install-cygwin-px4.bat) does.

1. Write up or copy the **batch scripts** [`run-console.bat`](https://github.com/MaEtUgR/PX4Toolchain/blob/master/run-console.bat) and [`setup-environment-variables.bat`](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/setup-environment-variables.bat).
    
    The reason to start all the development tools through the prepared batch scripts is they preconfigure the starting program to use the local, portable Cygwin environment inside the toolchain's folder. This is done by always first calling the script [**setup-environment-variables.bat**](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/setup-environment-variables.bat) and the desired application like the console after that.
    
    The script [`setup-environment-variables.bat`](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/setup-environment-variables.bat) locally sets environmental variables for the workspace root directory `PX4_DIR`, all binary locations `PATH`, and the home directory of the unix environment `HOME`.

2. Add necessary **python packages** to your setup by opening the Cygwin toolchain console (double clicking **run-console.bat**) and executing
    
        pip2 install toml
        pip2 install pyserial
        pip2 install pyulog
        
    
    > **Note** That's what [cygwin64/install-cygwin-python-packages.bat](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/cygwin64/install-cygwin-python-packages.bat) does.

3. Download the [**ARM GCC compiler**](https://developer.arm.com/open-source/gnu-toolchain/gnu-rm/downloads) as zip archive of the binaries for Windows and unpack the content to the folder `C:\PX4\toolchain\gcc-arm`.
    
    > **Note** This is what the toolchain does in: [gcc-arm/install-gcc-arm.bat](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/gcc-arm/install-gcc-arm.bat).

4. Install the JDK:
    
    * Download the [**Java Development Kit Installer**](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Because sadly there is no portable archive containing the binaries directly you have to install it.
    * Find the binaries and move/copy them to **C:\PX4\toolchain\jdk**.
    * You can uninstall the Kit from your Windows system again, we only needed the binaries for the toolchain.
    
    > **Note** This is what the toolchain does in: [jdk/install-jdk.bat](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/jdk/install-jdk.bat).

5. Download [**Apache Ant**](https://ant.apache.org/bindownload.cgi) as zip archive of the binaries for Windows and unpack the content to the folder `C:\PX4\toolchain\apache-ant`.
    
    > **Tip** Make sure you don't have an additional folder layer from the folder which is inside the downloaded archive.
    
    <span></span>
    
    > **Note** This is what the toolchain does in: [apache-ant/install-apache-ant.bat](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/apache-ant/install-apache-ant.bat).

6. Download, build and add *genromfs* to the path:
    
    * Clone the source code to the folder **C:\PX4\toolchain\genromfs\genromfs-src** with 
            cd /c/toolchain/genromfs
            git clone https://github.com/chexum/genromfs.git genromfs-src

* Compile it with: 
    
        cd genromfs-src
         make all
    
    * Copy the resulting binary **genromfs.exe** one folder level out to: **C:\PX4\toolchain\genromfs**
    
    > **Note** This is what the toolchain does in: [genromfs/install-genromfs.bat](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/genromfs/install-genromfs.bat).

1. Make sure all the binary folders of all the installed components are correctly listed in the `PATH` variable configured by [**setup-environment-variables.bat**](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/setup-environment-variables.bat).