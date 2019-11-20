1.0 版本

可以使用属性文件、`YAML`文件、环境变量和命令行参数等来外部化配置。

1. 主目录下的`devtools`的全局设置属性（当`devtools`处于活动状态时的主目录下的`.spring-boot-devtools.properties`，`~/.spring-boot-devtools.properties`）。

> 注意：文件名以`.`开头。

`spring-boot-devtools`模块可以包含在任何项目中，以提供额外的开发时特性。

```xml 
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

> 打包应用默认不包含`devtools`，除非设置插件的属性为false。

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <excludeDevtools>false</excludeDevtools>
    </configuration>
</plugin>
```

2. 测试代码中的 `@TestPropertySource` 注解。

```java
@TestPropertySource(
    properties = {"key=value"},
    locations = "classpath:config.properties"
)
```

> `@TestPropertySource(properties=...)`优先级高于`@TestPropertySource(locations=...)`。

3. 测试代码中的`@SpringBootTest#properties`注解的属性值。

```java
@SpringBootTest(properties = {"key=value"})
```

4. 命令行参数

```shell
java -jar xx.jar --server.port=8800
```

> 可以使用`SpringApplication.setAddCommandLineProperties(false)`禁用。

5. SPRING_APPLICATION_JSON 中的属性（嵌入在环境变量或系统属性中的内联JSON）。

JSON 文件

```java
SPRING_APPLICATION_JSON='{"key":"value"}' java -jar myapp.jar
```

在命令行中声明

```java
java -jar app.jar --spring.application.json='{"key":"value"}'
```

设置系统变量

```java
java -Dspring.application.json='{"key":"value"}' -jar app.jar
```

在SpringBoot中，配置文件的监听器ConfigFileApplicationListener使用EnvironmentPostProcessor来处理定义的参数，
spring.factories 默认定义三个 EnvironmentPostProcessor 的实现类，其中SpringApplicationJsonEnvironmentPostProcessor来解析该参数，
它默认解析配置第一个的 JSON 字符串，并且这些参数优先级只低于命令行配置。

6. ServletConfig 初始化参数

web.xml

```xml
<servlet>
  <init-param>
      <param-name>key</param-name>
      <param-value>value</param-value>
  </init-param>
</servlet>
```

7. ServletContext 初始化参数

web.xml 

```xml
<context-param>
    <param-name>key</param-name>
    <param-value>value</param-value>
</context-param>
```

8. java:comp/env中的属性值

JNDI（Java Naming and Directory Interface ）

在tomcat/conf/context.xml
或者
tomcat/conf/server.xml中指定

```xml
<Resource name="jndi/config" key="value"/>
```

9. Java 系统属性（System.getProperties()）

常见系统属性`System.getProperties()`

```
java.version                    Java 运行时环境版本
java.vendor	                    Java 运行时环境供应商
java.vendor.url	                Java 供应商的 URL
java.home	                    Java 安装目录
java.vm.specification.version	Java 虚拟机规范版本
java.vm.specification.vendor	Java 虚拟机规范供应商
java.vm.specification.name	    Java 虚拟机规范名称
java.vm.version	                Java 虚拟机实现版本
java.vm.vendor	                Java 虚拟机实现供应商
java.vm.name	                Java 虚拟机实现名称
java.specification.version      Java 运行时环境规范版本
java.specification.vendor	    Java 运行时环境规范供应商
java.specification.name	        Java 运行时环境规范名称
java.class.version	            Java 类格式版本号
java.class.path	                Java 类路径
java.library.path	            加载库时搜索的路径列表
java.io.tmpdir	                默认的临时文件路径
java.compiler	                要使用的 JIT 编译器的名称
java.ext.dirs	                一个或多个扩展目录的路径
os.name	                        操作系统的名称
os.arch	                        操作系统的架构
os.version	                    操作系统的版本
file.separator	                文件分隔符（在 UNIX 系统中是“/”）
path.separator	                路径分隔符（在 UNIX 系统中是“:”）
line.separator	                行分隔符（在 UNIX 系统中是“/n”）
user.name	                    用户的账户名称
user.home	                    用户的主目录
user.dir	                    用户的当前工作目录
```

10. 操作系统环境变量

常见环境变量`System.getenv()`

``` 
USERPROFILE             用户目录
USERDNSDOMAIN           用户域
PATHEXT                 可执行后缀
JAVA_HOME               Java安装目录
TEMP                    用户临时文件目录
SystemDrive             系统盘符
ProgramFiles            默认程序目录
USERDOMAIN              帐户的域的名称
ALLUSERSPROFILE         用户公共目录
SESSIONNAME             Session名称
TMP                     临时目录
Path                    path环境变量
CLASSPATH               classpath环境变量
PROCESSOR_ARCHITECTURE  处理器体系结构
OS                      操作系统类型
PROCESSOR_LEVEL         处理级别
COMPUTERNAME            计算机名
Windir                  系统安装目录
SystemRoot              系统启动目录
USERNAME                用户名
ComSpec                 命令行解释器可执行程序的准确路径
APPDATA                 应用程序数据目录
```

11. RandomValuePropertySource 随机生成的random.*属性

${random.value}、${random.int}、${random.long}、${random.uuid}、${random.int(10)}、${random.int[1024,65536]}

显式调用

```java
RandomValuePropertySource mRandom = new RandomValuePropertySource("mRandom");
Object property = mRandom.getProperty("random.int");
```

12. jar包外部的特定 profile 的配置文件（application-{profile}.properties和YAML变体）

13. jar包内部的特定 profile 的配置文件（application-{profile}.properties和YAML变体）

14. jar包外部的配置文件（application.properties和YAML变体）

15. jar包内部的配置文件（application.properties和YAML变体）

16. 在注解了`@Configuration`的配置类上的`@PropertySource`注解

`@PropertySource`用于加载指定的外部配置文件

```java
@Configuration
@PropertySource("classpath:config.properties")
```

17. 默认属性（特指使用SpringApplication.setDefaultProperties）

```java
SpringApplication application = SpringApplication.run(Application.class, args);
application.setDefaultProperties(customProperties);
```


任何在高优先级属性源里设置的属性都会覆盖低优先级的相同属性
