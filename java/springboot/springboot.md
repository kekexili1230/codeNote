<!--
 * @Author: kekexili1230 457738564@qq.com
 * @Date: 2024-01-11 10:30:00
 * @LastEditors: kekexili1230 457738564@qq.com
 * @LastEditTime: 2024-01-17 17:53:09
 * @FilePath: /mynote/springboot.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
# springboot笔记

## 什么是POJO

POJO通常只包含简单的Java类，不继承特定的基类或实现特定的接口，也不依赖于特定的框架或技术

## IOC

### IOC的基本概念

IOC（Inversion of Control），中文通常翻译为“控制反转”，它还有一个别名叫做依赖注入（ Dependency Injection）。可以从以下几点理解IOC：

- 控制权反转：传统的程序设计中，应用程序通常控制对象的创建和管理，而在IoC中，控制权从应用程序转移到了外部容器，由容器负责管理和组织对象的创建、销毁以及依赖关系。

- 依赖注入：对象的依赖关系不再由对象自己来创建，而是由外部容器来注入

- 解耦合：IoC有助于降低组件之间的耦合度。由于对象的创建和管理由容器负责，组件之间不再直接相互依赖，而是通过容器来管理它们的关系。

- 容器：在IoC中，通常有一个容器（如Spring容器）负责管理对象的生命周期和依赖关系。容器通过读取配置信息或使用注解等方式来了解应用程序的组件，并在需要时实例化和注入这些组件

### 依赖注入的方法

- 构造器注入

- setter方法注入

- 接口注入

### IOC service provider

- 对象的创建：IOC service provider将对象的构建逻辑从客户端对象剥离

- 对象的绑定：IOC掌握的对象，客户端请求掌握的对象，IOC需要知道两者之间的对应关系，方式包括如下（spring中）：

    - 直接编码：编码向容器中注入实例。注册时提供类名和实例；使用时根据类名查询

    - 外部配置文件：xml，properties文件

        - BeanDefinitionFReader：读取配置文件，生成BeanDefinition

        - BeanDefinition: 每一个类在容器中都会有一个对应的BeanDefinition，包括：对象的class类型，构造函数参数，是否是抽象类。当客户向BeanFactory提出请求时，根据这些信息返回一个对象实例。

        - BeanDefinitionRegistry：BeanDefinitionRegistry接口抽象了Bean的注册逻辑

        - BeanFactory接口只定义如何访问容器内管理的Bean的方法

        - DefaultListableBeanFactory实现 BeanFactory 和 BeanDefinitionRegistry接口，具体负责Bean的注册和管理接口。

    - 注解

### BeanFactory生命周期

容器在对象进入scope之前创建对象，实例在实例离开scope之后销毁对象。

三种类型的Scope:

- singleton(单例)

    - 对象存活时间：和容器的生命周期一致

    - 对象实例数量：一个容器中只有一个共享实例 

    - 适用场景：业务无关的工具类，配置类

- prototype(原型)

    - 对象存活时间：客户端负责容器的声明周期管理，容器不负责

    - 对象实例数量：每次请求，容器生成一个新的对象返回给客户端

    - 适用场景：由状态的对象

- session(会话)

    - 对象存活时间：

    - 对象实例数量：

### IOC容器的实现

- 容器启动阶段：

    - 加载配置

    - 分析配置信息

    - 生成BeanDefinition

    - 注册到BeanDefinitionRegistry
    
- Bean实例化阶段


## 注解

### 依赖注入注解

- @Autowired 按照类型进行匹配

- @Qualifier byname进行匹配

- @Resource 是jsr中定义的主机，通过byname方式匹配

### bean扫描注解

- @Configuration: 声明该类是一个配置类，配置类用于定义Bean

    - @Bean: 在配置类中，使用 @Bean 注解的方法用于声明 Spring Bean

    - @ComponentScan：将扫描指定路径下使用特定注解的类，生成BeanDefinition。这些注解包括

        - @Component：所有

        - @Service: 服务层

        - @Controller：控制器层

        - @Repository：dao层

        - 注解参数包括：basePackages、includeFilters 和 excludeFilters

### 配置文件注解

- @Value($(x.y.z))

- @ConfigurationProperties(prefix) 该注解应用到java类上，通过将prefix和类的属性名组成全限定名称到配置文件中查找


## AOP

### AOP相关概念

- joinpoint: 表示织入点的位置

