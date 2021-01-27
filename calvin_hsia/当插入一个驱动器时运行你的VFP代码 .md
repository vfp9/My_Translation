2004年11月16日，星期二，中午12点
作者：Calvin_Hsia
翻译：xinjie

      在上个月的拉斯维加斯的 Devcon 上，我用数码相机照了一些照片，让后将相机连接到我的 Tablet PC 上，运行了下面的这些 Visual FoxPro 代码。

      这些存储介质上的照片通过事件处理程序，自动存入一个表中，并在一个 Grid 中显示出来。

      这些，是利用 BINDEVENTS 函数新的特性而得以实现(这些存在于 VFP9 正式版本，所以，下面的代码只能运行于VFP9及以后的版本)。
 
      下面的代码使用了 SHChangeNotifyRegister 这个 WinAPI 函数来注册了一个利用 Windows Shell 的事件处理。(该API函数的解释见：http://msdn2.microsoft.com/en-us/library/ms647677.aspx，感谢珍宝！大意如下：可以把你的窗口添加到系统的系统消息监视链中，这样窗口就能接收到来自文件系统或者Shell的通知了)
 
      只需定义一个全局变量，并将 oDriveDetect 这个对象赋予这个变量，这个处理程序将一直存在，并在 drive 改变时做出已定义好的反应。
 
      正如你在下面代码所看到的那样，像文件/目录重命名这样的 shell 事件，它仍旧可以有所反应。
 
      在 Task Pane(任务面板) 中有更多的示例：打开任务面板，选择 Solution Samples(解决方案示例) 中的 New in Visual Foxpro 9(VFP9 的新增功能),里面有一个示例 “Binding to Windows Message Events”(绑定 Windoes 消息事件)
 
      (关于拉斯维加斯的屏幕保护程序Demo，可以参看博客 http://blogs.msdn.com/calvin_hsia/archive/2004/10/04/237723.aspx)
 
#define GWL_WNDPROC         (-4)
#define WM_USER                         0x0400
#define WM_USER_SHNOTIFY            WM_USER+10
#define SHCNE_RENAMEITEM          0x00000001
#define SHCNE_CREATE              0x00000002
#define SHCNE_DELETE              0x00000004
#define SHCNE_MKDIR               0x00000008
#define SHCNE_RMDIR               0x00000010
#define SHCNE_MEDIAINSERTED       0x00000020
#define SHCNE_MEDIAREMOVED        0x00000040
#define SHCNE_DRIVEREMOVED        0x00000080
#define SHCNE_DRIVEADD            0x00000100
#define SHCNE_NETSHARE            0x00000200
#define SHCNE_NETUNSHARE          0x00000400
#define SHCNE_ATTRIBUTES          0x00000800
#define SHCNE_UPDATEDIR           0x00001000
#define SHCNE_UPDATEITEM          0x00002000
#define SHCNE_SERVERDISCONNECT    0x00004000
#define SHCNE_UPDATEIMAGE         0x00008000
#define SHCNE_DRIVEADDGUI         0x00010000
#define SHCNE_RENAMEFOLDER        0x00020000
#define SHCNE_FREESPACE           0x00040000

#define SHCNE_DISKEVENTS          0x0002381F
#define SHCNE_GLOBALEVENTS        0x0C0581E0 // Events that dont match pidls first
#define SHCNE_ALLEVENTS           0x7FFFFFFF
#define SHCNE_INTERRUPT           0x80000000 // The presence of this flag indicates

#define CSIDL_DESKTOP                   0x0000        &&// < >
#define CSIDL_INTERNET                  0x0001        &&// IE浏览器(桌面图标)
#define CSIDL_PROGRAMS                  0x0002        &&// 开始-程序菜单
#define CSIDL_CONTROLS                  0x0003        &&// 我的电脑-控制面板
#define CSIDL_PRINTERS                  0x0004        &&// 我的电脑-打印机
#define CSIDL_PERSONAL                  0x0005        &&// 我的文档
#define CSIDL_FAVORITES                 0x0006        &&// < >\Favorites
#define CSIDL_STARTUP                   0x0007        &&// 开始-程序-启动
#define CSIDL_RECENT                    0x0008        &&// < >\Recent
#define CSIDL_SENDTO                    0x0009        &&// < >\SendTo
#define CSIDL_BITBUCKET                 0x000a        &&// < >\回收站
#define CSIDL_STARTMENU                 0x000b        &&// < >\Start Menu
#define CSIDL_MYDOCUMENTS               0x000c        &&// "我的文档"在桌面的图标
#define CSIDL_MYMUSIC                   0x000d        &&// "My Music" 文件夹
#define CSIDL_MYVIDEO                   0x000e        &&// "My Videos" 文件夹

PUBLIC oDriveDetect as DriveDetect 
oDriveDetect=NEWOBJECT("DriveDetect")

DEFINE CLASS DriveDetect AS session
      dwOrigWindProc=0
      dwShNotify=0
      PROCEDURE init
            DECLARE integer GetWindowLong IN WIN32API ;
                  integer hWnd, ;
                  integer nIndex
            DECLARE integer CallWindowProc IN WIN32API ;
                  integer lpPrevWndFunc, ;
                  integer hWnd,integer Msg,;
                  integer wParam,;
                  integer lParam
            THIS.dwOrigWindProc =GetWindowLong(_VFP.HWnd,GWL_WNDPROC)
            DECLARE integer SHChangeNotifyRegister IN shell32 ;
                  integer hWnd, ;
                  integer fSources, ;
                  integer fEvents, ;
                  integer wMsg,;
                  integer cEntries, ;
                  string @ SEntry 
            DECLARE integer SHChangeNotifyDeregister IN shell32 integer
            DECLARE integer SHGetSpecialFolderLocation  IN shell32 ;
                  integer hWnd,;
                  integer nFolder,;
                  string @ pItemList
            DECLARE integer SHGetPathFromIDList IN shell32 ;
                  integer nItemList,;
                  string @cPath
            cSEntry = REPLICATE(CHR(0),8)
            this.dwShNotify = SHChangeNotifyRegister(_vfp.hWnd, ;
                  2,;
                  SHCNE_ALLEVENTS,;
                  WM_USER_SHNOTIFY,1,;
                  @cSEntry)
*!*               this.dwShNotify = SHChangeNotifyRegister(_vfp.hWnd, ;
*!*                     2,;
*!*                     SHCNE_MEDIAINSERTED + SHCNE_MEDIAREMOVED + SHCNE_DRIVEADD + SHCNE_DRIVEREMOVED,;
*!*                     WM_USER_SHNOTIFY,1,;
*!*                     @cSEntry)
            BINDEVENT(_VFP.hWnd, WM_USER_SHNOTIFY,this,"HandleMsg")
            WAIT WINDOW "Monitoring drive changes" nowait &&显示"监视驱动器改变"信息
      PROCEDURE HandleMsg(hWnd as Integer, msg as Integer, wParam as Integer, lParam as Integer)
            LOCAL nRetvalue
            nRetvalue=0
            ?PROGRAM(),hwnd,"Media",TRANSFORM(wParam,"@0x"),TRANSFORM(lParam,"@0x")," "
            pidl1=CTOBIN(SYS(2600,wParam,4),"4rs")
            pidl2=CTOBIN(SYS(2600,wParam+4,4),"4rs")
            cPath=SPACE(270)
            SHGetPathFromIDList(pidl1,@cPath)
            cPath=LEFT(cPath,AT(CHR(0),cPath)-1)
            DO case
            CASE lParam=SHCNE_DRIVEADD
                  ??"Drive added "
                  ShowPix(cPath)
            CASE lParam=SHCNE_DRIVEREMOVED
                  ??"Drive removed" 
            CASE lParam=SHCNE_MEDIAINSERTED
                  ??"Media inserted "
                  ShowPix(cPath)
            CASE lParam=SHCNE_MEDIAREMOVED
                  ??"Media removed "
            ENDCASE 
            ??" path=",cPath,TRANSFORM(pidl2,"@0x")
            nRetvalue=CallWindowProc(this.dwOrigWindProc,hWnd,msg,wParam,lParam)
            RETURN nRetvalue
      PROCEDURE destroy
            IF this.dwShNotify != 0
                  IF SHChangeNotifyDeregister(this.dwShNotify) = 0
                        ?"Deregister ERRORd"
                  ENDIF 
            ENDIF 
ENDDEFINE



* 在文件中调用 SHOWPIX.PRG
LPARAMETERS cPath as String
PUBLIC oShowPix
oShowPix=0
IF PCOUNT()=0
      CLEAR
      cPath=SYS(5)+CURDIR()
ENDIF
oShowPix=CREATEOBJECT("ShowPix",cPath)
oShowPix.show

DEFINE CLASS ShowPix as Form
      left=100
      height=600
      width=900
      DataSession=2
      allowoutput=.f.
      PROCEDURE LoadPix(cPath as String,lRecursive as Boolean)
            LOCAL aFiles[1],n,i,fname
            n=ADIR(aFiles,cPath+"*.*","D")
            FOR i = 1 TO n
                  IF "D"$aFiles[i,5]
                        IF aFiles[i,1] != '.'
                              thisform.Loadpix(cPath+aFiles[i,1]+"\")
                        ENDIF 
                  ELSE
                        fName=cPath+aFiles[i,1]
                        IF JUSTEXT(fName)$"JPG"
                              WAIT WINDOW NOWAIT fname
                              INSERT INTO pix VALUES (fname,FILETOSTR(fname),PicProps(fname))
                        ENDIF 
                  ENDIF
            ENDFOR
            locate
      PROCEDURE init(cPath,lRecursive)
            CREATE CURSOR pix (name c(30),pic w, props m)
            IF VARTYPE(cPath)='L'
                  cPath=SYS(5)+CURDIR()
            ENDIF
            cPath=ADDBS(cPath)      && 如果需要，增加一个反斜杠
            thisform.LoadPix(cPath,lRecursive)
            WAIT CLEAR 
            this.AddObject("grd","grid")
            WITH this.grd as grid
                  .Visible=1
                  .ColumnCount=4
                  .Height=thisform.Height
                  .Width=thisform.Width
                  .RowHeight=200
                  .HeaderHeight=50
                  WITH this.grd.column1 as Column
                        .header1.caption="Filename"
                        .header1.fontsize=14
                        .Width=100
                        .AddObject("edtFname","editbox")
                        .CurrentControl="edtFname"
                        .Sparse=.f.
                        .edtFname.visible=1
                  ENDWITH
                  WITH this.grd.column2 as Column
                        .header1.caption="Picture"
                        .header1.fontsize=14
                        .Width=300
                        .AddObject("imgPic","MyImage")
                        .CurrentControl="imgPic"
                        .RemoveObject("text1")
                        .imgPic.pictureval="pix.pic"
                        .Sparse= .F.
                  ENDWITH
                  WITH this.grd.column3 as Column
                        .header1.caption="Pic Properties"
                        .header1.fontsize=14
                        .AddObject("edtProps","editbox")
                        .CurrentControl="EdtProps"
                        .RemoveObject("text1")
                        .Sparse= .F.
                        .Width=300
                        WITH .EdtProps as EditBox
                              .Visible=.t.
                        ENDWITH 
                  ENDWITH
                  WITH this.grd.column4 as Column
                        .header1.caption="Button"
                        .header1.fontsize=14
                        .AddObject("btn","mybtn")
                        .CurrentControl="btn"
                        .Sparse= .F.
                        .Width=100
                        WITH .btn as commandbutton
                              .Visible=.t.
                        ENDWITH 
                  ENDWITH
            ENDWITH
ENDDEFINE
DEFINE CLASS mybtn as commandbutton
      Caption="Click me!"
      PROCEDURE click
            oShowPic=CREATEOBJECT("form")
            WITH oShowpic as form
                  .Width=SYSMETRIC(1)
                  .Height=SYSMETRIC(2)
                  .Left=0
                  .Top=0
                  .AddObject("img","myimage")
                  WITH .img as image
                        .Stretch=2
                        .Width=thisform.width
                        .Height= thisform.height
                        .PictureVal =pix.pic
                        .Visible=1
                  ENDWITH
            ENDWITH
            oShowPic.show(1)
ENDDEFINE
DEFINE CLASS myimage as Image
      backstyle=0
      stretch=1
      PROCEDURE backstyle_access
            this.PictureVal=pix.pic
            RETURN this.backstyle
      PROCEDURE click
            this.RotateFlip=this.RotateFlip+1
      PROCEDURE dblclick
            ?PROGRAM()
ENDDEFINE
