1. IOC和DI是什么？
IOC（控制反转）：全称为 Inverse of Control。从字面上理解就是控制反转了，将对在自身对象中的一个内置对象的控制反转，反转后不再由自己本身的对象进行控制这个内置对象的创建，而是由第三方系统去控制这个内置对象的创建。
DI（依赖注入）：全称为 Dependency Injection，意思自身对象中的内置对象是通过注入的方式进行创建。
简单来说就是把本来在类内部控制的对象，反转到类外部进行创建后注入，不再由类本身进行控制，这就是IOC的本质。

2. Spring IOC 的理解，其初始化过程？
ClassPathResource resource = new ClassPathResource("bean.xml");  
DefaultListableBeanFactory factory = new DefaultListableBeanFactory();  
XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(factory);  
reader.loadBeanDefinitions(resource); 
ApplicationContext比较复杂，它不但继承了BeanFactory的大部分属性，还继承其它可扩展接口，扩展的了许多高级的属性，其接口定义如下：
public interface ApplicationContext extends EnvironmentCapable, 
　　　　　　　　　　　　　　　　　　ListableBeanFactory, // 继承于BeanFactory
　　　　　　　　　　　　　　　　　　HierarchicalBeanFactory, // 继承于BeanFactory
　　　　　　　　　　　　　　　　　　MessageSource, 
　　　　　　　　　　　　　　　　　　ApplicationEventPublisher,
　　　　　　　　　　　　　　　　　　ResourcePatternResolver // 继承ResourceLoader，用于获取resource对象
在BeanFactory子类中有一个DefaultListableBeanFactory类，它包含了基本Spring IoC容器所具有的重要功能，开发时不论是使用BeanFactory系列还是ApplicationContext系列来创建容器基本都会使用到DefaultListableBeanFactory类，可以这么说，在spring中实际上把它当成默认的IoC容器来使用。
第一步 Resource定位
即BeanDefinition资源的定位，spring定义了一个Resource接口来定义资源，ResourceLoader负责资源的定位。
常用的resource资源类型如下：
　　FileSystemResource：以文件的绝对路径方式进行访问资源，效果类似于Java中的File;
　　ClassPathResource：以类路径的方式访问资源，效果类似于this.getClass().getResource("/").getPath();
　　ServletContextResource：web应用根目录的方式访问资源，效果类似于request.getServletContext().getRealPath("");
　　UrlResource：访问网络资源的实现类。例如file: http: ftp:等前缀的资源对象;
　　ByteArrayResource: 访问字节数组资源的实现类。
第二步 通过返回的resource对象，进行BeanDefinition的载入
就是把用户卸载配置文件中的bean表示成IoC容器内的数据结构，即BeanDefinition。
通过BeanDefinition这个数据结构IoC容器能够方便的对POJO对象也就是Bean进行管理。
BeanDefinition的载入分为两个过程，首先通过调用XML的解析器得到Document对象，但这些Document对象并没有按照spring定义的bean的规则进行解析；
然后DocumentReader按照spring定义的bean的规则进行解析，默认的DocumentReader是DefaultBeanDefinitionDocumentReader。 
第三步，将BeanDefinition注册到容器中
载入的BeanDefinition最终是通过一个HashMap来持有的，因此注册也就是把解析得到的BeanDefinition设置到HashMap中去。
通过实现BeanDefinitionRegistry接口的方法registerBeanDefinition来注册BeanDefinition。

3. BeanFactory 和 FactoryBean的区别？
BeanFactory，以Factory结尾，表示它是一个工厂类(接口)，用于管理Bean的一个工厂。
在Spring中，BeanFactory是IOC容器的核心接口，它的职责包括：实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。
Spring为我们提供了许多易用的BeanFactory实现，XmlBeanFactory就是常用的一个，该实现将以XML方式描述组成应用的对象及对象间的依赖关系。
XmlBeanFactory类将持有此XML配置元数据，并用它来构建一个完全可配置的系统或应用。
Resource resource = new FileSystemResource("beans.xml");
BeanFactory factory = new XmlBeanFactory(resource);
或者
ApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"applicationContext.xml", "applicationContext-part2.xml"});
BeanFactory factory = (BeanFactory) context;
基本就是这些了，接着使用getBean(String beanName)方法就可以取得bean的实例；BeanFactory提供的方法及其简单，仅提供了六种方法供客户调用：
    boolean containsBean(String beanName) 判断工厂中是否包含给定名称的bean定义，若有则返回true
    Object getBean(String) 返回给定名称注册的bean实例。根据bean的配置情况，如果是singleton模式将返回一个共享实例，否则将返回一个新建的实例，如果没有找到指定bean,该方法可能会抛出异常
    Object getBean(String, Class) 返回以给定名称注册的bean实例，并转换为给定class类型
    Class getType(String name) 返回给定名称的bean的Class,如果没有找到指定的bean实例，则排除NoSuchBeanDefinitionException异常
    boolean isSingleton(String) 判断给定名称的bean定义是否为单例模式
    String[] getAliases(String name) 返回给定bean名称的所有别名
