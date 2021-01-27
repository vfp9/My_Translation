压缩文件: VFPToolkitNET.ZIP
程序: Visual FoxPro Toolkit for .NET
作者: Kamal Patel (kppatel@yahoo.com)
版权: 无(公用)
日期: 2002年4月24日
Version 1.00
Readme.txt 文件翻译：xinjie
 
所有源代码和包含在 Visual FoxPro Toolkit for .NET (VFPToolkitNET.ZIP) 中的文件都是公共文件。你可以使用、编辑、拷贝和分发，并且，源代码、示例程序或包含在 VFPToolkitNET.ZIP 中的文件是自由软件，并无版权保护。所有包含在 VFPToolkitNET.ZIP 中的文件都不提供责任担保。作者、捐献者或发行者不会承担任何责任。
关于 VFP Toolkit for .NET 的问题和讨论，都在 UT (http://www.universalthread.com) 的 .NET 范围，它被称为 Visual FoxPro Toolkit for .NET 。请在 UT 上发表问题、建议或需要改进的等等内容。
 
Visual FoxPro Toolkit for .NET 开发小组:
    - Kamal Patel (Lead Developer, Creator)
    - Cathi Gero (Program Manager)
    - Rick Hodder (Help)
    - Nancy Folsom (Reviewer, Evangelist)
    - Ken Levy (Advisor, Communications)
安装和使用 VFP Toolkit for .NET
---------------------------------------------
Visual FoxPro Toolkit for .NET ("Toolkit") 是一个类库，它可以在 Visual Basic .NET 或 C# .NET 中执行 Visual FoxPro 中的函数。为了在 Visual Studio .NET 项目中使用它，你必须首先要安装 Toolkit 并将其增加你的 .NET 项目引用中。

安装 VFP Toolkit for .NET
-----------------------------------
1. 解压包含在压缩文件中的文件到 C:\VFPToolkitNET 目录。
2. 在资源管理器中，双击执行 Setup.BAT 文件。

在你的 Visual Studio .NET 项目中增加一个到 VFPToolkitNET 集合的引用
--------------------------------------------------------------------------------
1. 在 Visual Studio .NET 中创建一个新项目。
2. 在解决方案资源管理器中，右击“引用”并选择“增加引用”。
3. 在 .Net 标签下，在列表中选择“Visual FoxPro Toolkit for .NET”并双击它。然后单击“选择”按钮。如果你在列表中没有看到这个文件，那么九单击“浏览”按纽，并定位到 VFPToolkitNET.dll 文件所在目录 C:\VFPToolkitNET 。
4. 单击“确定”按纽。这将会使 Toolkit 的引用增加到你的项目中。
Toolkit 是一个对应于 FoxPro 函数的类库。如何在不同的 .NET 语言中使用它呢？
  
   在 Visual Basic .NET 中
   -------------------- 
   1. 在解决方案资源管理器中，右击项目并选择“属性”。
   2. 在左侧列表中选择“公共属性”(Select Common Properties) / （导入）。
   3. 在命名空间文本框中，键入下列行。在每键入一行后，单击 Add Import 按纽：
  VFPToolkit.arrays
  VFPToolkit.dates
  VFPToolkit.dialogs
  VFPToolkit.common
  VFPToolkit.environment
  VFPToolkit.files
  VFPToolkit.help
   VFPToolkit.math
  VFPToolkit.strings
  VFPToolkit.vfpData
  
   4. 单击“确定”按纽保存更改。
  
   另一种可选方法是，你可以粘贴下面的代码到你的类模块的头部(揗odule1.vb,?例如):
 
  Imports VFPToolkit.arrays
  Imports VFPToolkit.dates
  Imports VFPToolkit.dialogs
  Imports VFPToolkit.common
  Imports VFPToolkit.environment
  Imports VFPToolkit.files
  Imports VFPToolkit.help
  Imports VFPToolkit.math
  Imports VFPToolkit.strings
  Imports VFPToolkit.vfpData
   5. 和在 Visual FoxPro 中同样的方式来使用命令。
      例如:
  dim lcContents as String
  lcContents = GetFile()

   在 C# .NET 中
   ----------
   1. 在你的类模块中增加下面的 using 语句
      (揅lass1.cs,?例如):
  using VFPToolkit;
      后者，你可以为每一个 Toolkit 类指定一个别名（参看后面的“关于 VFPToolkitNET.dll”）。
  
      例如:
  using VfpDialogs = VFPToolkit.dialogs;
  using VfpArrays = VFPToolkit.arrays;
  using VfpDates = VFPToolkit.dates;
  using VfpDialogs = VFPToolkit.dialogs;
  using VfpCommon = VFPToolkit.common;
  using VfpEnvironment = VFPToolkit.environment;
  using VfpFiles = VFPToolkit.files;
  using VfpHelp = VFPToolkit.help;
  using VfpMath = VFPToolkit.math;
  using VfpStrings = VFPToolkit.strings;
  using VfpData = VFPToolkit.vfpData;
   2. 在 C# 中使用的命令是不同于 VB .NET 或 Visual FoxPro 的。
      使用命名空间、类名和函数名。
      例如:
  string lcContents;
  lcContents = VFPToolkit.dialogs.GetFile();
      或者, 引用类名和函数名。如果没有冲突的命名空间的话。
      例如:
  string lcContents;
  lcContents = dialogs.GetFile();
      混合使用，如果你使用 using 语句指定了一个别名，你可以用下面的缩写方式。
      例如:
  using VfpDialogs = VFPToolkit.dialogs;
  ...
         string lcContents;
  lcContents = VfpDialogs.GetFile();

关于 VFPToolkitNET.dll
---------------------------
VFPToolkitNET.dll 是一个封装了 FoxPro 函数的类库。它们包括：
. arrays（数组）
. dates（数据）
. dialogs（对话框）
. common（公共组件）
. environment（环境）
. files（文件）
. help（帮助）
. math（数学）
. strings（字符串）
. vfpData（VFP数据）
每个类中，都包含有对应于 FoxPro 函数的方法。
 
 
下载地址：http://foxcentral.net/microsoft/VFPToolkitNET.zip
类库的VB.NET版源代码：http://foxcentral.net/microsoft/VFPToolkitNET_VBNET_Source.zip
类库的C#版源代码：http://foxcentral.net/microsoft/VFPToolkitNET_CSharpNET_Source.zip
