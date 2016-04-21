
There are hundreds of JVM parametersor JVM Optionsexists inside sun JDK and its virtually impossible to keep track of every single JVM option andbased on my experiencewe don'tevenuse most of JVM flags except couple of important JVM option related to java heap size, java options for printing garbage collection details and most likely JVM switches for setting up remote debugging in Java. but there are many other useful category of JVM parameters which you at least like to be familiar even if not intending to use it more frequently. In this article we will see examples of 10 different categories of JVM parameter which I found useful and use more frequently than other. I would recommend to get a full knowledge of what does a particular JVM options does by referring official list of JVM options.
	译者信息 Sun的JDK包含了很多可以参数而且实际上你也不可能了解每一个JVM参数，从我的经验来看，除了那些和堆大小，打印GC信息和远程调试相关的参数以外，其他的参数你可能基本上都用不到。 即使这样，有一些有用的参数你至少需要了解一下。在这篇文章里，我们将会看到10个我觉得有用并且使更常用的JVM参数的例子。我也建议你从官方的文档里去获取 关于JVM参数更全面的资料。
JVM parameters in Java
On the basis of how we specify JVM option it can be divided into two parts, JVM Options which starts with –X and those which starts with -XX:
1)    JVM Options that begin with -Xare non-standard (thy arenot guaranteed to be supported on allJVM implementations), and are subject to change without notice in subsequent releases of the JDK.

2)    JVM Options or parameters which are specified with -XXare not stable and are not recommended for casual use. These options are subject to change without notice also.

I was thinking about writing post on JVM options when I completed my post on Java Heap Size and Java Garbage Collection because these are two main area where we see usages of various JVM flags. But it didn’t happened even after I covered OutOfMemoryError post which hassome JVM option to solve OutOfMemoryError in Java. Now I am happy that I have completed this piece of information and its ready to be published. As always I look for your feedback, suggestions and any other JVM flags which I have missed and you guys find useful to share. 
	译者信息

JVM parameters in Java

我们根据JVM参数以-X开头或-XX开头将JVM参数分成两个部分：

1) 以-X开头的都是非标准的（这些参数并不能保证在所有的JVM上都被实现），而且如果在新版本有什么改动也不会发布通知。

2）以-XX开头的都是不稳定的并且不推荐在生产环境中使用。这些参数的改动也不会发布通知。

当写完 Java Heap Size 和 Java Garbage Collection 两篇文章的时候我就开始考虑写一片关于JVM参数的文章了，因为这两篇文章是关于JVM参数中主要的两个部分。 但是等我写了另一篇关于OutOfMemeoryError的文章 JVM option to solve OutOfMemoryError in Java 后仍然没有开始写……。现在我很开心终于把这篇文章写完并且可以发布了。像往常一样，我期待你们的反馈，建议以及其他我遗漏的但是你们认为有用的JVM参数



Good knowledge of JVM options specially related to GC tuning is important for time critical application e.g. high volume low latency electronic trading platform where every micro seconds matter. though getting right combination requires lot of profiling and trial and error and depends heavily on nature of trading application.
Important Points about JVM Options:
1)   Boolean JVM optionscan be turned on with -XX:+ andcan beturned off with -XX:-.

2)   Numeric JVM Options can beset with -XX:=. Numbers can include 'm' or 'M' for megabytes, 'k' or 'K' for kilobytes, and 'g' or 'G' for gigabytes (for example, 32k is the same as 32768).

3)   String JVM optionscan besetby using-XX:=,andusually used to specify a file, a path, or a list of commands.

The command java -help lists the standard options (standard across different JVM implementations) for the Java application launcher. The command java -X can be used to see the Java application launcher's non-standard (X for extension specific to that JVM) arguments.The -X options are non-standard and subject to change without notice. If you wish to detect which JVM arguments your currently running Java application is using, you can use the ManagementFactory.getRuntimeMXBean().getInputArguments()

Now here is my list of important JVM flags, switches, options or parameters which is most commonly used while running Java applications:
	译者信息

对响应时间要求很高的系统来说，良好掌握JVM关于GC调优的参数是很重要的。比如一个高流量地延迟的电子交易平台，他要求的响应时间都是毫秒级的。要获得适合的参数组合需要大量的分析和不断的尝试，更依赖于交易系统的特性。
关于JVM选项的几点：