FactoryBean，以Bean结尾，表示它是一个Bean，不同于普通Bean的是：它是实现了FactoryBean<T>接口的Bean
通过getBean(String BeanName)获取到的Bean对象并不是FactoryBean的实现类对象，而是这个实现类中的getObject()方法返回的对象。
要想获取FactoryBean的实现类，就要getBean(&BeanName)，在BeanName之前加上&。
示例xml文件
<bean id="student" class="com.spring.bean.Student">  
    <property name="name" value="zhangsan" />  
</bean> 
<bean id="factoryBeanPojo" class="com.spring.bean.FactoryBeanPojo">  
    <property name="type" value="student" />
</bean> 
示例class文件
public class FactoryBeanPojo implements FactoryBean{
	private String type;
	@Override
	public Object getObject() throws Exception {
		return "student".equals(type) : new Student() : null;
	}
    ......
}
示例测试文件
ClassPathXmlApplicationContext cpxa = new ClassPathXmlApplicationContext("...");
Object school=  cpxa.getBean("factoryBeanPojo");
FactoryBeanPojo factoryBeanPojo= (FactoryBeanPojo) cpxa.getBean("&factoryBeanPojo");
System.out.println(school.getClass().getName());    // com.spring.bean.Student
System.out.println(factoryBeanPojo.getClass().getName());  //com.spring.bean.FactoryBeanPojo
BeanFactory是提供了OC容器最基本的形式，给具体的IOC容器的实现提供了规范，
FactoryBean可以说为IOC容器中Bean的实现提供了更加灵活的方式，FactoryBean在IOC容器的基础上给Bean的实现加上了一个简单工厂模式和装饰模式
BeanFactory和FactoryBean其实没有什么比较性的，只是两者的名称特别接近

4. BeanFactory和ApplicationContext的区别？
容器是spring的核心，使IoC管理所有和组件，spring的两种容器：BeanFactoy 和 ApplicationContext应用上下文
BeanFactory是spring中比较原始的Factory，是Spring里面最低层的接口，提供了最简单的容器的功能，只提供了实例化对象和拿对象的功能。如XMLBeanFactory就是一种典型的BeanFactory。
原始的BeanFactory无法支持spring的许多插件，如AOP功能、Web应用等。 
ApplicationContext接口是一个BeanFactory基础上封装了更多功能的，Spring中最为常用的IoC容器，如资源/国际化支持/框架事件支持等
其包含两个子接口：ConfigurableApplicationContext、WebApplicationContext。
ApplicationContext以一种更向面向框架的方式工作以及对上下文进行分层和实现继承
ApplicationContext的三个实现类：
    ClassPathXmlApplication：把上下文文件当成类路径资源
    FileSystemXmlApplication：从文件系统中的XML文件载入上下文定义信息
    XmlWebApplicationContext：从Web系统中的XML文件载入上下文定义信息
Bean的实例化的时候需要一些初始化的动作，同样当不再使用的时候，需要从容器中将其销毁
对象的初始化：<bean init-method="方法名">
对象的销毁：<bean destroy-method="方法名">
销毁对象的过程：主线程先被中断，然后Java虚拟机使用垃圾回收机制回收Bean对象
BeanFactory在初始化容器时，并未实例化Bean，直到第一次访问某个Bean 时才实例目标Bean；
而ApplicationContext 则在初始化应用上下文时就实例化所有单实例的Bean 。
ApplicationContext 支持国际化，
ApplicationContext事件机制是观察者设计模式的 实现，通过ApplicationEvent类和ApplicationListener接口，可以实现ApplicationContext事件处理。 
如果容器中有一个ApplicationListener Bean，每当ApplicationContext发布ApplicationEvent时，ApplicationListener Bean将自动被触发。
代码
public class EmailEvent extends ApplicationEvent
public class EmailNotifier implements ApplicationListener<ApplicationEvent>
    public void onApplicationEvent(ApplicationEvent event)
