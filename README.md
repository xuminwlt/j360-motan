# Motan Demos

# Overview
Motan is a remote procedure call(RPC) framework for rapid development of high performance distributed services.

# Features
- Create distributed services without writing extra code.
- Provides cluster support and integrate with popular service discovery services like [Consul][consul] or [Zookeeper][zookeeper]. 
- Supports advanced scheduling features like weighted load-balance, scheduling cross IDCs, etc.
- Optimization for high load scenarios, provides high availability in production environment.

# Quick Start

The quick start gives very basic example of running client and server on the same machine. For the detailed information about using and developing Motan, please jump to [Documents](#documents).

> The minimum requirements to run the quick start are: 
>  * JDK 1.7 or above
>  * A java-based project management software like [Maven][maven] or [Gradle][gradle]

1. Add dependency to pom

   ```xml
    <dependency>
        <groupId>com.weibo</groupId>
        <artifactId>motan-core</artifactId>
        <version>0.0.1</version>
    </dependency>
    <dependency>
        <groupId>com.weibo</groupId>
        <artifactId>motan-transport-netty</artifactId>
        <version>0.0.1</version>
    </dependency>

    <!-- dependencies blow were only needed for spring-based features -->
    <dependency>
        <groupId>com.weibo</groupId>
        <artifactId>motan-springsupport</artifactId>
        <version>0.0.1</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.2.4.RELEASE</version>
    </dependency>
   ```

2. Create an interface for both service provider and consumer.

    `src/main/java/quickstart/FooService.java`  

    ```java
    package quickstart;

    public interface FooService {
        public String hello(String name);
    }
    ```

3. Implement service provider.


    `src/main/java/quickstart/FooServiceImpl.java`
    
    ```java
    package quickstart;
    
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;
    
    public class FooServiceImpl implements FooService {
    
        public String hello(String name) {
            System.out.println(name + " invoked rpc service");
            return "hello " + name;
        }
    
        public static void main(String[] args) throws InterruptedException {
            ApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath:motan_server.xml");
            System.out.println("server start...");
        }
    }
    ```
    
    `src/main/resources/motan_server.xml`
    
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
    	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    	xmlns:motan="http://api.weibo.com/schema/motan"
    	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
       http://api.weibo.com/schema/motan http://api.weibo.com/schema/motan.xsd">

        <!-- service implemention bean -->
        <bean id="serviceImpl" class="quickstart.FooServiceImpl" />
        <!-- exporting service by motan -->
        <motan:service interface="quickstart.FooService" ref="serviceImpl" export="8002" />
    </beans>
    ```
    
    Execute main function in FooServiceImpl will start a motan server listening on port 8002.

4. The service consumer

    `src/main/resources/motan_client.xml`

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:motan="http://api.weibo.com/schema/motan"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
       http://api.weibo.com/schema/motan http://api.weibo.com/schema/motan.xsd">

        <!-- reference to the remote service -->
        <motan:referer id="remoteService" interface="quickstart.FooService" directUrl="localhost:8002"/>
    </beans>
    ```

    `src/main/java/quickstart/Client.java`

    ```java
    package quickstart;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;


    public class Client {
    
        public static void main(String[] args) throws InterruptedException {
            ApplicationContext ctx = new ClassPathXmlApplicationContext("classpath:motan_client.xml");
            FooService service = (FooService) ctx.getBean("remoteService");
            System.out.println(service.hello("motan"));
        }
    }
    ```
    
    Execute main function in Client will invoke the remote service and print response.


# Documents

* [Wiki](https://github.com/weibocom/motan/wiki)
* [Wiki(中文)](https://github.com/weibocom/motan/wiki/zh_overview)
