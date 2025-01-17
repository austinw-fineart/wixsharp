[![stand with Ukraine](https://img.shields.io/badge/stand_with-ukraine-ffd700.svg?labelColor=0057b7)](https://stand-with-ukraine.pp.ua)

<img align="right" src="https://github.com/oleg-shilo/wixsharp/blob/master/Documentation/wiki_images/wixsharp_logo.png" alt="" style="float:right">

[![Build status](https://ci.appveyor.com/api/projects/status/jruj9dmf2dwjn5p3?svg=true)](https://ci.appveyor.com/project/oleg-shilo/wixsharp)
[![NuGet version (WixSharp)](https://img.shields.io/nuget/v/WixSharp.svg?style=flat-square)](https://www.nuget.org/packages/WixSharp/)
[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.cs-script.net/cs-script/Donation.html)

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.cs-script.net/cs-script/Donation.html)


# WixSharp (WixSharp) - managed interface for WiX

**_Framework for building a complete MSI or WiX source code by using script files written with the C# syntax._**

_Note: WixSharp support for WiX3 Toolset has been discontinued from 2025. WixSharp Nuget packages and Visual Studio templates that target WiX3 will still work but there will no longer be any development in the WiX3 targeting codebase. Please follow the WiX team guidelines for migrating to the actively supported versions of their tools. YOu can continue using WixSharp as usual. For details on how to install WiX5 see [this page](https://github.com/oleg-shilo/wixsharp/wiki#dependencies)._

## Project Description

WixSharp is a member of the [CS-Script](https://www.cs-script.net/) family. WixSharp allows building a complete MSI or WiX source code by executing script files written with 
the plain C# syntax. WixSharp engine uses a C# class structure to mimic WiX entities and their relationships to produce a valid deployment model.

WixSharp answers many MSI authoring challenges. It solves the common MSI/WiX authoring limitations in a very elegant yet unorthodox way. WixSharp follows the steps of other 
[transcompilers](http://en.wikipedia.org/wiki/Source-to-source_compiler) like Script#, CoffeeScript or GWT by using source code of a more manageable syntax (C# in this case) to produce 
the desired source code of a less manageable syntax (WiX). A "more manageable syntax" in this context means less verbose and more readable code, better compile-time error checking and 
availability of more advanced tools.

WixSharp also removes the necessity to develop MSI sub-modules (Custom Actions) in a completely different language (e.g. C++) by allowing both the components and behaviour to be defined in the 
same language (C#). This also allows for a homogeneous, simplified, and more consistent source code structure.

**_Overview_**

_NOTE: WixSharp releases come in two streams: Releases v1.* use the WiX3 toolset to author msi setups, and v2.* use WiX4+. For the time being, the two streams will be maintained in parallel, but when WiX4/5 becomes mature enough, the WiX3 stream will be obsolete._

If you use WiX4+ stream, you need to install .NET SDK (not .NET Framework SDK) installed in your build environment. Visual Studio 2022 comes with .NET SDK already. The need for .NET SDK is dictated by the method WiX vendor distributes WiX compiler and its dependencies (via `dotnet tool`). WixSharp fully adheres to this approach. Even though it provides a workaround for the absence of .NET SDK.

If you are planning to use WixSharp on Linux, you may find this [article](https://github.com/oleg-shilo/wixsharp/wiki/WixSharp-on-Linux) useful. Please note that WixSharp builds MSI deployment packages and while MSI can be built on Linux it cannot be run on Linux as MSI is a pure Windows technology.   

Please note that WixSharp NuGet packages (for both WiX3 and WiX4) are targeting .NET Framework only. This is because WiX does not support integration with any other .NET flavours but .NET Framework only.

You can find the instructions on how to author MSI setups with WixSharp in the [Documentation](https://github.com/oleg-shilo/wixsharp/wiki) section. And this section only highlights 
some of the available features.

> _If you prefer a manual approach you can use the Visual Studio console application project and NuGet package as the starting point._
![image](https://github.com/oleg-shilo/wixsharp/raw/master/Documentation/wiki_images/nuget.png) <br>
_However a simpler approach is to use Visual Studio WixSharp Project Template [extension](https://marketplace.visualstudio.com/items?itemName=OlegShilo.WixSharpProjectTemplates). Read more 
about the WixSharp VS templates [here](https://github.com/oleg-shilo/wixsharp/wiki/VS2019-%E2%80%93-2022-Templates)._

WixSharp allows a very simple and expressive definition of deployment. This is an example of a simple WixSharp script:
```C#
using System;
using WixSharp;
 
class Script
{
    static public void Main(string[] args)
    {
        var project = new Project("MyProduct",
                          new Dir(@"%ProgramFiles%\My Company\My Product",
                              new File(@"Files\Docs\Manual.txt"),
                              new File(@"Files\Bin\MyApp.exe")));
 
        project.GUID = new Guid("6f330b47-2577-43ad-9095-1861ba25889b");
 
        Compiler.BuildMsi(project);
    }
}
```
One of the most intriguing features of WixSharp is the ability to define/implement managed Custom Actions directly in the script file:
```C#
using System;
using System.Windows.Forms;
using WixSharp;
using Microsoft.Deployment.WindowsInstaller;
 
class Script
{
    static public void Main(string[] args)
    {
        var project = new Project("CustomActionTest",
                          new Dir(@"%ProgramFiles%\My Company\My Product",
                              new DirFiles(@"Release\Bin\*.*")),
                          new ManagedAction(CustomActions.MyAction));
 
        BuildMsi(project);
    }
}
 
public class CustomActions
{
    [CustomAction]
    public static ActionResult MyAction(Session session)
    {
        MessageBox.Show("Hello World!", "Embedded Managed CA");
        session.Log("Begin MyAction Hello World");
 
        return ActionResult.Success;
    }
}
``` 

Another important feature is the support for custom UI including WPF external UI:
![image](https://github.com/oleg-shilo/wixsharp/raw/master/Documentation/wiki_images/wpf_ui.png)

The [Samples Folder](https://github.com/oleg-shilo/wixsharp/tree/master/Source/src/WixSharp.Samples/Wix%23%20Samples) an extensive collection of WixSharp samples covering the following development scenarios:

* Visual Studio integration including [NuGet](https://www.nuget.org/packages/WixSharp/) packages and VS2013/2015 [project templates extension](https://visualstudiogallery.msdn.microsoft.com/4e093ce7-be66-40ed-ab16-61a1186c530e)
* Installing file(s) into Program Files directory
* Changing installation directory
* Installing shortcuts to installed files
* Conditional installations
* Installing Windows service
* Installing IIS Web site
* Modifying app config file as a post-install action (Deferred actions)
* Installing "Uninstall Product" shortcut into "Program Menu" directory
* Installing registry key
* Showing custom licence file during the installation
* Launching installed application after/during the installation with Custom Action
* Executing VBScript Custom Action
* Executing Managed (C#) Custom Action
* Executing conditional actions
* Targeting x64 OSs  
* Registering assembly in GAC
* File Type registration
* Setting/Reading MSI properties during the installation
* Run setup with no/minimal/full UI
* Localization
* Major Upgrade deployment
* Authoring and using MergeModules
* Pre-install registry search
* Customization of setup dialogs images
* Rebooting OS after the installation
* Building MSI with and without Visual Studio
* Simplified Managed bootstrapper for UI based deployments
* Simple Native bootstrapper
* Custom MSI dialogs
* Custom WinForms dialogs
* Custom external UI
* Console setup application
* WinForm setup application
* WPF setup application