配置
<bean class="com.cxg.test.springPlatfrom.EmailNotifier" />  
测试
ApplicationContext applicationContext=new ClassPathXmlApplicationContext("application.xml");  
EmailEvent emailEvent = new EmailEvent("hello Spring!", "cxg@126.com", "This is SpringApplicatoinContext test!");  
applicationContext.publishEvent(emailEvent); //主动触发事件监视机制  
ApplicationContext 支持AOP（常用的是拦截器），
<mvc:interceptors>  
    <mvc:interceptor>  
        <mvc:mapping path="/**" />  
        <mvc:exclude-mapping path="/login" />  
        <mvc:exclude-mapping path="/logout" />  
        <!-- 定义在mvc:interceptor下面的表示是对特定的请求才进行拦截的 -->   
        <ref bean="validateSystemUserSessionInterceptor"  />  
    </mvc:interceptor>  
</mvc:interceptors> 

5. ApplicationContext 上下文的生命周期
Spring中ApplicationContext加载机制。 
加载器目前有两种选择：ContextLoaderListener和ContextLoaderServlet。 
在web.xml中增加： 
<listener> 
     <listener-class> 
          org.springframework.web.context.ContextLoaderListener
     </listener-class> 
</listener> 
或
<servlet> 
       <servlet-name>context</servlet-name> 
       <servlet-class> 
          org.springframework.web.context.ContextLoaderServlet 
       </servlet-class> 
       <load-on-startup>1</load-on-startup> 
</servlet>
通过以上配置，Web容器会自动加载/WEB-INF/applicationContext.xml初始化 
ApplicationContext实例，如果需要指定配置文件位置，可通过context-param加以指定： 
<context-param> 
       <param-name>contextConfigLocation</param-name> 
       <param-value>/WEB-INF/myApplicationContext.xml</param-value> 
</context-param> 
配置完成之后，即可通过 
WebApplicationContextUtils.getWebApplicationContext方法在Web应用中获取ApplicationContext引用。
spring为ApplicationContext提供的3种实现分别为：ClassPathXmlApplicationContext，FileSystemXmlApplicationContext和XmlWebApplicationContext，
其中XmlWebApplicationContext是专为Web工程定制的。
过程：
调用构造函数：分3步，分别是调用父类构造函数、setConfigLocations和refresh
其中调用父类构造函数super(parent)的作用是为容器设置Bean资源加载器（PathMatchingResourcePatternResolver）
调用AbstractRefreshableConfigApplicationContext的setConfigLocations(configLocations)的作用是设置Bean定义资源文件的路径（封装成Resource）
调用AbstractApplicationContext的refresh函数载入BeanDefinition
refresh()重点一：调用obtainFreshBeanFactory，容器开始载入Bean
    在这个方法中，先判断BeanFactory是否存在，如果存在则先销毁beans并关闭beanFactory，接着创建DefaultListableBeanFactory，并调用loadBeanDefinitions(beanFactory)装载bean
    调用了另一个重载函数loadBeanDefinitions(beanDefinitionReader)，委托给了XmlBeanDefinitionReader
    调用子类的获取Bean定义资源定位的方法（getConfigResources()），该方法在ClassPathXmlApplicationContext中实现，FileSystemXmlApplicationContext返回null
    由于FileSystemXmlApplicationContext的getConfigResources返回null，因此程序执行configLocations分支，调用XmlBeanDefinitionReader的父类AbstractBeanDefinitionReader的loadBeanDefinitions(String… locations)方法
    将指定位置的Bean定义资源文件解析为IoC容器封装的资源（Resource）的语句（实际上，调用了DefaultResourceLoader的getResource方法获取Resource。）
    回到XmlBeanDefinitionReader的loadBeanDefinitions方法
    元素转成map
最后close
    
6. Spring Bean 的生命周期?（在ApplicationContext 和 BeanFactory上下文的生命周期类似）
容器初始化
（ApplicationContext）调用BeanFactoryPostProcessor接口的postProcessBeanFactory()方法（此时为应用上下文装载配置文件之后，初始化Bean实例之前）
调用InstantiationAwareBeanPostProcessor接口的postProcessBeforeInstantiation()方法（此时为实例化Bean之前）
根据配置情况调用Bean构造函数或者工厂方法实例化Bean
调用InstantiationAwareBeanPostProcessor接口的postProcessAfterInstantiation()方法（此时为实例化Bean之后）
调用InstantiationAwareBeanPostProcessor接口的postProcessPropertyValues()方法（此时为实例化Bean之后）
调用Bean中的setXxx()方法设置属性值
调用BeanNameAware接口的setBeanName()方法，将配置文件的Bean名称设置到Bean中。
调用BeanFactoryAware接口的setBeanFactory()方法，将BeanFactory容器实例设置到Bean中。
（ApplicationContext）调用ApplicationContextAware接口的setApplicationContext()方法，将ApplicationContext实例设置到Bean中。
调用BeanPostProcessor后处理器接口的postProcessBeforeInitialization()方法对Bean进行加工操作。
调用InitializingBean接口的afterPropertiesSet()方法
bean标签init-method属性定义了初始化方法，则将执行这个方法
调用BeanPostProcessor后处理器接口的postProcessAfterInitialization()方法对Bean再次进行加工操作。
对于scope=”singleton”(默认),则将Bean放入springIOC容器的缓存池中，并将引用返回给调用者，spring继续对这些Bean进行后续的生命管理。
    对于scope=”prototype”,则将Bean返回给调用者，调用者负责Bean的后续生命的管理，spring不再管理这个Bean的生命周期。
