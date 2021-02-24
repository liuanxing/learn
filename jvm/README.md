# JVM

## 一次编译到处运行
当我们想要执行一个java文件时，需要完成以下步骤
````
* 调用 javac 命令编译一个 Demo.java 文件生成一个 Demo.class 文件
* 调用 java 命令时会由ClassLoader将编译后的class文件加载到内存，同时也会将java类库的文件也同时加载到进来
* 加载进来后会通过字节码解释器或者JIT(及时编译器)来进行解释，解释完成后由执行引擎来执行，而执行引擎面对的就是OS硬件

而由ClassLoader加载开始到执行引擎执行的过程就是JVM
````
任何语言，只要能编译成class，并符合class的规范，那么就能够在JVM里运行，而JVM封装了所有操作系统的对应实现，所以能够做到一次编译到处运行。

## Class文件的生命周期
一个class的生命周期为`loading -> linking -> initializing -> gc` 这四个过程

### loading
JVM是按需动态加载，并且采用双亲委派的机制通过ClassLoader对class文件进行加载。
````
 ClassLoader有四种层级，分别是 BootstrapClassLoader -> ExtensionClassLoader -> AppClassLoader -> CustomClassLoader
 BootstrapClassLoader: 是最顶层的ClassLoader，主要加载 /lib/rt.jar,charset.jar 等核心类库，是由C++实现的
 ExtensionClassLoader: 是加载 /jre/lib/ext/*.jar 或者由 -Djava.ext.dirs 指定的jar包
 AppClassLoader: 是加载 classpath 下指定的内容
 CustomClassLoader: 自定义的ClassLoader
````
需要注意的是，双亲委派机制并不是指这几个ClassLoader是继承关系，而是由一种概念关系，整个双亲委派机制如下：
````
 假设我们自定义了ClassLoader，那么在加载一个类时，首先由CustomClassLoader来检查当前需要被加载的class是否已经存在，如果不存在就向上一级也就是AppClassLoader询问是否被加载，
 如果AppClassLoader也没有加载过当前class，那么就由AppClassLoader向上一级也就是ExtensionClassLoader询问是否已经加载过当前class，如果没有加载，那么就由ExtensionClassLoader
 继续向上一级也就是BootstrapClassLoader询问是否被加载，如果BootstrpClassLoader也没有加载过当前class，那么它就会告诉下一级也就是ExtensionClassLoader让它去加载当前class，
 ExtensionClassLoader会判断当前class是否属于自己加载的范围，如果不属于就委托下一级也就是AppClassLoader进行加载,AppClassLoader也会进行范围判断，如果不符合就委托下一级也就是
 CustomClassLoader进行加载
 整个双亲委派机制的流程便如上所诉，经历了自下而上的检查判断，再到自上而下的加载判断，最终实现class的加载
````