1) 布尔型参数选项：-XX:+ 打开， -XX:- 关闭。（译者注：比如-XX:+PrintGCDetails）

2) 数字型参数选项通过-XX:=设定。数字可以是 m/M(兆字节)，k/K(千字节)，g/G(G字节)。比如：32K表示32768字节。（译者注：比如-XX:HeapDumpPath=./java_pid.hprof）

3) 字符行参数选项通过-XX:=设定，通常用来指定一个文件，路径，或者一个命令列表。（译者注：比如-XX:+PrintGCDetails）

命令 java -help可以列出java 应用启动时标准选项（不同的JVM实现是不同的）。java -X可以列出不标准的参数（这是JVM的扩展特性）。-X相关的选项不是标准的，被改变也不会通知。如果你想查看当前应用使用的JVM参数，你可以使用：ManagementFactory.getRuntimeMXBean().getInputArguments()。

下面就是一些我经常用到的JVM参数列表。

1) JV M memory options related to java heap size
Following three JVM options are used to specify initial and max heap size and thread stack size while running Java programs.

  -Xms        set initial Java heap size
  -Xmx        set maximum Java heap size
  -Xss>        set java thread stack size

2) JVM option to print gc details
-verbose:gclogs garbage collector runs and how long they're taking. I generally use this as my first tool to investigate if GC is a bottleneck for a given application.

-XX:+PrintGCDetailsincludes the data from -verbose:gc but also adds information about the size of the new generation and more accurate timings.

-XX:-PrintGCTimeStamps  Print timestamps at garbage collection.
3) JVM parameters to specify Java Garbage collector
-XX:+UseParallelGC      Use parallel garbage collection for scavenges
-XX:-UseConcMarkSweepGCUse concurrent mark-sweep collection for the old generation. (Introduced in 1.4.1)
-XX:-UseSerialGC        Use serial garbage collection. (Introduced in 5.0.)

beware when you use GC Parameters if you are working on time critical application e.g. high frequency trading application. As  GC is time consuming operation and its desired to create a balance.
	译者信息

1) 跟 Java 堆大小相关的 JVM 内存参数

下面三个 JVM 参数用来指定堆的初始大小和最大值以及堆栈大小
  -Xms        设置 Java 堆的初始化大小
 -Xmx       设置最大的 Java 堆大小
 -Xss        设置Java线程堆栈大小

2) 关于打印垃圾收集器详情的 JVM 参数

-verbose:gc 记录 GC 运行以及运行时间，一般用来查看 GC 是否是应用的瓶颈

-XX:+PrintGCDetails 记录 GC 运行时的详细数据信息，包括新生成对象的占用内存大小以及耗费时间等

-XX:-PrintGCTimeStamps  打印垃圾收集的时间戳


3) 设置 Java 垃圾收集器行为的 JVM 参数

-XX:+UseParallelGC      使用并行垃圾收集

-XX:-UseConcMarkSweepGC 使用并发标志扫描收集 (Introduced in 1.4.1)

-XX:-UseSerialGC        使用串行垃圾收集 (Introduced in 5.0.)
需要提醒的是，但你的应用是非常关键的、交易非常频繁应用时，应该谨慎使用 GC 参数，因为 GC 操作是耗时的，你需要在这之中找到平衡点。
4)JVMdebug options JVM options for remote debugging
-Xdebug -Xnoagent -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8000
to read more about remote debugging check How to Setup Java remote debugging in Eclipse and 10 Java debugging tips in Eclipse 

5) JVM options related to profiling
-Xprof
-Xrunhprof

6) JVM options related to java classpath
Xbootclasspathspecifies classpath entries you want loaded without verification. The JVM verifies all classes it loads to ensure they don't try to dereference an object with an int, pop extra entries off the stack or push too many, and so on. This verification is part of the reason why the JVM is very stable, but it's also rather costly, and responsible for a large part of start up delay. Putting classes on the bootclasspath skips this cost, but should only be used when you know the classes have been verified many times before. In JRuby, this reduced startup time by half or more for a simple script. The - Xbootclasspath option can be used to either prepend (/p) or append (/a) resources to the bootstrap classpath.You Can read more about Java Classpath in my articles How Classpath Works in Java and How to Solve ClassNotFoundException in Java

	译者信息

