1、配置JVM装载环境

2、解析虚拟机参数

3、设置线程栈大小

4、执行Java main方法


# 1、配置JVM装载环境
Java代码执行时需要一个JVM环境，JVM环境的创建包括两部分：JVM.dll文件的查找和装载。

JVM.dll文件的查找
通过CreateExecutionEnvironment方法实现，根据当前JRE环境的路径和系统版本寻找jvm.cfg文件

实现逻辑：

1、GetJREPath查找当前JRE环境的所在路径；

2、ReadKnownVms读取JRE路径\lib\ARCH(CPU构架)\JVM.cfg文件，其中ARCH(CPU构架)通过GetArch方法获取，在window下有三种情况：amd64、ia64和i386；

3、CheckJvmType确定当前JVM类型，先判断是否通过-J、-XXaltjvm=或-J-XXaltjvm=参数指定，如果没有，则读取JVM.cfg文件中配置的第一个类型；

4、GetJVMPath根据上一步确定的JVM类型，找到对应的JVM.dll文件；

JVM.dll文件的装载

初始化虚拟机中的函数调用，即通过JVM中的方法调用JVM.dll文件中定义的函数

1、LoadLibrary方法装载JVM.dll动态连接库；

2、把JVM.dll文件中定义的函数JNI_CreateJavaVM和JNI_GetDefaultJavaVMInitArgs绑定到InvocationFunctions变量的CreateJavaVM和GetDefaultJavaVMInitArgs函数指针变量上；

# 2、虚拟机参数解析

装载完JVM环境之后，需要对启动参数进行解析，其实在装载JVM环境的过程中已经解析了部分参数，该过程通过ParseArguments方法实现，并调用AddOption方法将解析完成的参数保存到JavaVMOption中

```
typedef struct JavaVMOption {
    char *optionString;
    void *extraInfo;
} JavaVMOption;
```

AddOption方法，这里对-Xss参数进行特殊处理，并设置threadStackSize，因为参数格式比较特殊，其它是key/value键值对，它是-Xss512的格式。后续Arguments类会对JavaVMOption数据进行再次处理，并验证参数的合理性。


