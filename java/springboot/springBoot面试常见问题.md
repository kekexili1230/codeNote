# springboot面试常见问题

## springboot的优点

Spring Boot 是基于 Spring 框架的一个项目，旨在简化和加速 Spring 应用程序的开发和部署。以下是 Spring Boot 的一些优点：

- 简化配置： Spring Boot采用了约定优于配置的理念，通过默认配置和自动配置，减少了开发者需要手动配置的工作。这使得项目的配置更加简单和易于理解。

- 内嵌式容器： Spring Boot 内置了常见的 Servlet 容器（如Tomcat、Jetty、Undertow），可以将应用程序打包成可执行的 JAR 文件，无需外部容器。这简化了部署和维护过程。

- 自动配置： Spring Boot 根据项目的依赖自动配置应用程序。开发者只需引入相关的依赖，Spring Boot 将自动完成大部分配置，减少了样板代码的编写。

- 快速开发： Spring Boot 提供了一组开箱即用的特性和插件，如 Spring Initializer，可通过简单的 Web 界面或命令行工具初始化项目。这加速了项目的启动和开发。

- 集成测试支持： Spring Boot 提供了丰富的测试支持，包括单元测试、集成测试和端到端测试。它对测试注解的支持使得测试变得更加简单和可维护。

- 微服务架构： Spring Boot 非常适合构建微服务架构，其模块化的设计和对 Spring Cloud 的支持使得微服务的开发和管理更加容易。

- 生态系统： Spring Boot建立在强大的Spring框架之上，继承了 Spring 的丰富生态系统，包括数据访问、安全、消息队列、任务调度等各种功能。

- 监控和管理： Spring Boot 提供了许多监控和管理功能，如健康检查、度量指标、远程调试等，有助于更好地了解应用程序的运行状况。

- 社区支持： 由于其流行性和活跃的社区，Spring Boot 提供了广泛的文档、教程和支持。开发者可以方便地获取帮助和分享经验。

口诀"贱内快动，去测试生态服务"

## spring，springmvc, springboot，springcloud有何区别

- Spring Framework:

    - 定位： Spring Framework 是整个 Spring 生态系统的基石，提供了一个全面的、模块化的解决方案，用于构建企业级的 Java 应用程序和服务。
    
    - 主要功能： 提供了依赖注入（DI）、面向切面编程（AOP）、事务管理、数据访问、模型视图控制（MVC）等核心功能。它包括了多个模块，如Spring Core、Spring AOP、Spring JDBC、Spring ORM 等。

- Spring MVC:

    - 定位： Spring MVC 是 Spring 框架的一部分，用于构建基于模型-视图-控制器（MVC）设计模式的 Web 应用程序。

    - 主要功能： 提供了 Web 开发所需的一系列组件，包括控制器、模型、视图解析器等。通过使用注解或 XML 配置，开发者可以轻松地构建灵活、可扩展的 Web 应用程序。

- Spring Boot:

    - 定位： Spring Boot 是基于 Spring 框架的约定大于配置和快速开发原则的一个项目，旨在简化 Spring 应用程序的搭建和开发。

    - 主要功能： 自动配置、开箱即用的功能集成、嵌入式 Web 服务器（如Tomcat、Jetty、Undertow）、约定优于配置等。Spring Boot 提供了 Spring 应用程序的快速开发和简化配置的能力，使得开发者能够更专注于业务逻辑而不是繁琐的配置。

- Spring Cloud:

    - 定位： Spring Cloud 是用于构建分布式系统和微服务架构的 Spring 项目群。

    - 主要功能： 提供了一系列工具和框架，用于解决微服务体系结构中的共性问题，如服务发现、配置管理、负载均衡、断路器、分布式消息等。Spring Cloud 构建在 Spring Boot 之上，使得在微服务架构中快速实施常见的分布式系统模式变得更加容易。

总的来说，Spring Framework 是整个生态系统的基础，Spring MVC 专注于 Web 应用程序的开发，Spring Boot 简化了 Spring 应用程序的开发和部署，而 Spring Cloud 提供了构建和管理微服务体系结构所需的工具和模块。这些模块可以单独使用，也可以结合使用，根据应用程序的需求选择合适的模块。

## jar包和war包的区别

- 用途：

    - JAR包： 主要用于打包和传递Java类、资源文件和库。它通常用于普通的Java应用程序，包含了应用程序的所有类和相关资源。

    - WAR包： 主要用于打包和部署Web应用程序。它包含了Web应用程序的所有内容，如JSP文件、HTML文件、Servlet类、配置文件、静态页面、标签库等。

- 部署环境：

    - JAR包： 适用于独立的Java应用程序，可以在命令行或其他执行环境中直接运行。

    - WAR包： 用于部署到Java Web服务器，如Tomcat、Jetty等。Web服务器能够解析WAR包并正确地将其中的内容部署为一个Web应用程序。

## Spring Boot 打成的 jar 和普通的 jar 有什么区别?

- Spring Boot 打包成的 JAR 文件：

    - 可执行性： Spring Boot JAR 文件是可执行的，可以通过 java -jar 命令直接运行。这是因为 Spring Boot 默认包含了一个嵌入式的 Servlet 容器（如Tomcat、Jetty、Undertow）。

    - 内嵌式容器： Spring Boot JAR 文件包含了一个嵌入式的 Servlet 容器，不需要额外安装和配置外部的 Web 服务器。

    - 依赖管理： Spring Boot JAR 文件通常包含了所有依赖，形成了一个自包含的单元，不需要额外的类路径配置。

    - 默认配置： Spring Boot 提供了默认的配置，减少了对于一些基本配置的需求，开发者可以通过配置文件进行定制。

    - Spring Boot 插件： Spring Boot 通常使用构建工具的插件（如Maven或Gradle）来构建 JAR 文件，这些插件会自动处理依赖、资源文件、配置等。