4)JVM调试参数，用于远程调试

-Xdebug -Xnoagent -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8000
关于远程调试请阅读 How to Setup Java remote debugging in Eclipse and 10 Java debugging tips in Eclipse 

5) 关于性能诊断的 JVM 参数

-Xprof
-Xrunhprof


6) 关于类路径方面的 JVM 参数

Xbootclasspath用来指定你需要加载，但不想通过校验的类路径。JVM 会对所有的类在加载前进行校验并为每个类通过一个int数值来应用。这个是保证 JVM 稳定的必要过程，但比较耗时，如果你希望跳过这个过程，就把你的类通过这个参数来指定。
7) JVM options to change   Perm Gen Size
These JVM optiosn are quite useful to solve java.lang.OutOfMemoryError:Perm Gen Space.

-XX:PermSize and MaxPermSize
-XX:NewRatio=2  Ratio of new/old generation sizes.
-XX:MaxPermSize=64m     Size of the Permanent Generation.

8) JVM parameters to trace classloading and unloading
-XX:+TraceClassLoadingand -XX:+TraceClassUnloading are two JVM options which we use to print logging information whenever classes loads into JVM or unloads from JVM. These JVM flags are extremely useful if you have any memory leak related to classloader and or suspecting that classes are not unloading or garbage collected.

9) JVM switches related to logging
-XX:+TraceClassLoading and -XX:+TraceClassUnloading print information class loads and unloads. Useful for investigating if you have a class leak or if old classes (like JITed Ruby methods in JRuby) are getting collected or not.You can read more about logging in Java on my post 10 Tips while logging in Java

-XX:+PrintCompilationprints out the name of each Java method Hotspot decides to JIT compile. The list will usually show a bunch of core Java class methods initially, and then turn to methods in your application. In JRuby, it eventually starts to show Ruby methods as well
	译者信息

7) 用于修改 Perm Gen 大小的 JVM 参数
下面的这三个参数主要用来解决 JVM 错误： java.lang.OutOfMemoryError:Perm Gen Space.

-XX:PermSize and MaxPermSize
-XX:NewRatio=2  Ratio of new/old generation sizes.
-XX:MaxPermSize=64m     Size of the Permanent Generation.

8) 用来跟踪类加载和卸载的信息

-XX:+TraceClassLoading和 -XX:+TraceClassUnloading 用来打印类被加载和卸载的过程信息，这个用来诊断应用的内存泄漏问题非常有用。

9) JVM switches related to logging

-XX:+TraceClassLoading and -XX:+TraceClassUnloading print information class loads and unloads. Useful for investigating if you have a class leak or if old classes (like JITed Ruby methods in JRuby) are getting collected or not.You can read more about logging in Java on my post 10 Tips while logging in Java

-XX:+PrintCompilationprints out the name of each Java method Hotspot decides to JIT compile. The list will usually show a bunch of core Java class methods initially, and then turn to methods in your application. In JRuby, it eventually starts to show Ruby methods as well
10) JVM Switches for debugging purpose
-XX:HeapDumpPath=./java_pid.hprof  Path to directory or file name for heap dump.
-XX:-PrintConcurrentLocks       Print java.util.concurrent locks in Ctrl-Break thread dump.
-XX:-PrintCommandLineFlags   Print flags that appeared on the command line.

That’s all on JVM Options, I understand its not possible to remember all JVM flags but at-least having an idea of what kind of JVM flags are available is good asset. Image for JVM parameters is from Java tuning and Nutshell.  For full list of JVM options you can refer these link from Oracle Java site: Java Hotspot VM Options
	译者信息

10)用于调试目的的 JVM 开关参数

-XX:HeapDumpPath=./java_pid.hprof  Path to directory or file name for heap dump.

-XX:-PrintConcurrentLocks       Print java.util.concurrent locks in Ctrl-Break thread dump.

-XX:-PrintCommandLineFlags   Print flags that appeared on the command line.