对于scope=”singleton”(默认),容器关闭时，将触发spring对Bean的后续生命周期的管理。如果Bean实现了DisposableBean接口，则将调用接口的destroy()方法，可以编写释放日志，记录日志等。
对于scope=”singleton”(默认)的Bean，如果在bean标签中destory-method属性定义了Bean的销毁方法，则spring将执行这个方法。
以上生命周期所经历的方法可大致分为以下四类：
Bean自身的方法
Bean级生命周期接口方法
    包括BeanNameAware、BeanFactoryAware、InitializingBean和DisposableBean，这些接口方法由Bean直接实现
容器级生命周期接口方法
    包括InstantiationAwareBeanPostProcessor和BeanPostProcessor两个接口。
    一般称他们的实现类为后处理器，后处理器接口一般不由Bean本身实现，他们独立于Bean，实现类以容器附加装置的形式注册到Spring容器中，并通过接口反射为Spring容器扫描识别。
    当Spring容器创建任何Bean时，这些后处理器都会起作用，这些后处理器的影响是全局性的。
工厂后处理器接口方法
    包括AspectJWeavingEnabler、CustomAutowireConfigurer、ConfigurationClassPostProcessor等类，都实现了BeanFactoryPostProcessor接口。
    工厂后处理器也是容器级的，在应用上下文装配配置文件后立即调用。
Spring容器中还可以注册多个后处理器，只要他们都实现了Ordered接口，Spring容器就会按照特定的顺序依次调用这些后处理器。

7. Spring AOP的实现原理？
Spring AOP 基于代理（Proxy）的实现，有两种实现方式：
- 基于接口的动态代理(Dynamic Proxy)
- 基于子类化的CGLIB代理
在jdk1.3以后，提供了一个 API`java.lang.reflect.InvocationHandler`的类，
动态代理实现主要是实现`InvocationHandler`，并且将目标对象注入到代理对象中，利用反射机制来执行目标对象的方法。
```
public class MyInvocationHandler implements InvocationHandler
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable
SleepDao s=new SleepDaoImpl();
ClassLoader classLoader=s.getClass().getClassLoader();
MyInvocationHandler myInvocationHandler = new MyInvocationHandler(s);
SleepDao sd=(SleepDao) Proxy.newProxyInstance(classLoader, s.getClass().getInterfaces(), myInvocationHandler);
sd.sleep();
```
CGlib代理
```
public class CglibProxy implements MethodInterceptor 
    public Object intercept(Object proxy, Method method, Object[] args, MethodProxy arg3) throws Throwable
CglibProxy proxy=new CglibProxy();
Base base=(Base) proxy.getProxy(new Base());
base.sleep();
```
8. Spring 是如何管理事务的，事务管理机制？
Spring的事务机制包括声明式事务和编程式事务。
编程式事务管理：Spring推荐使用TransactionTemplate，实际开发中使用声明式事务较多。
声明式事务管理：将我们从复杂的事务处理中解脱出来，获取连接，关闭连接、事务提交、回滚、异常处理等这些操作都不用我们处理了，Spring都会帮我们处理。
Spring事务管理主要包括3个接口，Spring的事务主要是由他们三个共同完成的。
PlatformTransactionManager：事务管理器--主要用于平台相关事务的管理，主要有三个方法：
    commit  事务提交；
    rollback  事务回滚；
    getTransaction  获取事务状态。
TransactionDefinition：事务定义信息--用来定义事务相关的属性，给事务管理器PlatformTransactionManager使用，这个接口有下面四个主要方法：
    getIsolationLevel：获取隔离级别；
    getPropagationBehavior：获取传播行为；
    getTimeout：获取超时时间；
    isReadOnly：是否只读（保存、更新、删除时属性变为false--可读写，查询时为true--只读）
