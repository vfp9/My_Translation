# Visual FoxPro 中的 XAML

来自：https://www.sweetpotatosoftware.com/blog/index.php/2007/06/11/xaml-in-visual-foxpro/

翻译：xinjie

### 什么是 XAML
如果你还没听说过 XAML，那么你就太孤陋寡闻了。这篇文章假设读者至少对 XAML 有一个模糊的了解，以及意识到它对未来的潜在影响。如果你对 XAML 不感兴趣，那么你可以通过搜索引擎，或者访问一下
像 XAML.NET 这样的网站，或者从 horse’s mouth（译者注：找不到恰当的翻译，使用原文词汇） 得到一些关于 XAML 的信息。无论如何，如果你想深入理解 XAML 的方方面面，那么这篇博文并不适合你。

### XAML Visual FoxPro Style
大约在9-10个月前，我创建了一个 .NET ActiveX 控件，它允许任何可以使用 COM 的语言（例如 Visual Foxpro）用它来加载 XAML 控件。在控件内部，它使用 ElemetHost类(如System.Windows.Forms.Integration.ElementHost)
提供一个 UserControl(如System.Windows.Controls.UserControl)驻留在一个 UserControl(如System.Windows.Forms.UserControl)中。很绕是吗？无论如何，它基本上允许开发者在 XAML 不一定要运行的
地方运行它（例如，把 XAML 控件放置在 Visual FoxPro 表单上）。

关于如何创建可以在 Visual FoxPro 中使用的 .Net ActiveX 控件，请参阅我之前的博文。这篇文章既不会解释 XAML，也不会教你如何制作一个 .Net ActiveX 控件。那我要做什么呢？我会让你看到一些 XAML 
在 Visual FoxPro 表单中运行的屏幕截图，而且会提供一个 ActiveX 控件和它的 C# 源码，以及一个 Visual Foxpro 的 Demo(屏幕截图的来源)，你需要做的仅仅是保证你的 OS 是 Windows XP SP2 或更高
版本，并且已经安装了 .NET FrameWork 3.0(以前称为 WinFX)，这样你就可以运行 VFP Demo 了，也许还会给你的朋友或同事留下深刻的印象。不错的交易，是吗？

### Visual FoxPro XAML Example
在 VFP Demo 中包含了一个表单，其中包含四个在 Visual Foxpro 中运行 XAML 的例子。这个 Demo 不仅向你展示了在 Visual Foxpro 中加载 XAML 控件的一种方法，而且允许你动态的改变 XAML 控件（删除、添加
新控件等等）。XAML 可以在表单运行时修改，并通过提供的按钮重新加载。我也很容易的在代码中完成这些工作，或者通过从表单中提取 XAML 或其他什么--毕竟 XAM 只是 XML。

下面是在 Windows XP SP2 和 Visual FoxPro 9.0 上运行的四个例子的屏幕截图。它可以在更早版本的 Visual Foxpro 上运行吗？我希望问这个问题的人尽快升级到 Visual Foxpro 9.0。尽管任何可以使用 ActiveX
控件的 Visual Foxpro 版本都可以使用它。

