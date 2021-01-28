来自：http://weblogs.foxite.com/bernardbout/archive/2006/05/23/1568.aspx

翻译：xinjie
 
在上一篇中我已经解决了滚动条的问题(译者注：代码包含在此部分)，并且我准备探索新的领域。但是我的朋友 Malcolm 却不打算我就此结束。

**你的滚动条类如何来解决下面这些复杂的问题：**

_1. 当可滚动容器（窗口 和/或 窗口上的滚动条）获得焦点时，父表单的 Deactivate/Active 事件会被触发。_
_2. 如何使可滚动区域也可以使用 Anchor 属性工作，以便于在父表单调整尺寸时，它也可以自动的移动位置并调整自身的尺寸。_

    他用这些问题来攻击我。

    当然，表单并不具有 Anchor 属性，但这并不能难住我们。

    尽管我不能达到全部需求，但我还是有一个解决方案。还记得不可见的占位符吗？它仍旧在表单中，并且仍旧是一个VFP可认识的对象，即使它的是不可见的（VISIBLE = .F.）。

    所以，可以打开表单，设置占位符的 Anchor 属性（我希望你使用的是 VFP9）：

    Anchor = 15

    还需要做一些其他的事情。绑定子表单到占位符的 Anchor 。要达到目的，我们需要一个对象引用。在父表单上创建一个名为 oChild 的自定义属性。在原有的 INIT 事件代码中增加一行，结果像下面这样：

DO FORM scroller NOSHOW

SHOW WUNDOW scroller IN WINDOW scrollparent

With Scroller

   .Top = ThisForm.Placeholder.Top

   .Left = ThisForm.Placeholder.Left

   .Height = ThisForm.Placeholder.Height

   .Width = ThisForm.Placeholder.Width

ENDWITH

ThisForm.oChild = scroller

    最后，在父表单的 RESIZE 事件中添加这样的代码：

WITH ThisForm.oChild

   .Top = ThisForm.Placeholder.Top

   .Left = ThisForm.Placeholder.Left

   .Height = ThisForm.Placeholder.Height

   .Width = ThisForm.Placeholder.Width

ENDWITH

    这样，就使子表单也具有了 Anchor = 15 这样的属性设置。

    下一步就是在 Scroll 类中增加些代码。这是很微不足道的事。将上面的 RESIZE 事件代码移动到子表单的一个自定义方法中，并且增加代码，基于上面的自定义属性绑定父表单和子表单的 RESIZE 事件。所有的对象就都具有了 anchor = 15 的设置，所以，如果你像控制所有其他的 Anchor 设置，可以使用一个 CASE 选择语句来达到目的。

    我还没有解决第1个问题，但那也不过是时间问题。

    是 Malcolm 提出来的。 

    以后再说......

    发表于 2006年5月23日，星期二，10:20 AM
    作者：bbout 