- pointcut: joinpoint的表述方法；将横切逻辑织入到系统中，需要参考pointcut的描述信息，才知道需要往那些joinpoint上织入横切逻辑。

- Advice: Advice是一个横切关注点逻辑的载体，它代表将织入到joinpoint的横切逻辑。根据Advice再joinpoint执行位置的不同，分为：

    - before advice：通常做些初始化工作，设置初始值，获取系统资源等

    - after advice

        - after return advice：joinpoint处的流程正常执行后

        - after throw advcie: joinpoint处的流程抛出异常后

        - after advice：类似finnal操作，无论joinpoint正常还是异常后

    - Around advcie：在joinpoint之前和之后执行逻辑

    - Aspect是对系统中的横切关注点逻辑进行模块化封装的AOP概念实体。可以包含多个pointcut和advice

    - 织入和织入器

    - 目标对象

### spring AOP的实现机制



## springBoot

### spring和springBoot的异同

### xml还是注解

- 业务类使用注解

- 配置类（例如数据库、第三方资源）使用xml

### springBoot的优点

- 创建独立的spring应用程序

- 嵌入式的Tomcat, Jetty, 无需部署war文件

- 允许通过maven根据需要获取starter

- 尽可能自动配置spring

### Spring Boot Starter

Spring Boot Starter是Spring Boot框架中的一种特殊的依赖项，它是一组预定义的依赖关系集合，旨在简化项目的构建和配置。
Starter通常用于引入特定功能的依赖，例如spring-boot-starter-web是用于构建Web应用程序的Starter，它会引入一组常见的Web依赖，包括嵌入式的Web服务器（如Tomcat、Jetty或Undertow）和Spring MVC框架

### springboot参数配置加载顺序

- 命令行参数

- java的系统属性（通过System.getProperties()）

- 操作系统环境变量

- application.properties

    - 当前目录: ./config/application.properties
    
    - 当前目录: ./application.properties

    - classpath: ./config/application.properties

    - classpath: ./application.properties

    - 如果同级目录存在 application-{profile}文件，则profile文件优先级高

    - 如果同级目录存在 同名yaml和properties文件，则properties文件优先级高。

### springBoot IOC

1. 在spring IOC 中bean都是默认以单例形式存在


### Bean的声明周期

- 资源定位：通过@Configuration定义Bean

- 保存Bean定义：将Bean的定义保存到BeanDefinition实例中

- IOC容器装载Bean定义：将BeanDefinition存放到IOC容器中

- 创建Bean对象：创建Bean实例

- 依赖注入

- setBeanName：接口BeanNameAware实现

- setBeanFactory：接口BeanfactoryAware实现，用于让Bean感知自己所属的BeanFactory

- setApplicationContext: 接口ApplicationContextAware实现

- PostConstruct: pring 容器在实例化 bean 之后，会在调用构造函数后立即调用这个方法。

- 生存周期

- destroy：接口DisposableBean方法，Spring 在容器关闭或销毁 bean 的时候，会调用 destroy 方法

### BeanPostProcesser

#### BeanPostProcesser的作用

BeanPostProcessor的两个方法中都传入了原来的对象实例的引用，通常比较常见的使BeanPostProcessor的场景，是处理标记接口实现类，或者为当前对象提供代理实现。

#### 在springboot中如何自定义BeanPostProcesser

- 创建自定义BeanPostProcesser类

```java

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class CustomBeanPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        // 在Bean初始化之前的处理逻辑
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        // 在Bean初始化之后的处理逻辑
        return bean;
    }
}

```

- 在配置类中注册自定义BeanPostProcessor：

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public CustomBeanPostProcessor customBeanPostProcessor() {
        return new CustomBeanPostProcessor();
    }
}

