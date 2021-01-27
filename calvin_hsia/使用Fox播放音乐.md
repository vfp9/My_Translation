# Fox plays music
作者：Calvin_Hsia

时间：Thursday, December 16, 2004 9:54 AM

翻译：xinjie 
 
我收到一个客户的问题：

_我已经搜索了网络，并且和很多人一样，找不到答案，所以我只能打扰你。_
_你知道使用什么方式可以使用 VFP 来播放 mp3 格式的音乐吗？只能使用 media player 驱动或 ActiveX 来达到目的吗？有没有其他的方法？_

要简单的播放 mp3 文件，可以试着在命令窗口里执行这个命令：
```foxpro
!start d:\piano.mp3
```
 
一个更优雅的解决方案是：使用 VFP9 提供的 Fox Media Player（任务面板-解决方案示例-VFP9新增内容-Fox Media Player）。

或者，你可以运行这个示例代码。(我在VFP8中实验了，可以运行：我已经将其中涉及VFP9的特性移除，例如 anchoring)。

```foxpro
playit("D:\piano.mp3")

*playit("D:\Calvinh\My Music\Claude Bolling\2003. Pariswing\02. La Mer.wma")

PROCEDURE playit(tcURL, tnDrive)
LOCAL loError, loCD

IF VARTYPE(tcURL)#"C" OR EMPTY(tcURL)
      * Sample URL
      tcURL = "http://radio.livephish.com:8000"
ENDIF

 

      IF TYPE("_SCREEN.oWMP")#"O"
            _SCREEN.ADDOBJECT("oWMP","WMPContainer")
            
            WITH _SCREEN.oWMP
                  .HEIGHT = _SCREEN.HEIGHT
                  .WIDTH = _SCREEN.WIDTH
                  .VISIBLE=.T.
            ENDWITH
      ENDIF

      IF UPPER(tcURL)=="CD"
            IF tnDrive = -1
                  RETURN
            ENDIF

            loCD = _SCREEN.oWMP.oleWMP.cdromCollection.ITEM(tnDrive)

            _SCREEN.oWMP.oleWMP.currentPlaylist = loCD.Playlist
      ELSE
            _SCREEN.oWMP.oleWMP.url = tcURL
      ENDIF

*---------- CLASSES ----------------------------------

DEFINE CLASS WMPContainer AS CONTAINER
      oWMPToolBar = NULL

      ADD OBJECT oleWMP AS OLECONTROL WITH ;
            OLECLASS = 'WMPlayer.OCX'

      ADD OBJECT tmrWMP1 AS tmrWMP

      PROCEDURE INIT
            THIS.oleWMP.WIDTH = THIS.WIDTH
            THIS.oleWMP.HEIGHT = THIS.HEIGHT
            THIS.oWMPToolBar = ;
                  NEWOBJECT('WMPToolBar', SYS(16), NULL, THIS)
            THIS.oWMPToolBar.VISIBLE = .T.
      ENDPROC

      PROCEDURE RELEASE
            _SCREEN.LOCKSCREEN = .T.
            THIS.REMOVEOBJECT('oleWMP')
            RELEASE THIS
            _SCREEN.LOCKSCREEN = .F.
      ENDPROC

      PROCEDURE oleWMP.DoubleClick(p1,p2,p3,p4)
            THIS.VISIBLE = .F.
            THIS.PARENT.VISIBLE = .F.
            THIS.PARENT.oWMPToolBar = NULL
            THIS.PARENT.tmrWMP1.ENABLED = .T.
      ENDPROC
ENDDEFINE

*--------------------------------------------

DEFINE CLASS WMPToolBar AS TOOLBAR
      SHOWTIPS = .T.
      CAPTION = 'Fox Media Player'
      WMPContainer = NULL

      ADD OBJECT CmdClose AS COMMANDBUTTON WITH ;
            TOOLTIPTEXT = 'Close Fox Media Player', ;
            PICTURE = HOME() + 'tools\test\close.bmp', ;
            SPECIALEFFECT = 2, ;
            HEIGHT = 22, WIDTH = 140

       PROC INIT(WMPContainer AS OBJECT)
            THIS.WMPContainer = WMPContainer
      ENDPROC

       PROCEDURE CmdClose.CLICK
            THIS.PARENT.WMPContainer.oleWMP.DoubleClick()
      ENDPROC
ENDDEFINE

*--------------------------------------------

DEFINE CLASS tmrWMP AS TIMER
      INTERVAL = 500
      ENABLED = .F.
      
      PROCEDURE TIMER
            THIS.PARENT.RELEASE()
      ENDPROC
ENDDEFINE
```
