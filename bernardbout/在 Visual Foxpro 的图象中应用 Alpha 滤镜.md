# Using the Alpha Channel in Visual Foxpro Images 

翻译：xinjie

在 PNG 图象中应用 Alpha 滤镜可以做出一些非常有趣和令人喜爱的效果。

我以前在一些伪表单上对 PNG 图象使用 Alpha 滤镜获得了非常可喜的成果。我不知道在VFP是否可以控制 PNG 的 Alpha 滤镜，所以，我决定试一试。

##### 2006年9月12日更新

我在 Paint Shop Pro 中创建了一些图象，当然，你可以使用其他的图象工具，像 Photoshop 等等这些软件。当我在 VFP 的 image 控件中载入这些图象时，获得了非常有趣的效果，所以我决定花一些时间来探索这个特性并想出下面的一些屏幕效果。Titlebar.png 用于标题栏， mainform.png 用于主表单区域。我增加了  Vistabuttons.bmp 并在它上面放置了不可见的命令按钮用来进行操作。所有这些都放置在 VFP 中包含 image 的透明容器中，并且对于其他的对象，像 labels、textboxes 等等都置于顶层。可以通过使用 ADDOBJECT() 将容器添加到表单中。下面的图片可以通过右键-图片另存为...  保存到你的硬盘上。

Titlebar.png

![](Titlebar.png)

Mainform.png

<img src="../../Picture/Mainform.png">

VistaButtons.bmp

<img src="../../Picture/VistaButtons.png">

这里的“表单”类实际是一个包含所有对象的透明容器。它可以像任何其他类一样实例化为一个“表单”。

这里“表单”只所以加引号，是因为它并不是一个真正的表单，不具有表单所具有的方法、事件等。它只是看上去像表单，并且可以在一个 SDI（顶层表单）表单中实例化。它可以实现任何的数据录入甚至显示消息框。

下面是一个具有不同对象的表单。“表单”居然可以像一个常规表单那样移动。你可以很容易的看到所获得的透明效果，它有标题栏和边框，但中心区域并不透明。在 SDI 表单右边，是一些常规的 VFP 对象，这是用来做比较的。注意，标题栏的边界和“表单”的边界是可见的。

***单击图片看大图***

<img src="../../Picture/form1.png">

下一副展示了移动后的“表单”，它有部分区域盖住了右侧的对象。透过“表单”我们可以清晰的看到那些对象的文本。

<img src="../../Picture/form2.png">

最后一张展示了最终效果，表单主要的部分和对象是不透明的。

<img src="../../Picture/form3.png">

完成这个效果需要大约10分钟。时间很短，现在你知道如何使用滤镜并应用它达到这个效果了吧。在 XP 系统下，使用很少的代码就可以换来很现代的视觉外观： Vista Aero Glass 。

现在，这些已经被我的朋友 [Cesar Chalom](http://weblogs.foxite.com/cesarchalom/default.aspx) 所遗弃，他使用 GDI+ 来达到这个目的......

**Fox 是不是很酷？**


## 2006年9月12日更新
Cesar 非常痛快的提供了在运行时生成 PNG 图像的代码，你可以在这里得到它：

http://weblogs.foxite.com/cesarchalom/archive/2006/09/12/2445.aspx

下面这行代码可以改变图像的尺寸。这里是 300w X 270H
```foxpro
loBitmap = .Bitmap.New(300,270)
```

中心不透明的区域颜色可以在下面的代码中进行更改：
```foxpro
* 绘制一个黄色的矩形
loGfx.FillRectangle(.SolidBrush.New(.Color.FromRGB(254,254,228)), 10,9,278,249)
```
***切记，要改变最后两个值来适应你定义的图片尺寸*** - ,278,249)

Cesar 的代码不仅仅是可以做一个 绿色透明的 PNG 。我想，可以通过下面这句代码，更改最后三个表示 RGB 的数字来做到。
```foxpro
* 使所有的颜色都变成透明的
loGfx.Clear(.Color.FromARGB(0,0,0,0))
```
但是我已经留给他处理了。

最后设置  **image.BorderStyle = 1-Fixed Single**。这样，我们就可以得到一个带有边框的“表单”。这正是我们所需要的。

***感谢 Cesar 。你是真正的  GDI+ 专家。***

发表于2006年9月11日，星期一，12:55 AM 
作者： bbout  
