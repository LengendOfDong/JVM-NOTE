# JDK的命令行工具

|名称|主要作用|
|--|--|
|jps|JVM Process Status Tool,显示指定系统内所有的HotSpot虚拟机进程|
|jstat|JVM Statistics Monitoring Tool,用于收集HotSpot虚拟机各方面的运行数据|
|jinfo|Configuration Info for Java,显示虚拟机配置信息|
|jmap|Memory Map for Java,生成虚拟机的内存转储快照（heapdump文件）|
|jhat|JVM Heap Dump Browser,用于分析heapdump文件，它会建立一个HTTP/HTML服务器，让用户可以在浏览器上查看分析结果|
|jstack|Stack Trace for Java,显示虚拟机的线程快照|

## jps:虚拟机进程状况工具
&emsp;&emsp;jps的功能：可以列出正在运行的虚拟机进程，并显示虚拟机执行主类名称以及这些进程的本地虚拟机唯一ID。

&emsp;&emsp;jps命令格式：jps [option] [hostid]

## jstat:虚拟机统计信息监视工具
&emsp;&emsp;jstat可以显示本地或者远程虚拟机进程中的类加载、内存、垃圾收集、JIT编译等运行数据，在没有GUI图形界面，只提供了纯文本控制台环境的服务器上，它将是运行期定位虚拟机性能问题的首选工具。

&emsp;&emsp;jstat命令格式为：jstat [option vmid [interval[s|ms] [count]]]

&emsp;&emsp;对于本地虚拟机进程，vmid与lvmid是一致的，如果是远程虚拟机进程，vmid格式为：[protocol:][//]lvmid[@hostname[:port]/servername]

&emsp;&emsp;参数interval和count分别代表查询间隔和次数，如果省略这两个参数，表示只查询一次。假设需要每250毫秒查询一次进程2764垃圾收集状况，一共查询20次，那命令应该是：jstat -gc 2764 250 20

&emsp;&emsp;选项option代表着用户希望查询的虚拟机信息，主要分为3类：类装载、垃圾收集、运行期编译状况。

## jinfo:Java配置信息工具
&emsp;&emsp;jinfo的作用是实时地查看和调整虚拟机各项参数。jinfo可以使用-sysprops选项把虚拟机进程的System.getProperties()的内容打印出来。

&emsp;&emsp;jinfo命令格式：jinfo [option] pid

## jmap:Java内存映像工具
&emsp;&emsp;jmap命令用于生成堆转储快照。jmap的作用不仅仅是为了获取dump文件，它还可以查询finalize执行队列、java堆和永久代的详细信息。

&emsp;&emsp;jmap命令格式：jmap [option] vmid

## jhat:虚拟机堆转储快照分析工具
&emsp;&emsp;jhat命令与jmap搭配使用，来分析jmap生成的堆转储快照。一般不使用jhat用来分析，一方面耗时且消耗硬件资源，另一方面有专业的分析工具来分析。

## jstack:JAVA堆栈跟踪工具
&emsp;&emsp;jstack命令用于生成虚拟机当前时刻的线程快照。线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合。
