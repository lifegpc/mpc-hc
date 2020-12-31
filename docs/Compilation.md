# Compilation instructions

## Part A: Preparing the Visual Studio environment

### Visual Studio 2017/2019

1. Install Visual Studio (any edition will work fine) with at minimum the following components:
    Workloads:
    Desktop Development with C++
    Individual Components:
    C++ x64/x86 build tools
    MFC
    ATL
    Windows Universal CRT SDK
    Windows 8.1 SDK
2. Install the DirectX SDK (June 2010) → <https://go.microsoft.com/fwlink/?LinkID=71193>


## Part B: Install Python 2.7 (optional)

This is required for building the translation DLL files.

1. Install Python version 2.7.18 from <https://www.python.org/downloads/release/python-2718/>
2. Run this command to install a required library:
    c:\Python2.7\Scripts\pip install polib


## Part C: Preparing the MSYS and GCC environment (optional)

This is required for building LAV Filters, which is used as the internal codecs by MPC-HC.

You can skip compilation of LAV Filters by selecting the "Release Lite"/"Debug Lite" build configuration
in the MPC-HC project file. This can be useful for making quick builds during development. The resulting
binary will be missing the internal filter functionality. So don't use this configuration for actual
releases.

1. Download MSYS2 from <http://www.msys2.org/>.
   If you are using a 64-bit Operating System, which you should be, get the 64-bit version.
2. Install it on **`C:\MSYS64\`**. You can always install it somewhere else, but these instructions
   assume the aforementioned place.
3. Run `msys2_shell.bat` (if you didn't use the installer you must restart MSYS2 after first run).
4. Install the needed software by running these commands:
   ```text
   pacman -Syu
   pacman -Su
   pacman -S make pkg-config
   ```
   Note that this is the bare minimum, you can install more packages that can be useful to you.
5. Download and extract MinGW to **`C:\MSYS64\mingw64\`** from <http://files.1f0.de/mingw/>
   (you might use the one that ships with MSYS2, but we recommend this one)
6. It is recommended to add **`C:\MSYS64\mingw64\bin`** and **`C:\MSYS64\usr\bin`** to the %PATH% environment variable.
   Windows Control Panel > System > Advanced System Settings > Environment variables
   This allows you to run GCC and all other MSYS tools from the Windows command line.


## Part D: Yasm

Download YASM and save it as **yasm.exe** in **`C:\MSYS64\usr\bin`** (if using MSYS) or somewhere else in %PATH% (for example **`C:\Windows`**):
   * For 64-bit Windows: <http://www.tortall.net/projects/yasm/releases/yasm-1.3.0-win64.exe> (Recommended)
   * For 32-bit Windows: <http://www.tortall.net/projects/yasm/releases/yasm-1.3.0-win32.exe>


## Part E: Config file with paths

Create a file named **build.user.bat** in the source code folder of MPC-HC (see part F). It should have the following contents (with paths adapted for your system):

    ```bat
    @ECHO OFF
    SET "MSYSTEM=MINGW32"
    SET "MPCHC_MSYS=C:\MSYS"
    SET "MPCHC_MINGW32=%MPCHC_MSYS%\mingw"
    SET "MPCHC_MINGW64=%MPCHC_MINGW32%"
    REM You can set `MSYS2_PATH_TYPE` here or in environment variables so that Git is properly added to your `PATH`
    REM SET "MSYS2_PATH_TYPE=inherit"
    REM `MPCHC_GIT` is optional to set if you chose to add it in `PATH` when installing it and have set `MSYS2_PATH_TYPE`
    SET "MPCHC_GIT=C:\Program Files\Git"
    REM Optional, if you plan to modify the translations, install Python 2.7 or set the variable to its path
    SET "MPCHC_PYTHON=C:\Python27"
    REM Optional, If you want to customize the Windows SDK version used, set the variable
    SET "MPCHC_WINSDK_VER=8.1"
    ```

Notes:

* For Visual Studio, we will try to detect the VS installation path automatically.  If that fails you need to specify the installation path yourself. For example:
  ```
  SET "MPCHC_VS_PATH=%ProgramFiles(x86)%\Microsoft Visual Studio\2019\Community\"
  ```
* If you installed the MSYS/MinGW package in another directory you will have to use that path in the previous steps.
* If you don't have Git installed then the build version will be inaccurate, the revision number will be a hard-coded as zero.


## Part F: Downloading the MPC-HC source

You need Git for downloading the source code.

Install **Git for Windows** from <https://git-for-windows.github.io/> and also **Git Extensions** from <http://gitextensions.github.io/>.
Choose `Use Git from the Windows command prompt`. This isn't mandatory, so if you choose
`Use Git from Git Bash only` make sure you set the `MPCHC_GIT` variable in `build.user.bat`.

Use Git to clone MPC-HC's repository to **C:\mpc-hc** (or anywhere else you like).

    1. Install Git
    2. Run these commands:

        ```text
        git clone --recursive https://github.com/clsid2/mpc-hc.git
        ```

        or

        ```text
        git clone https://github.com/clsid2/mpc-hc.git
        git submodule update --init --recursive
        ```

        If a submodule update fails, try running:

        ```text
        git submodule foreach --recursive git fetch --tags
        ```

        then run the update again

        ```text
        git submodule update --init --recursive
        ```

        Note that you can add `-b master` to the `git clone` command if you want to get the latest
        stable version instead of the latest development version
        
## Part G: Compiling the MPC-HC source

1. Open the solution file **C:\mpc-hc\mpc-hc.sln**.
   Change the solution's configuration to **Release** (in the toolbar).
2. Press **F7** to build the solution.
3. You now have **mpc-hc.exe** under **C:\mpc-hc\bin\mpc-hc_x86**
4. Open the solution file **C:\mpc-hc\mpciconlib.sln**
5. Press **F7** to build the solution.
6. You now have **mpciconlib.dll** under **C:\mpc-hc\bin\mpc-hc_x86**
7. Open the solution file **C:\mpc-hc\mpcresources.sln**
8. Build **BuildAll** project.
9. You now have **mpcresources.XX.dll** under **C:\mpc-hc\bin\mpc-hc_x86\Lang**

Alternatively, you can use **build.bat** that can build everything for you (run: `build.bat help` for more info)


## Part H: Building the installer

Download Inno Setup Unicode v5.5.9 or newer from <http://www.jrsoftware.org/isdl.php>.
Install everything and then go to **C:\mpc-hc\distrib**, open **mpc-hc_setup.iss** with Inno Setup,
read the first comments in the script and compile it.

### NOTES

* **build.bat** can build the installer by using the **installer** or the **packages** switch.
* Use Inno Setup's built-in IDE if you want to edit the iss file and don't change its encoding since it can break easily.
