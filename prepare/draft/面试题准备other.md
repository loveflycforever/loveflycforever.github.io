RMI协议RMI协议采用JDK标准的java.「mi/实现，采用阻塞式短连接和JDK标准序列化方式,Java标准的远程调用协议。连接个数:多连
接连接方式:短连接传输协议:TCP传输方式:同步传输序列化:Java标准
二进制序列化适用范围:传入传出参数数据包大小混合,消费者与提供
者个数差不多,可传文件。适用场景:常规远程服务方法调用,与原生
RMI服务互操作
6.Hessian协议Hessian协议用于集成Hessian的服务,Hessian底层采
用Http通讯,采用SerdMet暴露服务,Dubbo缺省内嵌Jetty作为服务
器实现基于Hessian的远程调用协议。连接个数:多连接连接方式:短
连接传输协议:HTTP传输方式:同步传输序列化:Hessian二进制序列化
适用范围:传入传出参数数据包较大,提供者比消费者个数多，提供者
压力较大,可传文件。适用场景:页面传输,文件传输，或与原生
hessian服务互操作
http采用Spring的Httplnvoker实现基于http表单的远程调用协议。
连接个数:多连接连接方式:短连接传输协议:HTTP传输方式:同步传输
序列化:表单序列化(JSON)适用范围:传入传出参数数据包大小混合，提
供者比消费者个数多,可用浏览器查看,可用表单或URL传入参数,
暂不支持传文件。适用场景:需同时给应用程序和浏览器JS使用的服
务。
8.Webservice基于CXF的frontend-simple和transports-http实现基于
WebService的远程调用协议。连接个数:多连接连接方式:短连接传输
协议:HTTP传输方式:同步传输序列化:SOAP文本序列化适用场景:系统
集成，跨语言调用
9.Thrif Thrift是Facebook捐给Apache的一个RPC框架，当前dubbo支
持的thrit协议是对thrit原生协议的扩展,在原生协议的基础上添加了
些额外的头信息,比如service name,magic number等



2.如果没有明确指定:
若Synchronized修饰的方法为非静态方法,表示此方法对应的对象为锁
对象;
若Synchronized修饰的方法为静态方法,则表示此方法对应的类对象为
锁对象.
注意,当一个对象被锁住时,对象里面所有用Synchronized修饰的方法
都将产生堵塞,而对象里非Synchronized修饰的方法可正常被调用,不
受锁影响

