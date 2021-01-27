# Do you like reading a blog author? Retrieve all blog entries locally for reading/searching using XML, XSLT, XPATH
作者：Calvin_Hsia

时间：2005年1月12日，星期三，1:36 PM

翻译：xinjie 
 
如果你喜欢读一个博客并且想阅读同一作者更多的文章，你可以使用 RSS 来订阅博客，阅读博客中更多的内容，例如  [newsgator](http://www.newsgator.com/ngs/default.aspx) 和 [intravnews](http://www.intravnews.com/)。这样，你会得到一些日常记录（RSS 只能 [显示15篇](http://www.webreference.com/authoring/languages/xml/rss/intro/)） 和任何后来的内容，但是，你如何检索博客所有的档案内容呢？

 一些站点允许你搜索博客，例如 [Techonorati](http://www.technorati.com/) 和 [feedster](http://www.feedster.com/) 。如果你是博客的作者，你可以 [增加一个搜索按钮](http://weblogs.asp.net/cumpsd/archive/2005/01/11/350909.aspx) （就像这个页面中的那样），这样，就可以使用 Google 搜索你自己的博客（但这个搜索引擎或许无法搜索到你最新的作品）。

我些了一个小程序，用来从作者的博客中得到其所有内容，并且将结果存入一个表，这个表存储 HTML 和内容的摘要。

这样，查询一个表就可以像这样写一个很简单的 [SQL Select 语句](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dv_foxhelp/html/lngselect___sql.asp) 。

```foxpro
SELECT * FROM blog where ATC("CallWindowProc",blograw)<0
```
 
你可以编辑它的代码来读取/订阅你自己的博客。所加入的代码 [从 RSS 的目录中](http://blogs.msdn.com/calvin_hsia/archive/2005/01/11/350907.aspx)周期性的检索任何新的内容。你可以使用许多方法来写一个博客的阅读器，例如使用 [IE Web 控件](http://blogs.msdn.com/calvin_hsia/archive/2004/08/20/217915.aspx) 或者使用 Outlook、InternetExplorer 或者 MSWord 。

从非博客源检索到的内容还可以编辑：在线专栏作家的同类文章。

 
### 代码是如何工作的：

这里调用一个 GetHTML ，它使用 [WinHTTP](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/winhttp/http/winhttp_start_page.asp) 对象来检索任意 URL 的 HTML 。它也被用来检索主要博客页面的 HTML ，它包含一个档案列表。

然后，它分析 HTML 将档案的超级链接存入一个表。用这些链接查询 HTML 时，首先使用 [MSWord 的 SaveAs 方法（需要 wdFormatXML 参数）](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/vbawd11/html/womthsaveas1.asp)转换 HTML 为 XML 。然后使用 [XMLDOM](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/xmlsdk/html/DOM_DevGuide_Overview.asp)  的 [selectSingleNode](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/xmlsdk/html/xmmthselectsinglenode.asp) 方法，它使用 [xpath](http://www.w3.org/TR/xpath) 查询语言来找到档案部分。

针对每个月的链接，用同样的方法获得 HTML 和使用一个 XML 查询来找到超级链接和每篇博客内容的 HTML ，它们被存储到一个表中。

最后，遍历表并再次使用 MSWord 转换 HTML 为明文文件，这样，就获得了原始的文本内容。

这个明文文本针对搜索是非常有用的：通常在 HTML 中有一些标记和脚本代码是你不想搜索的。

已经写出的代码需要在  blogs.msdn.com 或 http://weblogs.asp.net  解析，注意里面有微小的错误。对于其他的博客，你可以编辑 XML 查询。例如，它或许不存在档案或者档案并不是按月来组织。

[这里可以看到示例代码。](http://www.calvinhsia.com/default.asp?page=link&file=vfp/blogreader.htm)
