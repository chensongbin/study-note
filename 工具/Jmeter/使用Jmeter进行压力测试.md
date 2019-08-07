# 前言

使用场景

- 对于一个系统做了优化，不知道这个优化效果如何，可以通过压力测试对比一下
- 测试系统最大能承受的并发数目





# 开始

## 大纲

**第一步：**

**通过图形化界面写测试脚本**

1. 新建一个测试计划
2. 添加一个线程组
   - 线程数目
   - Ramp-Up Period(in seconds) ：在多少秒内启动所有线程
   - Loop Count：循环测试计划的次数
3. 添加http请求
4. 添加监听器



**第二步：**

**使用命令行执行上一步写好的测试脚本**

```shell
jmeter -n -t 测试脚本.jmx -l 测试结果.jtl

-n 说明Jmeter在命令行模式下运行
-t [name of JMX file that contains the Test Plan].
-l [name of JTL file to log sample results to].
-j [name of JMeter run log file].
-g [path to CSV file] 
-e 生成测试报告
-o 测试报告输出文件夹路径
```



**第三步：**

使用GUI模式，导入测试结果进行查看





# 注意点

- 运行jmeter的机子与被测试的应用最好不要放同一个机子
- 并发线程不要设置太高，设置200以下







# 参考

[官方文档](https://jmeter.apache.org/usermanual/get-started.html)