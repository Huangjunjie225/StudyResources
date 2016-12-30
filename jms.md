#JMS

####1.JMS的消息结构
* 消息头（包含消息的识别信息和路由信息）
	* 1）JMSDestination 由send方法设置 消息发送的目的地 主要是指Queue和Topic 自动分配
	* 2）JMSDeliveryMode 由send方法设置 传送模式  持久模式和非持久模式
	* 3）JMSExpriation 由send方法设置 消息过期时间 
	* 4）JMSPriority 由send方法设置 消息优先级 默认是4级
	* 5）JMSMessageID 由send方法设置 唯一识别每个消息的标示
	* 6）JMSTimestamp 由客户端设置 消息发送和消费者实际接受的时间差
	* 7）JMSCorrelationID 由客户端设置 用来连接另外一个消息，典型的应用是在回复消息中连接到原来的消息
	* 8）JMSReplyTo 由客户端设置 提供本消息回复消息的目的地址
	* 9）JMSType	 由客户端设置 消息类型的识别符
	* 10）JMSRedelivered 重新传递 由JMS Provider设置
属性，消息体

* 消息体
	* 1）TextMessage,MapMessage,BytesMessage,StreamMessage,ObjectMessage



###broker 监控方式
> 1 localhost:8161/admin/
> 2 localhost:8161/hawtio/


###集成ActiveMQ和Tomcat 
1:修改web.xml
	<context-param>
		<param-name>brokerURI</param-name>
		<param-value>/WEB-INF/activemq.xml</param-value>
	</context-param>
	<listener>
		<listener-class>org.apache.activemq.web.SpringBrokerContextListener</listener-class>
	</listener>
2:增加WEB-INF/activemq.xml
	<beans
		xmlns="http://www.springframework.org/schema/beans"
		xmlns:amq="http://activemq.apache.org/schema/core"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.0.xsd
		 http://activemq.apache.org/schema/core http://activemq.apache.org/schema/core/activemq-core-5.2.0.xsd 
		 http://activemq.apache.org/camel/schema/spring http://activemq.apache.org/camel/schema/spring/camel-spring.xsd">
		
		<bean id="derby-ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	        <property name="url" value="jdbc:mysql://localhost/activemq?relaxAutoCommit=true"/>
	        <property name="username" value="root"/>
	        <property name="password" value="123456"/>
	        <property name="maxActive" value="200"/>
	        <property name="poolPreparedStatements" value="true"/>
    	</bean>

		<broker xmlns="http://activemq.apache.org/schema/core" brokerName="localhost">
		
			<persistenceAdapter>
				<kahaDB directory="/usr/local/testdata/kahadb"/>
			</persistenceAdapter>

		<!--
			<persistenceAdapter>
			 <jdbcPersistenceAdapter dataSource="#derby-ds"/>
			 <journaledJDBC useDatabaseLock="false"></journaledJDBC>
			</persistenceAdapter>
		-->


			<transportConnectors>
			 <transportConnector name="openwire"uri="tcp://localhost:61616"/>
			</transportConnectors>
		</broker>
		
	</beans>

3:在web应用下拷入activemq的jar包,在activemq下面的lib包 例如：
	cp -r lib /usr/... 目的地址的lib

4:在lib下面传入spring的包,就从前面的archlweb应用下面找spring的包就可以了

5:还需要xbean，这是apache的，可以从maven依赖的仓库里面找到

6:然后启动tomcat，进行测试


###什么时候使用ActiveMQ
1: 异步调用
2: 一对多通信(topic)
3: 做多个系统的集成，同构，异构
4: 作为RPC的替代(远程过程调用)
5: 多个应用相互解耦
6: 作为事件驱动架构的幕后支撑
7: 为了提高系统的可伸缩性

###性能优化
1: 网络拓扑结构
2: transpo 协议
3: service质量 topic 还是 queue 是否需要重新投递
4: 硬件，网络，jvm和操作系统
5: 生产者数量，消费者数量
6: 消息分发要经过的destination 数量

非持久化消息比持久化消息更快
	非持久化发送消息是异步的，producer不需要等待Consumer的receipt消息
	而持久化是要把消息先存储起来，然后再传递
	
尽量使用异步投递消息
	cf.setUseAsyncSend(true);
Transaction比Non-transaction更快
可以考虑内嵌启动broker，这样应用和broker之间可以使用vm协议通讯,速度快
尽量使用基于文件的消息存储方案，比如使用KahaDB方式

调整Prefetch Limit ,activeMQ默认的prefetch大小不同的
1：queue Consumer ->1000
2: queue browser consumer ->500
3: persistent topic consumer ->1000
4: non-persistent topic consumer ->32767

可以考虑生产者流量控制，可以通过xml配置，
	cf.setProducerWindowSize(1024000);

考虑关闭消息的复制功能
	ActiveMQConnectionFactory cd = ...
	cf.setCopyMessageOnSend(false);

调整tcp协议
	1 socketBufferSize: 默认65536
	2 tcpNoDelay:默认是false
	String url = "failover://(tcp://localhost:61616?tcpNoDelay=true)"
	ActiveMQConnectionFactory cf = new ActiveMQConnectionFactory(url);

消息投递和消息确认
	官方建议使用自动确认的模式，同时还应该开启优化确认的选项
	ActiveMQConnectionFactory cf = new ActiveMQConnectionFactory();
	cf.setOptimizeAcknowledge(true);