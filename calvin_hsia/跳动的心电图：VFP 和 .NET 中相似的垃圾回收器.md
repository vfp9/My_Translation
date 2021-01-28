# Heartbeat: Garbage collection in VFP and .NET are similar
作者：Calvin_Hsia

时间：Thursday, January 20, 2005 10:28 AM

翻译：xinjie
 
VFP 在一个表中将使用者定义的字段名、属性名、类名和过程名作为一个变量存储在一个表中。

当命名表（name table）的空间用完时，垃圾回收器（GC）开始工作。所有的输入都被标记为未使用。然后，所有不同的 name table 客户端会接收到一个消息来标记它们所使用的名字。这些客户端包括对象管理器、数据库引擎、变量管理器以及编译代码管理器。剩余的名字被标记为未使用，并且从表中被清除。

这个算法非常像 [.NET 垃圾回收器](http://msdn.microsoft.com/msdnmag/issues/1100/GCI/default.aspx)。它没有名字的引用记数，并且未使用的名字不能被引用。然而，如果没有引用或指针，那么在 VFP 中的垃圾回收器就非常简单了。

通过一个创建和抛弃成批的唯一标识符的循环，你可以看到垃圾回收器运行的效果，它需要多长的时间。

运行下面的代码，你会在你的 Screen 中得到一个跳动的心电图。你或许需要根据你的机器性能调整图形的坐标比例。x 坐标表示时间，y 坐标表示抛弃每一批所用的时间总合。

合成的图表显示了在表中增加 10,000 批名字所需要花费的时间。时间是周期性的，大约每 6 批重复一次。如果每批所用的时间正好相同，那么图表就会显示为一条直线（心脏就停止了跳动）。

填充 name table 过程中，增加一个名字时需要一些时间。当表填充满了，垃圾回收器就开始工作，它会清除大量的表。

每 6 批重复出现一次，是因为 name table 的尺寸大约可以容纳 65000 个名字，当回收时并不是每个名字都是垃圾。

 让我们成为心脏病专家，并且来检查心跳的情况。心电图中最低的地方实际是批次花费时间最长的地方（最高的数值在屏幕左上方）。name table 被清理后，它基本上就是空的了。因为它还在继续填充，所以每一批的时间就增加。

 ```foxpro
CLEAR

x0=0
y0=0
nPrior=0

SET DECIMALS TO 3

CREATE CURSOR results (x i,nsecs b,delta b)

BROWSE LAST nowait

FOR i = 1 TO 200
      nsec=SECONDS()    && 开始的时间
      num=5000
      usenames(i,num)
      ntime=SECONDS()-nsec    && 结束的时间
      nval=ntime-nPrior && 时间增量(# of names/sec)

*     ?i,ntime,ntime,nval

      y1=ntime*3000 &&+_screen.Height/2   && 调整比例放置在屏幕中间 
      x1=i*4      && 较好的来显示 X 坐标

      INSERT INTO results VALUES (i,ntime,nval)

      _screen.Line(x0,y0,x1,y1)     && 绘制连接点的连线

      x0=x1 && 定义起始点
      y0=y1
      nPrior=ntime      && 和已经经过的时间
ENDFOR

PROCEDURE usenames(base,n)
      LOCAL i
      FOR i = 1 TO n
            cvar="x"+PADL(base,3,"0")+TRANSFORM(i)    && 创建像"x0011"这样的唯一容器标识符
            STORE i TO (cvar) && 创建变量容器
      ENDFOR
RETURN && 退出循环时，容器被清除
