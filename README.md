# WinSDK71_VisualStudio2019
Get your Visual Studio 2019 (2017) to work with Windows SDK 7.1

------

## Motivation

Windows SDK 7.1 first appeared in 2010.05. It has been 10 years since then. It was designed as an SDK for Windows 7, and many projects still use Windows 7 as the base support today. This is why these projects still want to use this 10 year old version as the most basic development base.

When Windows SDK 7.1 was released, the expected version of Visual Studio was VS2010, which is why SDK 7.1 projects always require VS 2010 support. This is also why it is so difficult to work with the latest versions of VS (2017 / 2019).

Let's start looking at how to get your VS 2019 to work with the Windows 7.1 SDK **without installing VS 2010**.

[**Microsoft Windows SDK from WIKI**](https://en.wikipedia.org/wiki/Microsoft_Windows_SDK)

| Microsoft Windows SDK                                    | Version | Build Version    | Release Date | Description                                                  |
| -------------------------------------------------------- | ------- | ---------------- | ------------ | ------------------------------------------------------------ |
| Included in Visual Studio 2010                           | v7.0a   | 6.1.7600.16385   | 2010-04-12   | It is the latest version that officially supports [Windows XP](https://en.wikipedia.org/wiki/Windows_XP) target. |
| Microsoft Windows SDK for Windows 7 and .NET Framework 4 | v7.1    | 7.1.7600.0.30514 | 2010-05-19   | Introduces the "v110_xp" platform toolset                    |
| Included in Visual Studio 2012 Update 1 (or later)       | v7.1A   | 7.1.51106        |              |                                                              |

This is where we start discussing the possible problems. We will use Visual Studio 2019 as the default development tool and explain in detail how to compile Windows SDK 7.1 and Visual Studio 2010 Visual C++ MFC based projects using Visual Studio 2019 and runs on Windows 10.

## List Challenges

* Install Windows SDK 7.1
* Working with Visual C++ 2010 Projects
  * Install "atlmfc" Visual C++ Files
  * Setup Visual C++ for Your System



## Install Windows SDK 7.1

- Download the Windows SDK 7.1. [Microsoft Windows SDK for Windows 7 and .NET Framework 4 (ISO)](https://www.microsoft.com/en-us/download/details.aspx?id=8442)

- Mount the **ISO** file according your system.

  [Windows SDK 7.1, ISO images](https://www.notion.so/44cd9698b57644108246b19bb35658d4)

> **32** bits platform, please download **GRMSDK_EN_DVD.iso** and **64** bits platform, please download **GRMSDKX_EN_DVD.iso**.

- Don't run the default installer.

  Please don't run the setup.exe in ISO folder. Run \setup\SdkSetup.exe.

- Unselect Visual C++ Compilers and unselect redistributable Packages.

  ![WinSDK71-InstallSDK](/Artwork/InstallSDK71.gif?raw=true "Install Windows SDK 7.1")

  Since the VC 2010 runtime is no long compatible with Windows 10 and SDK, you should not install the old redistribution files. We will try to install new version later.

- Install Windows VC 2010 SP1 Update for SDK 7.1 [Microsoft Visual C++ 2010 Service Pack 1 Compiler Update for the Windows SDK 7.1](https://www.microsoft.com/en-US/download/details.aspx?id=4422) Download and Install **VC-Compiler-KB2519277.exe**.

- Install VC 2010 SP1 Redistributable Packages.

  [Microsoft Visual C++ 2010 SP1 Redistributable Package (x64)](http://www.microsoft.com/en-US/download/details.aspx?id=13523)

  [Microsoft Visual C++ 2010 SP1 Redistributable Package (x86)](https://www.microsoft.com/en-US/download/details.aspx?id=8328)

  Download and Install "**vcredist_x64(2010).exe**" and "**vcredist_x86(2010).exe**".

After these steps, Windows SDK 7.1 has been successfully installed on your Windows 10 system. You can start to use SDK to build and compile your C/C++ source code.  But this is not our intention, we want the SDK to run perfectly with Visual Studio 2019. Now, select "**Windows7.1SDK**" in your Platform Toolset.

![WinSDK71-SDK-PROP](/Artwork/SDK71_prop.png?raw=true "Visual Studio SDK")

## Working with Visual C++ 2010 Projects

If your project is based on VC 2010, you need to access the  "**atlmfc**" headers, libraries and binary files. These files are not included in Windows SDK 7.1 and VC Compiler update (**VC-Compiler-KB2519277.exe**).  To overcome this problem, you need to install Visual C++ files.

### Install "atlmfc" Visual C++ files

Open **%ProgramFiles(x86)%\Microsoft Visual Studio 10.0\VC** and there is no atlmfc. This means you can not build your Visual C++ project. To build the VC project, you need install atlmfc.

![WinSDK71-ATLMFC-1](/Artwork/Install_atlmfc_1.png?raw=true "Select atlmfc file")

Due to the "license" issue, it is not possible to download atlmfc directly. you must copy and paste from another computer that already has VS 2010 installed. For my case, I copy the atlmfc from my test computer to this Visual Studio 2019 PC.

Copy and install **atlmfc** to **%ProgramFiles(x86)%\Microsoft Visual Studio 10.0\VC** so that your VC project could compile and link with **atlmfc** libraries.

### Setup Visual C++ for Your System

After installing VC++ files, you should setup system registries for VS 2010 and your Visual Studio 2019 will find your installed VC 2010 automatically.

- **Update Visual Studio 2010 Registry Settings**

```xml
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\\SOFTWARE\\WOW6432Node\\Microsoft\\VisualStudio\\10.0]
"Source Directories"="C:\\\\Program Files (x86)\\\\Microsoft Visual Studio 10.0\\\\VC\\\\crt\\\\src\\\\;;;"

[HKEY_LOCAL_MACHINE\\SOFTWARE\\WOW6432Node\\Microsoft\\VisualStudio\\10.0\\Setup]

[HKEY_LOCAL_MACHINE\\SOFTWARE\\WOW6432Node\\Microsoft\\VisualStudio\\10.0\\Setup\\VC]
"ProductDir"="C:\\\\Program Files (x86)\\\\Microsoft Visual Studio 10.0\\\\VC\\\\"

[HKEY_LOCAL_MACHINE\\SOFTWARE\\WOW6432Node\\Microsoft\\VisualStudio\\10.0\\Setup\\VS]
"ProductDir"="C:\\\\Program Files (x86)\\\\Microsoft Visual Studio 10.0\\\\"
```

Change the Visual Studio 2010 paths according your system.

Visual Studio 2019 uses "Microsoft.Cpp.Win32.Windows7.1SDK.targets" to probe your installed Windows SDK 7.1 and Visual C++ 2010. With above REG update, Visual Studio will read out your in VC paths and related variables properly and your old Visual C++ 2010 projects could be built successfully.

- **Fix Compiler Errors, File Tracking Issue**

Visual Studio will track changes to your source files and related files. It needs to set up the tracer correctly so that file tracing will work properly.

To solve this problem, basically, we have two options. The first option is to disable this feature. The other option is to set the correct track parameters.

**Disable File Track Feature:**

If you don't need this feature, then turning it off is the easiest option for you. Just turn off the "**TrackFileAccess**" parameter to solve the problem. We have already wrapped this function in a prop file. You just need to use it in your project.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" xmlns="<http://schemas.microsoft.com/developer/msbuild/2003>">
  <ImportGroup Label="PropertySheets" />
  <PropertyGroup>
    <_PropertySheetDisplayName>SDK7.1 Patch No Track</_PropertySheetDisplayName>
  </PropertyGroup>
  <PropertyGroup Label="Globals">
    <TrackFileAccess>false</TrackFileAccess>
  </PropertyGroup>
</Project>
```

View → Other Windows → Property Manager

![WinSDK71-PROPMGT-1](/Artwork/propmgt_1.png?raw=true "Select Property Manager")

Select "Add Existing Property Sheet..." and choose the  property file.

![WinSDK71-PROPMGT-2](/Artwork/propmgt_2.png?raw=true "Select Property File")

**Disadvantage:** You will find that Visual Studio does not automatically detect file changes. You'll have to manually recompile the file over and over again. If you change the code a lot, it can be very annoying.

**Setup Correct Track Parameters**

Another way is to set the TRACK parameters correctly. Again, we have prepared another prop file, you just have to reference to it and make sure that the location in the prop is compatible with your system.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" xmlns="<http://schemas.microsoft.com/developer/msbuild/2003>">
  <ImportGroup Label="PropertySheets" />
  <PropertyGroup>
    <_PropertySheetDisplayName>SDK7.1 Patch</_PropertySheetDisplayName>
  </PropertyGroup>
  <PropertyGroup Label="Globals">
    <TrackFileAccess>true</TrackFileAccess>
    <CLTrackerSdkPath>$(WindowsSdkNetFx4ToolsDir)</CLTrackerSdkPath>
    <LinkTrackerSdkPath>$(WindowsSdkNetFx4ToolsDir)</LinkTrackerSdkPath>
    <RCTrackerSdkPath>$(WindowsSdkNetFx4ToolsDir)</RCTrackerSdkPath>
    <ManifestTrackerSdkPath>$(WindowsSdkNetFx4ToolsDir)</ManifestTrackerSdkPath>	
  </PropertyGroup>
</Project>
```