```



### Bean的作用域

- singleton

    - 默认的作用域，表示在整个Spring容器中只存在一个Bean实例

    - Bean会在容器启动时被创建，并在容器关闭时被销毁

- prototype

    - 表示每次请求该Bean时，容器都会创建一个新的实例

    - 由用户销毁

- request

    - 表示在每个HTTP请求中，容器会创建一个新的Bean实例。

    - spring在请求结束时销毁

- session

    - 表示在每个用户会话中，容器会创建一个新的Bean实例

    - spring在会话结束时销毁


### AOP

配置多个Aspect，优先级分别为：A1, A2, A3, 如果作用在同一个joinpoint上，则调用顺序是：

A1 Before
A2 Before
A3 Before
A3 after
A2 after
A1 after

## 事务

### 什么是事务

事务是对数据资源进行访问的一组操作。事务有四个特性：ACID

- 原子性

- 一致性

- 隔离性

- 持久性

### spring中的事务机制

- 声明式事务管理：

    - 基于注解

    - 基于xml

- 编程式事务管理

### @Transactional 的配置项

- value（transactionManager）：事务管理器，事务的打开、回滚、提交由事务管理器控制

- propagation：传播行为

- isolation：隔离级别

- timeout：超时时间

- readonly：只读事务

- rollbackfor ：发生指定异常时回滚

- rollbackforclassname: 方法在发生指定异常名称时回滚

### 事务的隔离级别

### 事务的传播行为

- REQUIRED: 默认的传播行为，如果存在当前事务，就沿用当前事务; 否则新建一个事务。

- SUPPORTS: 支持事务，如果当前存在事务，就沿用当前事务；不存在，则采用无事务的方法

- MANDATORY: 必须使用事务，如果当前没有事务，则会抛出异常

- REQUIRES_NEW：无论当前事务是否存在，都会创建新事物。并与当前事务完全隔离

- NOT_SUPPORT: 不支持事务，当前存在事务，则挂起事务执行

- NEVER: 不支持事务，如果当前方法存在事务，则抛出异常

- NESTED: 当前方法调用子方法时，如果子方法发生异常，只回滚子方法执行过的sql，而不回滚当前方法的事务。

### NESTED和REQUIRED_NEW 的区别

- REQUIRED_NEW（独立的新事务）：

    - 如果当前没有事务，则创建一个新的事务。

    - 如果当前存在事务，则将当前事务挂起，创建一个新的独立事务，新事务与外部事务无关。

    - 外部事务的状态对内部事务没有影响，内部事务的提交或回滚也不会影响外部事务。

    - 内部事务的提交和回滚都是独立于外部事务的。

- NESTED（嵌套事务）：

    - 如果当前没有事务，则创建一个新的事务。

    - 如果当前存在事务，则在当前事务的内部开启一个嵌套事务。

    - 但外部事务的回滚会导致内部事务的回滚。

    - 如果内部事务发生了异常回滚，只会回滚到内部事务的保存点，而不会影响到外部事务。

### @Transactional 注解什么时候会失效

- 注解在非公有方法

- self-invocation

- final方法

- 代码捕获异常，而非@Transactional

## springboot的启动过程



### springboot中有那些设计模式

- 工厂模式：BeanFactory和ApplicationContext创建中用到

- 代理模式：spring AOP 中利用代理模式

- 策略模式：AOP中根据代理对象的不同，使用不同的代理实现方式；根据资源的不同，使用不同的资源加载方式，classPathResource，FileSystemResource，UrlResource

- 单例模式：创建Bean的时候

- 适配器模式：MVC中的adapter

- 观察者模式：spring中的ApplicationEvent，ApplicationListener

### RequestMapping的作用

- @RequestMapping 是Spring Framework中用于映射Web请求到处理方法的注解。它可以用在类级别和方法级别，用于定义请求映射规则

### @ResponseBody的作用

- @ResponseBody 是Spring框架中的一个注解，用于指示方法的返回值应该直接写入HTTP响应体中，而不是通过视图解析器进行解析渲染

###  @RequestMapping 和 @GetMapping 注解有什么不同？

- @RequestMapping 注解在类和方法上，@GetMapping注解在方法上

- @RequestMapping支持get，post，put，delete等方法； @GetMapping 是 @RequestMapping 的 GET 请求方法的特例


### 什么是RESTful？


RESTful（Representational State Transfer）是一种软件架构风格，主要遵循如下原则：

- 资源：在REST中所有的东西都被抽象为资源，每一个资源都有唯一的标识符，通常是URI

- 表现层状态转化：表示客户端和服务器之间的交互是通过资源的表现形式进行的。

    - 资源的状态：源的状态是指资源在特定时刻的属性、数据、关系等信息

    - 资源的表现形式：资源的状态以及与之相关的操作所包含的信息。通常用json或者xml根式表示

- 无状态性：RESTful 架构是无状态的，这意味着每个请求都包含了客户端需要的全部信息。每一个请求都是独立的

- 统一接口：统一接口是RESTful架构的另一个关键原则，它定义了客户端和服务器之间的通信标准。统一接口包含了一组规范，包括资源标识符、资源操作（例如GET、POST、PUT、DELETE等HTTP方法）、表示形式（资源的表现形式）和状态转换（客户端和服务器之间的交互）等。

