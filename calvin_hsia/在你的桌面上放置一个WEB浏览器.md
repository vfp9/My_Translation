# put a web browser on your desktop

作者:Calvin_Hsia

时间:2004年8月20日,星期五,12:54 PM

翻译:xinjie

 
仅仅需要少量的代码，就可以在你的VFP桌面背景上放置一个全功能的自定义WEB浏览器。 

如果你还没有运行 VFP9 的 beta 版，那么就移去下面代码中 textbox 的 autocomplete 属性设置。 

AlwaysOnBottom 用来设置浏览器放置在其他 VFP 窗口下，所以，它并不妨碍你使用 VFP 。 

即使我在其中播放视频，它仍旧会显示在 VFP 的桌面上。

BindEvent 允许当使用者调整 VFP 桌面尺寸时，允许表单使用钩子来调整表单的尺寸。 

(VFP 桌面浏览器自从 VFP5 还是 6 开始，就可以实现了：至少有六年了！）

 附加其他的代码，你就可以自定义浏览器，例如增加特别喜欢的 tabbed 页。 
 
```foxpro
PUBLIC ox
ox=NEWOBJECT("myformx")

BINDEVENT(_screen,"resize",ox,"resize")

ox.show

DEFINE CLASS myformx as Form
      ADD OBJECT txtURL as textbox WITH width=400,value="www.msn.com",autocomplete=3
      ADD OBJECT cmdRefresh as commandbutton WITH caption="\left=400,height=20
      ADD OBJECT cmdBack as commandbutton WITH caption="\left=475,height=20
      ADD OBJECT cmdForward as commandbutton WITH caption="\left=550,height=20

      ADD OBJECT oweb as cweb WITH top=30
      width=800
      height=800
      alwaysonbottom=.t.
      allowoutput=.f.
      titlebar=0

      PROCEDURE init
            this.oweb.width=thisform.Width
            thisform.resize
            this.oweb.height=thisform.height-thisform.txtURL.height-2
            thisform.txtURL.valid

      PROCEDURE resize
            thisform.Width=_screen.Width
            thisform.Height=_screen.height
            this.oweb.top=30
            this.oweb.height=thisform.Height-100
            this.oweb.width=thisform.width-thisform.left

      PROCEDURE txtURL.valid
            thisform.oweb.navigate(this.value)

      PROCEDURE cmdRefresh.click
            thisform.oweb.refresh

      PROCEDURE cmdBack.click
            try
                  thisform.oweb.goback()
            CATCH
            endtry

      PROCEDURE cmdForward.click
            try
                  thisform.oweb.goForward()
            CATCH
            endtry
ENDDEFINE

DEFINE CLASS cweb as olecontrol
      oleclass="shell.explorer.2"
      
      PROCEDURE refresh
            nodefault
ENDDEFINE 
```