- 普通的 JAR 文件：

    - 不可执行性： 普通的 JAR 文件通常不是可执行的，它们没有包含嵌入式的 Servlet 容器，因此无法直接运行。

    - 需要外部容器： 如果想要在 Web 环境中运行普通的 JAR 文件，需要依赖外部的 Servlet 容器，例如 Tomcat。

    - 手动配置： 普通的 JAR 文件需要手动配置类路径，包括依赖的 JAR 包、资源文件等。

    - 依赖管理： 需要手动管理依赖，确保所需的 JAR 包在类路径上。

总体而言，Spring Boot JAR 文件更适合构建独立的、自包含的、可执行的应用程序，而普通的 JAR 文件更适合作为库或模块被其他应用程序引用。选择哪种打包方式取决于项目的特定需求和部署环境。如果你希望构建一个独立的可执行应用程序，并且不依赖外部容器，那么使用 Spring Boot JAR 文件是更为方便的选择。

## spring-booter-starter的作用是？

在Spring Boot中，Starter（启动器）是一种特殊的依赖项，它提供了一种方便的方式来引入一组相关的依赖项，以支持特定的功能或技术栈。Starter通常被设计成“约定大于配置”的方式，以简化项目的依赖管理和配置。以下是Starter的一些主要特点和作用：

- 集成依赖项： Starter将一组常用的、相关的依赖项打包在一起，使得开发者无需手动引入大量的依赖项。这有助于避免版本冲突和简化配置。

- 默认配置： Starter通常包含了一组默认的配置，以支持特定的功能。这使得开发者可以快速启动一个具备基本功能的应用程序，而无需手动进行繁琐的配置。

- 自动配置： Spring Boot的Starter通常伴随着自动配置，即自动根据应用程序的依赖项和配置来配置Spring容器。这样可以减少开发者的配置工作，提高开发效率。

- 模块化： Spring Boot的Starter是模块化的，你可以根据项目的需要选择合适的Starter，而无需引入整个技术栈。这样保证了项目只引入了真正需要的依赖。

- 简化开发： 通过使用Starter，开发者可以更专注于业务逻辑而不必过多关注底层的技术栈和配置。这有助于降低学习曲线，使得新手也能够快速上手。

## Spring Boot 的核心注解是哪个？

启动类上面的注解是@SpringBootApplication，它也是 Spring Boot 的核心注解，主要组合包含了以下 3 个注解：

- @SpringBootConfiguration：组合了 @Configuration 注解，实现配置文件的功能。

- @EnableAutoConfiguration：打开自动配置的功能，也可以关闭某个自动配置的选项，如关闭数据源自动配置功能： @SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })。

- @ComponentScan：Spring组件扫描。

## springboot中的常用注解

- 核心注解：
    
    - @SpringBootApplication： 用于标识主程序类，通常位于项目的根包下，它是一个复合注解，包括了 @Configuration、@EnableAutoConfiguration、@ComponentScan。

    - @RestController： 标识一个类为 RESTful 控制器，其方法返回的数据直接写入 HTTP 响应体。

    - @Controller： 标识一个类为 Spring MVC 控制器。

    - @Service： 标识一个类为服务层组件。

    - @Repository： 标识一个类为数据访问组件，通常用于 DAO 类。

    - @Component： 泛指 Spring 管理的组件。

- 依赖注入：

    - @Autowired： 用于进行自动装配，可以标注在字段、构造函数、Setter 方法上。

    - @Qualifier： 与 @Autowired 配合使用，用于指定具体的注入 bean 的名称。

    - @Value： 用于注入简单的数值、字符串等，从配置文件中读取。

- 请求处理与Web开发：

    - @RequestMapping： 处理请求的映射，用于类和方法级别，用于指定 URL 和 HTTP 方法。

    - @GetMapping、@PostMapping、@PutMapping、@DeleteMapping： 简化的 HTTP 方法映射注解。

    - @RequestParam： 用于将请求参数绑定到方法参数。

    - @PathVariable： 用于将 URI 模板变量绑定到方法参数。

    - @RequestBody： 用于将 HTTP 请求体绑定到方法参数，通常用于接收 JSON 或 XML 格式的请求体。

- 数据访问：

    - @Entity、@Table： 用于定义 JPA 实体类和表映射。

    - @Repository： 标识一个类为 Spring Data Repository，用于数据访问层。

- Spring Boot 配置：

    - @Configuration： 声明当前类为配置类，通常与 @Bean 配合使用。

    - @Bean： 在配置类中声明一个 Bean。

    - @PropertySource： 指定配置文件的位置。

    - @ConfigurationProperties： 用于绑定配置文件中的属性到 Java 对象。

- 事务管理：
    
    - @Transactional： 标识一个方法为事务性方法。

- 其他常用注解：

    - @ComponentScan： 配置 Spring 扫描组件的基础包。

    - @Conditional： 根据条件判断是否创建 Bean。

    - @Profile： 根据环境配置选择不同的 Bean。

这只是一些常用的注解，实际上 Spring Boot 提供了更多的注解来支持不同的场景和需求。注解的具体使用取决于你的应用程序的架构和功能需求。
