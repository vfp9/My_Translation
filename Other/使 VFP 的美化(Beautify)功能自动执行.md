**声明：这篇儿是原创！不是翻译**

作者：xinjie

***
VFP 的系统变量 _Beautify 保存有指定的美化程序。该程序接收两个参数：要美化的源文件名和美化选项。该程序执行完后，有一个返回值，为已美化的代码所存储的文件名。
 
美化选项是由9组ASC字符组成：

- 第一部分 chr(n) + chr(0) + chr(0) + chr(0) ,n 对应变量大小写设置，其值和显示顺序相同

- 第二部分 chr(n) + chr(0) + chr(0) + chr(0) ,n 对应关键字大小写设置，其值和显示顺序相同

- 第三部分 chr(n) + chr(0) + chr(0) + chr(0) ,n 为当 TAB 设置为空格时，指定的空格数值；当使用 TAB 设置锁进时，n = 系统设置（估计可以自己指定，未测试）

- 第四部分 chr(n) + chr(0) + chr(0) + chr(0) ,n 对应TAB设置,其值和显示顺序相同

- 第五部分 chr(0) + chr(0) + chr(0) + chr(0) 

- 第六部分 chr(n) + chr(0) + chr(0) + chr(0)  ,注释不缩进 n=0   否则 n =1

- 第七部分 chr(n) + chr(0) + chr(0) + chr(0)  ,续行不缩进 n=0   否则 n =1

- 第八部分 chr(n) + chr(0) + chr(0) + chr(0)  ,proc不缩进 n=0   否则 n =1

- 第九部分 chr(n) + chr(0) + chr(0) + chr(0)  ,case不缩进 n=0   否则 n =1
 
利用这些信息，我们就可以做自动执行的美化功能，而无需打开文件，自然也就跳过了美化选项对话框。
 
代码大致是这样的：
```foxpro
Set Procedure To (_beautify)
*!* 这里设置美化选项
*!* Options = Chr(n)+.......+Chr(0)
StrToFile(FileToStr(beautify([program1.prg], Options)), [program1.prg])
*!* Set Procedure To
```
--------------------------------------------------------------------------------------------------
add: 2009-5-31
 
自动获取 VFP 美化选项并美化指定PRG文件：
```foxpro
Local LcResource As Character, LaBeautify[1]
If Set("Resource") == [ON]
    Select Data From Sys(2005) Where Upper(Id) == [BEAUTIFY] Into Array LaBeautify
Else
    m.LcResource = Addbs(Getenv("APPDATA")) + [Microsoft\Visual FoxPro 9\FxoUser.dbf]
    If File(m.LcResource, 1)
        Select Data From (m.LcResource) Where Upper(Id) == [BEAUTIFY] Into Array LaBeautify
    Endif
Endif
If Select([FoxUser]) > 0
    Use In Foxuser
Endif
Set Procedure To (_Beautify) Additive
Strtofile(Filetostr(beautify([Test.prg], Right(m.LABEAUTIFY[1], 36))), [Test.prg])
```
