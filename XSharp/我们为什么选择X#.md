地址：https://www.xsharp.info/itm-help/why-xsharp

翻译：xinjie    2020.06.14
***
## 历史：
自上世纪八十年代以来，在 PC 平台上，xBase 一直是一种非常流行的开发语言。其起源于 CPM 的 dBase-II ，紧接着是 DOS 平台下的 dBase-III。

dBase 的流行受到了其他公司的欢迎。很快，诸如Clipper，Foxbase，QuickSilver等产品进入了历史舞台。

随着 Windows 操作系统的出现，我们需要这些这些产品的 Win 版本。Clipper 的所有者 Nantucket 开发了一种名为  Visual Objects (VO) 的语言，Computer Associates 购买了它。
在 WIN 平台下，也出现了其他的产品，例如  FoxPro, dBase, Xbase++, FlagShip 和 Harbour （最后这两个产品，不仅仅可以在 WIN 平台使用，而且，可以在 Linux 和 UNIX 平台使用）。

在 Computer Associates 对 xBase 下的 Clipper 和 VO 失去兴趣之后，GrafX Software 在 2002 年购买了这些产品的营销和开发权。GrafX 并开发了名为 Vulcan.NET 的后继产品。它致力于将 VO 延申至 .NET 平台。


对于微软来说，它已经“放弃”了 Visual FoxPro。


如果你想要详细了解 xBase 语言的历史，可以参阅[维基百科](https://en.wikipedia.org/wiki/XBase) 。

一直到 2015 年，仍然存在着几种 xBase 英语。但是对于 .NET 框架而言，仅有一种语言可以在其上运行，那就是 Grafx 的 Vulcan.NET。


不幸的是，GrafX 从来没有很好地推销其产品，并且市场份额正在缩小。 GrafX 开发团队在过去几年中也有所缩减。


## XSharp
2015 年 4 月，一些客户和 Grafx 开发团队的成员讨论开始一个新的开源项目。以便在 .NET 平台下继续延续 xBase 语言的生命。该计划被命名为 XSharp。在某些方面，它是受到以下事实的启发：Microsoft已发布了在开源许可证（.NET编译器平台“ Roslyn”）下的 C# 和 Visual Basic 编译器的源代码。 该计划的目标是创建一种新的开发语言（编译器，运行时库，IDE，工具），其中编译器部分基于Roslyn源代码。


来自荷兰的独立软件开发人员，Visual Objects 和 Vulcan.NET 开发团队的前成员，Visual Objects 和 Vulcan.NET 的多个第三方产品的作者 Robert van der Hulst 自愿创建了一家新公司 XSharp BV。 该公司是 XSharp 项目背后的法人实体。 他得到了 xBase 社区几位成员的支持，但是这几位成员暂时保持匿名。



该项目的目的是为 .NET 平台创建 xBase 语言的开源版本。
由于存在许多xBase方言，因此团队将开发具有不同语言“特色”的编译器，例如
- Core
- Visual Objects compatibility
- Vulcan.NET compatibility
- Xbase++ compatibility
- FoxPro compatibility
- (x)Harbour compatibility
- Etc.


Core 语言是 Microsoft C＃编译器的 xBase 语言版本。 它具有与 C＃6 相同的功能，理所当然，它会使用众所周知的 xBase 语法。基于这种 Core 语言编译器，项目创建了不同的“风味”，并尽力支持每种“方言”下的数据类型，类和对象。
Core 语言不仅可以生成在 Windows 下运行的 .NET 程序集，而且还可以生成在其他平台上运行的“通用应用程序”。 还计划提供.Net本机支持。

目前，Core，VO和Vulcan方言已经“就绪”，我们正在努力完成 Xbase ++ 和 FoxPro 方言。
(译者注：“方言”，指 xBase 体系下的具体的某一种开发语言。)
