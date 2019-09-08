原码：最高位为符号位
反码：在原码的基础上，符号位不变，其余位取反
补码：正数为原码，负数为反码+1

使用补码的好处：
（1）零只有一种表示
（2）简化整数的加减法计算，将减法计算视为加法计算





# Java虚拟机参数
-Xmx32m 堆最大空间

-Xss128K 线程最大栈空间

逃逸分析
https://www.hollischuang.com/archives/2583
-XX:+DoEscapeAnalysis ： 表示开启逃逸分析
-XX:-DoEscapeAnalysis ： 表示关闭逃逸分析 从jdk 1.7开始已经默认开始逃逸分析，如需关闭，需要指定-XX:-DoEscapeAnalysis



jdk7的方法区为永久代(Perm)
-XX:PermSize=5M
-XX:MaxPermSize=5m

jdk8中永久代被彻底移除，使用元数据区(直接内存，如果不指定最大大小，默认会耗尽系统内存)
-XX:MaxMetaspaceSize



## 跟踪垃圾回收
-XX:+PrintGC 打印gc日志
-XX:+PrintGCDetails
-XX:+PrintHeapAtGC 每次GC前后分别打印堆的信息
-XX:+PrintGCTimeStamps 每次发生GC时，额外输出GC发生的时间
-Xloggc:log/gc.log 在当前目录下的log文件夹下的gc.log文件中记录所有gc日志



## 跟踪类加载/卸载
-verbose:class 跟踪类的加载和卸载
-XX:+TraceClassLoading 跟踪类的加载和卸载
-XX:+TraceClassUnloading 跟踪类的卸载

在运行时打印、查看系统中各个类的实例数目
（1）-XX:+PrintClassHistogram
（2）在Java控制台中Ctrl+Break



## 查看系统参数
-XX:+PrintVMOptions  打印虚拟机接收到的命令行显式参数
-XX:+PrintCommandLineFlags 显式参数和隐式参数(虚拟机启动时自动设置的)
-XX:+PrintFlagsFinal 打印所有的系统参数的值



## 堆参数配置

堆配置参数
-Xms5m 初始堆大小5MB
-Xmx20m 最大堆大小20MB

新生代配置
设置一个较大的新生代会减小老年代的大小
（新生代大小一般设为整个堆的1/3到1/4左右）
-Xmn 
-XX:SurvivorRatio=eden/from=eden/to 设置新生代中eden和from/to的比例

-XX:NewRatio=老年代/新生代

![1567061535801](imgae/1567061535801.png)





## 堆溢出处理

```shell
-XX:+HeapDumpOnOutOfMemoryError 在内存溢出时导出整个堆栈信息
-XX:HeapDumpPath=d:/out.dump 导出到文件
```





## 直接内存

```shell
-XX:MaxDirectMemorySize  设置最大直接内存
如果不设置，最大值默认为最大堆空间（即-Xmx）
```

注意：

- 一般来说，直接内存的访问速度会快于堆内存
- 在申请内存时，堆内存速度更快







参考资料：

《实战Java虚拟机》