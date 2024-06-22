<!--
 * @Author: kekexili1230 457738564@qq.com
 * @Date: 2024-01-16 16:47:26
 * @LastEditors: kekexili1230 457738564@qq.com
 * @LastEditTime: 2024-01-16 22:12:32
 * @FilePath: /mynote/面试常见问题/spring.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
# spring常见问题

## AOP

### JDK动态代理和CGLIB动态代理的区别？

- JDK 动态代理：

    - 接口依赖： JDK 动态代理要求目标类必须实现一个或多个接口，代理对象将实现这些接口。

    - 代理对象生成： 使用 java.lang.reflect.Proxy 类和 java.lang.reflect.InvocationHandler 接口生成代理对象。Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) 方法用于创建代理对象。

    - 性能： 由于代理对象必须实现接口，因此 JDK 动态代理更适合于代理接口而非类。在性能方面，通常来说，JDK 动态代理相对较慢，因为它使用反射机制，对方法调用的处理需要额外的开销。

- CGLIB 动态代理：

    - 接口无关： CGLIB 动态代理不要求目标类实现接口，可以代理没有实现接口的类。

    - 代理对象生成： 使用 CGLIB 库生成字节码，创建目标类的子类作为代理对象。这种方式更加灵活，可以代理非接口类型的类。

    - 性能： 由于 CGLIB 使用字节码生成，它的性能通常比 JDK 动态代理更高。但在某些场景下，CGLIB 可能会因为生成子类而导致一些问题，比如 final 类或方法无法被代理。

### Spring AOP相关的术语

### Spring通知有哪些类型？执行顺序是？

- 单个切面正常情况的顺序

    - @Around

    - @Before

    - joinPoint.proceed(); 执行方法

    - @AfterReturning

    - @After

    - @Around

- 单个切面异常情况的顺序

    - @Around

    - @Before

    - joinPoint.proceed(); 执行方法

    - @AfterThrowing

    - @After

- 多个切面正常情况的顺序

    - @Order 值越小的越先执行，越先执行的最后才结束

## IOC

### 什么是IOC

IOC（Inversion of Control），中文通常翻译为“控制反转”，它还有一个别名叫做依赖注入（ Dependency Injection）。传统的程序设计中，应用程序通常控制对象的创建和管理，而在IoC中，控制权从应用程序转移到了外部容器，由容器负责管理和组织对象的创建、销毁以及依赖关系。

### IOC的优势？

- 松耦合： IOC 通过控制反转的机制，降低了组件之间的直接依赖关系。对象之间通过接口或抽象而不是具体的实现进行交互，减少了组件之间的耦合度。

- 集中管理： IOC 将对象的创建、组装和管理集中在容器中，提供了一种集中式的管理机制。

- 代码简洁： IOC 容器中，开发者只需关注业务逻辑的实现，而无需关心对象的创建和管理。

### BeanFactory和ApplicationContext有什么区别？

BeanFactory和ApplicationContext是Spring框架中两个关键的容器接口，它们之间存在一些区别，主要涉及功能、性能和初始化等方面：

- 功能差异：

    - BeanFactory： 是Spring框架的基础容器接口，提供了基本的IoC功能，负责对象的实例化、配置、组装和管理。它延迟加载（懒加载）Bean，即只有在需要获取Bean时才进行初始化。

    - ApplicationContext： 是BeanFactory的扩展，提供了更多的功能和特性，包括国际化（ I18n）信息支持、统一资源加载策略、容器内事件发布、多配置模块加载的简化。

- 性能差异：

    - BeanFactory： 由于采用延迟加载策略，相对来说初始化速度较快。但在大型应用中，由于只有在需要时才初始化Bean，可能会导致在应用运行时动态地加载Bean时稍微增加一些开销。

    - ApplicationContext： 在容器初始化时就完成了对所有Bean的实例化和依赖注入，因此在应用启动时可能会稍慢一些。但在应用运行时，由于已经预先初始化了Bean，获取Bean的速度相对较快。

- 初始化时机差异：

    - BeanFactory： 在需要时才进行初始化，采用懒加载的策略。这意味着在应用启动时，只有容器本身被创建，Bean的实例化和初始化过程将在第一次被请求时才执行。

    - ApplicationContext： 在容器初始化时就完成了对所有Bean的实例化和初始化，即使不使用某些Bean，它们也会在启动阶段被初始化。

- 适用场景：

    - BeanFactory： 适用于资源受限的环境，或者在大型应用中需要优化性能时。

    - ApplicationContext： 适用于大多数应用场景，特别是在需要更多高级特性和功能的情况下，如AOP、事件传播等。

### 统一的资源加载策略

- Resource：所有资源抽象和访问的接口

    - ClassPathResource: 该实现从Java应用程序的ClassPath中加载具体资源并进行封装

    - ByteArrayResource

    - FileSystemResource: 对java.io.File类型的封装

    - UrlResource: 通过java.net.URL进行的具体资源查找定位的实现类

    - InputStreamResource   

- ResourceLoader: 资源查找定位策略的统一抽


### @Autowired和@Resource的区别？

- 来源：

    - @Autowired： 是Spring框架提供的注解，用于自动装配Bean，通过类型进行匹配。

    - @Resource： 是Java EE规范中的注解，由Java EE容器提供，通过名称进行匹配。

- 匹配方式：

    - @Autowired： 根据类型进行自动装配，它会尝试将属性的数据类型与Spring容器中的Bean进行匹配。如果存在多个匹配的Bean，它将尝试按照属性名称或者@Qualifier注解进行匹配。

    - @Resource： 根据名称进行匹配，它首先会根据属性名称进行查找，如果找不到，则尝试使用 name 属性指定的名称进行匹配。如果都找不到，则按照类型进行匹配。

- 支持的注入方式：

    - @Autowired： 支持构造方法、成员变量、方法、以及方法参数的注入。可以用在方法、构造方法和字段上。

    - @Resource： 主要用于字段注入和方法注入。它不能用于构造方法注入，因为它没有构造方法级别的注入语义。

- 可选性：

    - @Autowired： 默认情况下，被注入的属性是必须存在的，否则会抛出异常。可以通过设置required属性为false来标记该属性为非必须的。

    - @Resource： 默认情况下，被注入的属性是必须存在的。可以通过设置 required 属性为 false 来标记该属性为非必须的。

- 支持的注入类型：

    - @Autowired： 除了支持Spring框架的自动装配外，还支持JSR-330的@Inject注解。

    - @Resource： 是Java EE规范中的注解，不仅可以在Java EE环境中使用，也可以在Spring中使用。但它不支持@Inject注解。