### Example 1
![](http://www.sweetpotatosoftware.com/SPSBlog/content/binary/vfpxaml0000.gif)
### Example 2
![](http://www.sweetpotatosoftware.com/SPSBlog/content/binary/vfpxaml0001.gif)
### Example 3
![](http://www.sweetpotatosoftware.com/SPSBlog/content/binary/vfpxaml0002.gif)
### Example 4
![](http://www.sweetpotatosoftware.com/SPSBlog/content/binary/vfpxaml0003.gif)

### 哪部分是 XAML ？
在标题"Enter XAML UserControl below"上面的内容，就是XAML。下面的一切，包括标签，都是 Visual FoxPro 控件。现在，请注意，当你第一次点击加载XAML按钮时，会有一个相当明显的延迟。然后，例子就会
很好地出现。最初延迟是由于将必要的3.0. NET FrameWork 加载到内存中的开销造成的。此后，任何延迟都是由于使这个例子成为可能所必需的抽象层所造成的。

如果这些 XAML 例子看起来很熟悉，那么你要么参加了 Southwest Fox 2006 的主题演讲，要么是在其他网站上看到的，因为我不是这些例子中所包含的 XAML 的原作者。是的，在表单的代码注释中，已经给出了原作者
适当的信息和相关链接（如果知道的话）。

### 如何启动和运行这个例子

当你解压缩后，你会看到两个文件夹。VFPXAML 和 VFP Example。前者包含压缩包根目录中提供的 C# 解决方案的代码--这就是ActiveX控件。后者包含一个名为 "Form1 " 的 Visual FoxPro 表单，当你安装了最新
的3.0 .NET FrameWork 并注册了 ActiveX 控件（...\VFPXAML\VFPXAML\bin\Release\VFPXAML.dll），你就可以运行这个表单。要注册ActiveX控件，只需使用 .NET 提供的 RegAsm.exe
（位置：C:\WINDOWS\Microsoft.NET\Framework\v2.0.50727\）。你想在DOS中执行的命令看起来有点像这样......

C:\WINDOWS\Microsoft.NET\Framework\v2.0.50727\RegAsm.exe /codebase C:\MyPath\VFPXAML\VFPXAML\bin\Release\VFPXAML.dll

关于RegAsm.exe的更多信息可以在微软网站上找到，对于那些不熟悉它的人。当涉及到 .NET 时，就把它看作是 Resvr32 的等价物。

接下来，在 Visual FoxPro 中运行前面提到的 form1，通过表单左下方提供的选项组选择你想要的例子，准备好后点击 Load XAML 来运行该例子。正如我之前所说，XAML 是可以改变的。只需在提供的编辑框中
修改 XAML，然后重新点击载入 XAML。几乎所有可以作为 XAML 用户控件的东西都可以使用。乱搞一下，看看你能想出什么。如果你创造了一个非常酷的XAML例子，那就把它写进博客，与 Visual FoxPro 社区分享。
如果没有其他原因，请把它发给我，我会把它写进博客。

### 它可以直接用于生产环境吗？

几乎不可能！它只是一个简单的原型，我把它弄到一起，并且承认它在过去的 9 个月里没有被更改过。我只是想展示一种可能性，希望能让微软在 Sedna 的 XAML 项目上签字。归根结底，它的目的是用来展示开发人员
可以通过使用支持 COM 但不能直接使用 .NET 的语言来使用新技术的一种方法。当然还有其他方法也可以做同样的事情。一种方法是放弃 Visual FoxPro 表单，而在 .NET 的 WinForm 上完成整个事情，这种方法的
开销较小。WinForm 可以由 Visual FoxPro 通过 COM 互操作来控制，并且可以创建接口来让你访问 WinForm 的属性、事件和方法。

有没有一种更简单的方法来制作 .NET 中的互操作控件和窗体，以便在 Visual FoxPro 中使用呢？当然有，你可以利用微软的 Interop Forms Toolkit 2.0 来代替自己编写所有繁琐的互操作性代码。还应该指出的是，
当涉及到互操作性时，VB.NET 比 C# 有一些优势（尽管 VB.NET 不会给你的朋友和同事留下太多印象）。其中最主要的一点是，C# 无法处理可选参数，而 VB.NET 却能轻松地处理它们。COM和重载函数不能很好地混合。

在我离开之前，我希望你能注意到最后一件事。如果你重建下载的 .NET 解决方案，你会看到一些关于 "Non COM Visible value types "的编译器警告。不要担心这些，它们不是什么大问题。我只是太懒了，没有编写
必要的 ComVisible 属性来摆脱它们。

### 下载它，祝你玩儿的开心！
[Download VFP XAML Solution and Example (approx. size 1.4MB)](http://www.sweetpotatosoftware.com/files/XAML4VFP.zip)