事务管理器能够根据这个返回值进行优化，这些事务的配置信息，都可以通过配置文件进行配置。
TransactionStatus：事务具体运行状态--事务管理过程中，每个时间点事务的状态信息。例如它的几个方法：
    hasSavepoint()：返回这个事务内部是否包含一个保存点，
    isCompleted()：返回该事务是否已完成，也就是说，是否已经提交或回滚
    isNewTransaction()：判断当前事务是否是一个新事务

9. Spring 的不同事务传播行为有哪些，干什么用的？
事务传播行为用来描述由某一个事务传播行为修饰的方法被嵌套进另一个方法的事务如何传播。
PROPAGATION_REQUIRED
如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是最常见的选择。
PROPAGATION_SUPPORTS
支持当前事务，如果当前没有事务，就以非事务方式执行。
PROPAGATION_MANDATORY
使用当前的事务，如果当前没有事务，就抛出异常。
PROPAGATION_REQUIRES_NEW
新建事务，如果当前存在事务，把当前事务挂起。
PROPAGATION_NOT_SUPPORTED
以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
PROPAGATION_NEVER
以非事务方式执行，如果当前存在事务，则抛出异常。
PROPAGATION_NESTED
如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。

10. Spring 中用到了那些设计模式？
9个？

11. Spring MVC 的工作原理？
springmvc请所有的请求都提交给DispatcherServlet,它会委托应用系统的其他模块负责负责对请求进行真正的处理工作。 
DispatcherServlet查询一个或多个HandlerMapping,找到处理请求的Controller. 
DispatcherServlet请请求提交到目标Controller 
Controller进行业务逻辑处理后，会返回一个ModelAndView 
Dispathcher查询一个或多个ViewResolver视图解析器,找到ModelAndView对象指定的视图对象 
视图对象负责渲染返回给客户端。 

12. Spring如何解决循环依赖？
循环依赖其实就是循环引用，也就是两个或则两个以上的bean互相持有对方，最终形成闭环。
Spring中循环依赖场景有：
    构造器的循环依赖
    field属性的循环依赖。
Spring的单例对象的初始化主要分为三步： 
    createBeanInstance：实例化，其实也就是调用对象的构造方法实例化对象
    populateBean：填充属性，这一步主要是多bean的依赖属性进行填充
    initializeBean：调用spring xml中的init 方法。
Spring为了解决单例的循环依赖问题，使用了三级缓存。
/** Cache of singleton objects: bean name --> bean instance */单例对象的cache
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(256);
/** Cache of early singleton objects: bean name --> bean instance */提前暴光的单例对象的Cache 
private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);
/** Cache of singleton factories: bean name --> ObjectFactory */单例对象工厂的cache 
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);
    分析getSingleton()的整个过程，Spring首先从一级缓存singletonObjects中获取。
    如果获取不到，并且对象正在创建中，就再从二级缓存earlySingletonObjects中获取。
    如果还是获取不到且允许singletonFactories通过getObject()获取，就从三级缓存singletonFactory.getObject()(三级缓存)获取
    然后从singletonFactories中移除，并放入earlySingletonObjects中。其实也就是从三级缓存移动到了二级缓存。
Spring解决循环依赖的诀窍就在于singletonFactories这个三级cache。
对于setter注入造成的依赖是通过Spring容器提前暴露刚完成构造器注入但未完成其他步骤（如setter注入）的Bean来完成的，而且只能解决单例作用域的Bean循环依赖。
在Spring IOC中对于singleton作用域Bean,可以通过setAllowCircularReferences(false);来禁用循环引用。
对于prototype作用域Bean(即非singleton)，Spring容器无法完成依赖注入，因为prototype作用域的Bean，Spring容器不进行缓存，因此无法提前暴露一个创建中的Bean。
因为加入singletonFactories三级缓存的前提是执行了构造器，所以构造器的循环依赖没法解决。只能抛出BeanCurrentlyInCreationException异常表示循环依赖。

13. Spring 如何保证 Controller 并发的安全？
Spring的Controller默认是Singleton的，这意味着每个request过来，系统都会用原有的instance去处理，
这样导致了两个结果:一是我们不用每次创建Controller，二是减少了对象创建和垃圾收集的时间;由于只有一个Controller的instance，
当多个线程调用它的时候，它里面的instance变量就不是线程安全的了，会发生窜数据的问题。
有几种解决方法：
    在Controller中使用ThreadLocal变量
    在spring配置文件Controller中声明 scope="prototype"，每次都创建新的controller
所在在使用spring开发web 时要注意，默认Controller、Dao、Service都是单例的。





