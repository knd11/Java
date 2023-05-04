- # Java
	- ## 1. Java 基础
	  collapsed:: true
		- ### ⽤户⾃⼰写⼀个String类，会发⽣什么？
	- ## 2. JVM
		- ### 什么是类加载器，类加载器有哪些？
			- 实现通过类的权限定名获取该类的二进制字节流的代码块叫做==类加载器==。主要有一下四种类加载器：
			- >==启动类加载器==（Bootstrap ClassLoader）：用来加载 Java 核心类库，无法被 Java 程序直接引用。
			- >==扩展类加载器== (extensions class loader)：
			-
			-
			-
			- 。）：它用来加载 Java
			- 的扩展库。Jva 虚拟机的实现会提供一个扩展库目录。该类加载器在此目录里面查找并加载 Java 类。
			- 系统类加载器（system class loader）：它根据 Java 应用的类路径（CLASSPATH）来加载 Java 类。一般来说，Java 应用的类都是由它来完成加载的。可以通过 ClassLoader. GetSystemClassLoader (0 来获取它。。用户自定义类加载器，通过继承 java. Lang. ClassLoader 类
			- 的方式实现。
			-
		-
		-
		- ### 类加载器——双亲委派模型
			- 当一个类收到了类加载请求时，不会自己先去加载这个类，而是将其委派给父类，由父类去加载，如果此时父类不能加载，反馈给子类，由子类去完成类的加载。
		-
	-
-
-