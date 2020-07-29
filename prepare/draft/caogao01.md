// 获取调用者类
Class callerClass = Reflection.getCallerClass()
// 判断是不是系统加载器，抛出SecurityException
VM.isSystemDomainLoader(callerClass.getClassLoader())

// 通过反射获取类的静态字段，这样就绕过了 getUnsafe() 的安全限制。  
``` java
Field f= Unsafe.class.getDeclaredField("theUnsafe");
f.setAccessible(true);
U= (Unsafe) f.get(null);
```






使用“先 DB 再缓存”的方式，并且缓存操作用 delete 而不是 set。

Enumeration和Iterator接口的区别
大型网站技术架构 核心原理与案例分析
设计模式: 可复用面向对象软件的基础

其他的哈希算法如sha256等
加盐哈希
加盐哈希的步骤如下：
	用户注册时，给他随机生成一段字符串，这段字符串就是盐（Salt）
	把用户注册输入的密码和盐拼接在一起，叫做加盐密码
	对加盐密码进行哈希，并把结果和盐都储存起来
在登陆时，先取出盐，再同样进行拼接、计算哈希，就能判断密码的合法性。用于应对彩虹表



CPU密集型和IO密集型。那么面对CPU密集型的任务，线程数不宜过多，一般选择CPU核心数+1或者核心数的2倍是比较合理的一个值。因此我们可以考虑将corePoolSize设置为CPU核心数+1，maxPoolSize设置为核心数的2倍。

同样的，面对IO密集型任务时，我们可以考虑以核心数乘以4倍作为核心线程数，然后核心数乘以5倍作为最大线程数的方式去设置线程数，这样的设置会比直接拍脑袋设置一个值会更合理一些。

当然总的线程数不宜过多，控制在100个线程以内比较合理，否则线程数过多可能会导致频繁地上下文切换，导致系统性能反不如前。




Java字节码指令是Jvm层面的汇编语言，或者说是Java代码的最小执行单元。

javac命令会将Java源文件编译成字节码文件，即.class文件

i = i++ 和 i ++ 和 ++ i的结果本质的不同 通过java -p 发现