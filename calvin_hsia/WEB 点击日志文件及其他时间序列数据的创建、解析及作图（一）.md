# Creating, parsing, graphing web hit logfiles or other temporal data

作者：Calvin_Hsia

时间：2004年10月28日，星期四，6:30 PM

翻译：xinjie

 
查看时间序列数据可以得到完整的信息。例如，我有一个大约有 16000 张图片的表。每条记录都有一个时间戳。通过查看每条的时间序列信息，可以清楚的看到一些东西，例如，孩子的出生、亲戚的来往或者是买了一个照相机。

网络信息服务（IIS）是运行于 Windows XP 专业版的一个 WEB 服务。如果你有 XP 专业版，你可以通过控制面版-<增加/删除程序-<增加/删除 windows 组件来安装它。一旦安装完，你就可以在同一台机器的 IE 地址栏中键入 “localhost”来访问你自己的 WEB 地址。如果是用网络中的另一台机器，你可以通过键入机器名来访问 WEB 地址。为了在 Internet 上访问机器，你需要从你的在线提供商那里得到一个静态的 IP 地址并绑定到一个域名。例如，我的 IP 是 http://66.13.78.182 我的域名是 www.calvinhsia.com 。

当安装完成后，IIS 默认状态下会将每一次 WEB 地址的访问记录到一个日志文件中。选择 开始-<控制面版-<管理工具-<IIS 管理器，在 本地计算机/网站/默认网站 处，右击鼠标，选择“属性”。WEB 地址页会显示一个“启用日志记录”选择框，点击“属性”，会看到默认的日志文件目录是“c:\windows\system32\logfiles”，日志文件的文件名为：W2SVC1\exyymmdd.log 。 

继续在本地机器上使用 http://localhost 访问自己的 WEB 页，然后查看生成的日志文件。日志中记录的时间是格林威治时间，而不是大多数人的当地时间。这也就意味着，你的日志文件名显示的是明天（或昨天），这要看你在哪个时区。

(除了 IIS 日志文件外，你还可以自定义日志文件。例如，我的 WEB 站点日志是一个 fox 表。）

下面的 120 行代码解析 IIS 日志文件为一个游标，并将结果以曲线图形式给出。它仅需要短短的几秒，就可以遍历 1000 多个日志文件，里面记录了超过 50 万的站点访问记录。显示曲线图也就是一秒左右的时间。

当使用数据创建或缩放曲线图时，这个 SQL Select 命令很方便：
```foxpro
SELECT INT((dtime-this.nMin)/this.nBucketSize) as bucket,COUNT(*) as cnt FROM webhits GROUP BY 1 INTO CURSOR buckets
```

当你在曲线图上点击一个点时，一个相应的 SQL Select 命令就会执行，得到那一天的 WEB 访问概要，并会显示在另一个表单的 Grid 中。 
