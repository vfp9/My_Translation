# Creating a VFP application as a service

作者：Calvin_Hsia

时间：2004年12月13日，星期一，1:49 PM

翻译：xinjie 
 
有时使你的应用程序作为服务运行是很有必要的。一个服务可以在任何使用者登录到系统时都可以运行，在系统重新启动后可以自动开始，而且可以避免使用者来注销它。它还可以限制不同的使用者具有不同的访问权限。

正常情况下，创建一个服务是比较复杂的，但是创建一个作为服务的VFP应用程序却非常的简单，它借助两个工具来实现：INSTSRV.EXE 和 SRVANY.EXE 。它们包含在 Windows Resource Kit 中（译者注：不同的操作系统，好象有不同的资源工具包，可以在MS的官方站点下载）。INSTSRV.EXE 需要两个参数：要安装的新的服务名和 SRVANY.EXE 所在的完整路径。

例如，我要在我的安装有 Windoes XP 机器上要创建一个名为“VFPSrv”的服务：

C:\Program Files\Windows Resource Kits\Tools<instsrv VFPSrv "d:\Program Files\Windows Resource Kits\Tools\srvany.exe"

SRVANY 提供所有主要的用以将应用程序伪装成服务的工作，并且查看注册表来找到 EXE 文件并使其作为服务来运行。EXE 文件名、开始路径、任何可选参数都可以在注册表中找到。

复制下面的代码到文件 VFPSRV.REG (改变路径是必须的：注意双斜杠符号)并双击 .REG 文件，以便在注册表中增加这些内容。
```foxpro
 Windows Registry Editor Version 5.00
 [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\VFPSrv\Parameters]
"AppDirectory"="d:\\fox90\\test"
"Application"="d:\\fox90\\test\\vfpsrv.exe"
"AppParameters"="myparm1 myparm2"
```

你可以在 VFP 中使用下面的命令来控制服务：
```foxpro
!net start vfpsrv
!net stop vfpsrv
```
或者在 CMD 窗口使用这样的命令：

 ```foxpro
 net start vfpsrv
```
  
当计时器被触发时，示例服务就执行下面的代码，它们存储在“vfpstrvtn.prg”文件中。然而，这些代码并没有被编译成 EXE 。这就意味着你可以动态改变服务的行为，而不需要 停止/重建/开始 服务。

代码每隔5秒执行一次 timer 事件，每隔5秒列出一个时间(以便于是正确的结果：00, :05, :10, … 每小时经过的秒数)。

如果你运行下面的代码，它会作为一个正常的 PRG 文件运行，并且编译为 VFPSRV.EXE 。

为了使它作为一个服务运行，你可以使用控制面板-<管理工具-<服务 或者使用 “net start vfpsrv”

从网络中另一台机器中，在一个编辑器中打开日志文件，例如 Visual Studio 。你仍旧可以听到嘟嘟声，并看到即使没有使用者登录，日志文件也会改变。

(使用旧版本的 SRVANY 或在一个旧版本的 Windows 时，你或许需要使用 SYS(2340) )

 
```foxpro
*保存这些内容到文件 VFPSRV.PRG
LPARAMETERS parm1,parm2
CLEAR

* http://msdn.microsoft.com/library/default.asp?url=/library/en-us/tools/tools/service_control_utility.asp
* http://www.microsoft.com/msj/1097/WINNT.aspx
*要得到 SrvAny and SrvInst，可以访问http://www.microsoft.com/downloads，在关键字处键入 “Windows 2003 Resource Kit Tools”并点击查找按钮。
*然后，点击 Windows Server 2003 Resource Kit Tools 下载它
*在网页中下载 rktools.exe（它包含 Instsrv 和 Srvany 最新的版本），并在你的系统上进行安装。

#define TIMERINT  5     && 预定义 timer 时间间隔，并同步一天中 TIMERPINT 的秒数

PUBLIC oService
oService=NEWOBJECT("vfpsrv")
oService.logstr("开始服务：得到一些参数："+TRANSFORM(parm1)+" "+TRANSFORM(parm2))

IF _vfp.StartMode<0     && 如果我们运行的是一个 EXE 文件
      READ events       && 开始事务
ENDIF

*创建 VFPSRVRTN.PRG 使机器发出嘟嘟声
SET TEXTMERGE TO vfpsrvrtn.prg
      \LPARAMETERS oTimer,oService
      \oService.logstr("")
      \Messagebeep(0)
SET TEXTMERGE TO

*RETURN

 

BUILD PROJECT vfpsrv from vfpsrv

MODIFY PROJECT vfpsrv nowait

STRTOFILE("screen=off"+CHR(13)+CHR(10),"config.fpw")  && 运行时刻隐藏 VFP 桌面

_vfp.ActiveProject.Files.Add("config.fpw")
_vfp.ActiveProject.Close

BUILD EXE vfpsrv FROM vfpsrv

ERASE config.fpw


DEFINE CLASS vfpsrv AS form
      PROCEDURE Init
            this.logstr(CURDIR())

            DECLARE integer MessageBeep IN WIN32API integer

            this.AddObject("mytimer","mytimer")
            
            WITH this.mytimer
                  .enabled=.t.
                  .interval=1000    && 1秒后开始
            ENDWITH

      PROCEDURE Destroy
            ?PROGRAM()
            MessageBeep(32)

      PROCEDURE logstr(str as String)
            str=TRANSFORM(DATETIME())+" "+str+CHR(13)+CHR(10)

            ??str

            STRTOFILE(str,"c:\vfpsrv.log",.t.)
ENDDEFINE

DEFINE CLASS mytimer AS timer
      PROCEDURE timer
            DO ("vfpsrvrtn" ) with this,oService

            CLEAR PROGRAM

            dtNow=DATETIME()  && 立即读取 datetime() 和 seconds()

            nsec=SECONDS()
            nsec=CEILING((nsec+.5)/TIMERINT)*TIMERINT && 目标是自午夜流逝的时间+少许的秒数

            dtTarget=DTOT(DATE())+nsec
            this.interval=(dtTarget-dtNow) * 1000
ENDDEFINE
```
