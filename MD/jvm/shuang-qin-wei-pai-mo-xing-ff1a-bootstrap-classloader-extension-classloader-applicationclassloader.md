# 双亲委派模型

JVM的类加载是通过类加载器实现的， Java中的类加载器体系结构如下：

 ![](https://github.com/M78Snail/JavaReview/blob/master/MD/jvm/assets/import2.9.1.png) 

* 启动类加载器（Bootstrap ClassLoader）：是用本地代码实现的类装入器，它负责将/lib下面的类库加载到内存中（比如rt.jar）。由于引导类加载器涉及到虚拟机本地实现细节，开发者无法直接获取到启动类加载器的引用，所以不允许直接通过引用进行操作

* 标准扩展类加载器：是由 Sun 的 ExtClassLoader（sun.misc.Launcher$ExtClassLoader）实现的。它负责将
  &lt;Java\_Runtime\_Home&gt; /lib/ext或者由系统变量 java.ext.dir指定位置中的类库加载到内存中。开发者可以直接使用标准扩展类加载器

* 应用程序类加载器：由sun.misc.Launcher$AppClassLoader实现，负责加载用户类路径classpath上所指定的类库，是类加载器ClassLoader中的getSystemClassLoader\(\)方法的返回值，开发者可以直接使用应用程序类加载器，如果程序中没有自定义过类加载器，该加载器就是程序中默认的类加载器 值得注意的是：上述三个JDK提供的类加载器虽然是父子类加载器关系，但是没有使用继承，而是使用了组合关系。

从JDK1.2开始，JVM规范推荐开发者使用双亲委派模式\(ParentsDelegation Model\)进行类加载，其加载过程如下：

1. 如果一个类加载器收到了类加载请求，它首先不会自己去尝试加载这个类，而是把类加载请求委派给父类加载器去完成

2. 每一层的类加载器都把类加载请求委派给父类加载器，直到所有的类加载请求都传递给顶层的启动类加载器

3. 如果顶层的启动类加载器无法完成加载请求，则子类加载器会尝试去加载，如果连最初发起类加载请求的类加载器也无法完成加载请求时，将会抛出ClassNotFoundException，而不再调用其子类加载器去进行类加载

采用双亲委派模型的好处在于：使得java类随着它的类加载器一起具备了一种带有优先级的层次关系，越是基础的类，越是被上层的类加载器进行加载，保证了java程序的稳定运行。

