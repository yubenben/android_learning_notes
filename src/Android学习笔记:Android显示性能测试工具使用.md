#Android学习笔记:Android显示性能测试工具使用
##Traceview工具的使用
Traceview是一个记录函数运行时间的工具,我们可以用它来检测我们应用的性能.
我们可以通过两种方式获得trace log文件.
1,通过在代码里添加代码,记录代码段内的时间.
``` java
// start tracing to "/sdcard/calc.trace"
Debug.startMethodTracing("calc");
// ...
// stop tracing
Debug.stopMethodTracing();
```
这种代码插桩的方式会降低运行速度,所以获得的值只能作为对比参考值.不能拿绝对值来衡量应用性能.
当你调用startMethodTracing()后,系统会创建一个<trace-base-name>.trace文件,应用的调用日志会存放在系统的buffer data内,
直到遇到stopMethodTracing(),系统会将buffer data内的日志输出到文件中.如果在调用stop之前,日志量达到buffer的最大值,
会停止trace,并且输出一个警告到终端.
在4.4之后,可以使用startMethodTracingSimpling()函数,这个会对应用的性能影响较小.
2,
