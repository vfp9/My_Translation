# Vista's Aero Glass for Visual FoxPro

翻译：xinjie

## Aero Glass 效果

当透明表单刚刚出现时，就已经有了一些关于它的一些技术，它是来自 Windows Vista 的具有强烈震撼的 Aero Glass 效果。我的看法是，Aero Glass 效果是一个决定性的要素，它标志着一个应用程序可以运行于 Windows Vista 。所以，一些开发者针对他们的应用程序找到一些方式使它看上去非常像 Aero Glass 效果，这并不是特别能认人感到惊喜。Bernard Bout 在他的博客中提供了一些非常好的示例。它需要开发时有好的视力，可以从 [Cesar Chalom](http://weblogs.foxite.com/cesarchalom/) 和 GDI+ 可以得到一些帮助，它是 Windows Vista 的 Aero Glass 入门指南，Bernard 已经给我留下了印象（随后我也说出了我的看法）。请参看下面的他的博客连接来查看我讨论过什么。

- [在 Visual Foxpro 的图象中应用 Alpha 滤镜](https://github.com/vfp9/My_Translation/blob/main/bernardbout/%E5%9C%A8%20Visual%20Foxpro%20%E7%9A%84%E5%9B%BE%E8%B1%A1%E4%B8%AD%E5%BA%94%E7%94%A8%20Alpha%20%E6%BB%A4%E9%95%9C.md)

- [在 Visual FoxPro 中显示透明表单](https://github.com/vfp9/My_Translation/blob/main/bernardbout/%E5%9C%A8%20Visual%20FoxPro%20%E4%B8%AD%E6%98%BE%E7%A4%BA%E9%80%8F%E6%98%8E%E8%A1%A8%E5%8D%95.md)

- [非常非常酷的 VFP](http://weblogs.foxite.com/bernardbout/archive/2006/08/16/2261.aspx)

- [使用 Visual Foxpro 来做 Glass!](http://weblogs.foxite.com/bernardbout/archive/2006/06/15/1838.aspx)

## Visual FoxPro 怎样运行于 Vista

创建一个具有 Aero Glass 效果的 Visual FoxPro 应用程序非常容易。顶层表单（ShowWindow = 2）可以自动适应（标题栏、控件和边框）Aero Glass 。这是一件好事，Visual FoxPro 不需要改变就可以在微软提供的操作系统中得到最新最好的视觉效果（还记得在 VFP 中增加的主题支持吗？）。替代方案并不是真实的，这种方案才是真实的（参看下面的截图，一个 ShowWindow = 0 或 1 的 VFP 表单）。

<img src="http://www.sweetpotatosoftware.com/SPSBlog/content/binary/VFPInScreen.jpg">

## 在开始之前需要了解的

我知道你在读这些内容时在想什么：“Craig, 你在这里讲的 Windows Vista 中实现 Aero Glass 效果和其他已经展示的方法相比，没什么新的东西！”。 然而，你大概还没有看到过在客户区域（表面的区域）显示出 Aero Glass 效果的 VFP 表单（我不相信你曾经看到过），这就是我要讨论的东西。下面的截图是一个例子，也就是我要讨论的东西。

<img src="http://www.sweetpotatosoftware.com/SPSBlog/content/binary/VFPAeroGlass5.jpg">

## 它是如何做到的？

为了实现它，我使用了来自 Windows Vista 中 [Desktop Window Manager API](http://blogs.msdn.com/greg_schechter/archive/2006/09/14/753605.aspx) (dwmapi.dll) 的函数  DwmExtendFrameIntoClientArea 。在 MSDN 中该函数是这样声明的：
```foxpro
HRESULT DwmExtendFrameIntoClientArea(
HWND hWnd,
const MARGINS *pMarInset
);
```
并且，MARGINS 结构(参看上面代码的 pMarInset 参数)是这样定义的：
```foxpro
typedef struct _MARGINS{
int cxLeftWidth;
int cxRightWidth;
int cyTopHeight;
int cyBottomHeight;
} MARGINS, *PMARGINS
```
我将它转换成下面的声明格式，并且将代码增加到运行于 Windows Vista 的 Visual FoxPro 9.0 的顶层表单的 Init() 事件中：
```foxpro
DECLARE LONG DwmExtendFrameIntoClientArea IN dwmapi.dll Long hwnd, string @ pMarInset

LOCAL lnHwnd, lcMargin, lnGlassLeft, lnGlassRight, lnGlassTop, lnGlassBottom

m.lnHwnd = this.HWnd
m.lnGlassLeft = 50
m.lnGlassRight = 50
m.lnGlassTop = 50
m.lnGlassBottom = 50
m.lcMargin = BINTOC(m.lnGlassLeft, '4RS') ;
            + BINTOC(m.lnGlassRight, '4RS') ;
            + BINTOC(m.lnGlassTop, '4RS') ;
            + BINTOC(m.lnGlassBottom, '4RS')

DwmExtendFrameIntoClientArea(m.lnHwnd, @m.lcMargin)
```
这段代码在我的顶层表单中并没有显示出任何效果，我知道我遇到了一个难题。答案来自 [article out on Code Project](http://www.codeproject.com/winfx/VGGlassIntro.asp) 。它指出，DwmExtendFrameIntoClientArea 函数的工作需要将作为神奇的 Aero Glass 效果的像素设置它们自己的 [Alpha channel](http://www.webopedia.com/TERM/A/alpha_channel.html) ，且设置为 0 。并且，文章指出，最容易的方式就是将这些像素设置为黑色。

所以，相比于绑定 windows 消息，我选择使用 GDI+ 来实现它（我确信可以），在表单中使用一些黑色的 VFP shapes 。我在表单中放置了 4 个 shape ，并且设置了它们的宽高（依赖于它们所在的位置），以匹配我已经赋值的 m.lnGlassLeft、m.lnGlassRight、m.lnGlassTop 和 m.lnGlassBottom (参看上面的代码，我伪造了一个 MARGINS 结构)。设计结束的表单，在设计器中就是下面截图这个样子。并且，当我运行它时，我得到了一个在客户区域具有 Aero Glass 效果的 VFP 表单！

<img src="http://www.sweetpotatosoftware.com/SPSBlog/content/binary/VFPAeroGlass4.jpg">

## 这样做的好处是什么？

使用这个方法已经设计出了很多伟大的 UI 示例。如果你在 [Code Project article](http://www.codeproject.com/winfx/VGGlassIntro.asp) 查找的话，可以找到很多。简而言之，针对 Visual FoxPro 应用程序，用它来创建 UI 的结构是一个好方法。

一个很好的应用就是创建一个顶层表单，它具有状态栏，其中包含一个进度条和其他一些控件。下面的图片是一个简单的示例，展示了它的效果。我使用了和上面同样的代码，仅仅更改参数  m.lnGlassBottom 为 50 (其他都设置为  0) ，且底部的 shape 的背景色设置为黑色。

<img src="http://www.sweetpotatosoftware.com/SPSBlog/content/binary/VFPAeroGlass3.jpg">

## 还需要做更多的工作

这个概念的证明并没有完成，我们需要放置在客户区域的任何 VFP 类/控件 都可以使用 Aero Glass 效果，但是存在一些问题。这是因为，你可以这样假设，任何的东西都是黑色的（像前景色和背景色），都可以转换成半透明。围绕这个有几种方法来使用 ActiveX 控件/Windows 类 ，就像我在表单上使用  Microsoft ProgressBar Control（5.0 sp2）。另一些方法就是使用图形元素和使用 GDI+ 的文本。在这方面，来自 Visual FoxPro 9.0 中的 _gdiplus.vcx 和 [VFPX](https://vfpx.github.io/) 中 [GDIPlusX project](https://github.com/VFPX/GDIPlusX) 可以很好的处理它。无论如何，我们知道在 Visual FoxPro 9 中很容易的实现 Aero Glass 效果是很好的消息。我对找到如此简单的实现方法既感到高兴，又有些惊讶。

一直到下次... Visual FoxPro 前进!
