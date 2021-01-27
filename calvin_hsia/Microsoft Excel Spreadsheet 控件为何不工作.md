### Why doesn't Miscrofost Excel Spreadsheet control work?
时间：2007年4月5日，星期四，下午1点26分
作者：Calvin_Hsia
翻译：xinjie

    我们收到一个BUG报告：
    问题重现：
    打开表单设计器并创建一个新表单。
    在表单中添加 ActiveX 控件：Microsoft Office Spreadsheet 11.0
    实际结果：
    OLE错误编号 0x80040202: Unknown COM status code.
    我们想要的结果：
    在表单中添加一个 Microsoft Office Spreadsheet 11.0 控件。
 
    毫无疑问，我们会遇到这样的问题。
 
    发生什么意外的事情了呢？
    在 VFP 的表单设计器中，Excel spreadsheet 控件被创建时，将会建立一个属性关系的关联。作为这个的一部分，VFP会调用IConnectionPoint::Advise，它会返回一个包含某些值的一个文档。
     Excel 返回 0x80040202 ，也就是说  EVENT_S_NOSUBSCRIBERS 这个返回值并不在这个文档中。当我们试图建立一个属性关系的关联时， Excel 会执行一个 Spreadsheet 事件代码来查询我们添加的对象。它不知道我们是在设计时刻，所以，它就返回一个错误。
    当我们得到这个错误时，也就是说我们创建控件失败。
 
    如果我们改变一下代码来忽略 EVENT_S_NOSUBSCRIBERS，那么我们就可以在表单设计器里得到对象的实例；我们可以在 speadsheet 的一些事件里添加一些代码，使它感觉像是在运行时刻。
 
    在控件的 BeforeKeyDown 事件增加下面的代码：
    ?PROGRAM()
 
    当我们运行表单时，IConnectionPoint::Advise 就会被调用，这时，事件虽然被触发，单我们仍旧可以成功的创建这个对象。
 
    When I hit a keystroke, I see the line being executed

附：作者对于“在控件的 BeforeKeyDown 事件增加下面的代码”的解释：
I’m sorry: I should have been more clear. When I said “I change the code”  I meant that I changed the code for VFP itself where we handle hooking up events for ActiveX controls.
（我很抱歉：我没有表述清楚。当我说“我改变代码”时，它的意思是，我通过更改 VFP 自身的代码，通过钩子，我们来控制 ActiveX 控件）

译者注：此 bug 在 VFP9 SP2 中已经解决。
