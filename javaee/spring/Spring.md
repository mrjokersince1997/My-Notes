# Spring

---

## 基本概念

### Spring

Spring 是用于开发 Java 应用程序的开源框架，为解决企业应用开发的复杂性而创建。

1. Spring 的基本设计思想是利用 IOC（依赖注入）和 AOP （面向切面）解耦应用组件，降低应用程序各组件之间的耦合度。
2. 在这两者的基础上，Spring 逐渐衍生出了其他的高级功能：如 Security，JPA 等。

### Spring MVC

Spring MVC 是 Spring 的子功能模块，专用于 Web 开发。

Spring MVC 基于 Servlet 实现，将 Web 应用中的数据业务、显示逻辑和控制逻辑进行分层设计。开发者可以直接调用 Spring MVC 框架中 Spring 解耦的组件，快速构建 Web 应用。

### Spring Boot

Spring Boot 是用于简化创建 Spring 项目配置流程，快速构建 Spring 应用程序的辅助工具。Spring Boot 本身并不提供 Spring 框架的核心特性以及扩展功能。但 在创建 Spring 项目时，Spring Boot 可以：

1. 自动添加 Maven 依赖，不需要在 pom.xml 中手动添加配置依赖。
2. 不需要配置 XML 文件，将全部配置浓缩在一个 appliaction.yml 配置文件中。
3. 自动创建启动类，代表着本工程项目和服务器的启动加载。
4. 内嵌 Tomcat 、Jetty 等容器，无需手动部署 war 文件。

---

## Spring Boot 配置

### 依赖

在Spring Boot中，引入的所有包都是 starter 形式：

spring-boot-starter-web-services，针对 SOAP Web Services
spring-boot-starter-web，针对 Web 应用与网络接口
spring-boot-starter-jdbc，针对 JDBC
spring-boot-starter-data-jpa，基于 Hibernate 的持久层框架
spring-boot-starter-cache，针对缓存支持


### 默认映射路径

- `classpath:/META-INF/resources/`
- `classpath:/resources/`
- `classpath:/static/` 
- `classpath:/public/`

优先级顺序：META-INF/resources > resources > static > public


### 全局配置

位于 resources 文件夹下，支持以下两种格式。由 Spring Boot 自动加载。

1. application.properties
2. application.yml

```properties
#端口号
server.port=8080
#访问前缀
server.servlet.context-path=/demo

#数据库驱动
jdbc.driver=com.mysql.jc.jdbc.Driver
#数据库链接
jdbc.url=jdbc:mysql://localhost:3306/demo?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC
#数据库用户名
jdbc.username=root
#数据库密码
jdbc.password=wdh19970506

#Mybatis
#配置文件路径
mybatis_config_file=mybatis-config.xml
#SQL语句配置路径
mapper_path=/mapper/**.xml
#实体类所在包
type_alias_package=com.example.demo.entity
```

- JDBC 连接 Mysql5 驱动： com.mysql.jdbc.Driver
- JDBC 连接 Mysql6 驱动： com.mysql.cj.jdbc.Driver , URL 必须要指定时区 serverTimezone ！


**多重配置**

在 Spring Boot 中，我们往往需要配置多个不同的配置文件去适应不同的环境：

- `application-dev.properties` 开发环境
- `application-test.properties` 测试环境
- `application-prod.properties` 生产环境

只需要在程序默认配置文件 `application.properties` 中设置环境，就可以使用指定的配置。

```properties
spring.profiles.active=dev
```

### 启动类


 `@SpringBootApplication` 类：作为程序入口，在创建 Spring Boot 项目时自动创建。

 等同于 `@Configuration` + `@EnableAutoConfiguration` + `@ComponentScan` ，会自动完成配置并扫描路径下所有包。

```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

---

Spring 需要定义调度程序 servlet ，映射和其他支持配置。我们可以使用 web.xml 文件或 Initializer 类来完成此操作：

```java
public class MyWebAppInitializer implements WebApplicationInitializer {
  
    @Override
    public void onStartup(ServletContext container) {
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.setConfigLocation("com.pingfangushi");
          container.addListener(new ContextLoaderListener(context));
          ServletRegistration.Dynamic dispatcher = container
          .addServlet("dispatcher", new DispatcherServlet(context));
        dispatcher.setLoadOnStartup(1);
        dispatcher.addMapping("/");
    }
}
```
还需要将 `@EnableWebMvc` 注释添加到 `@Configuration` 类，并定义一个视图解析器来解析从控制器返回的视图：

```java
@EnableWebMvc
@Configuration
public class ClientWebConfig implements WebMvcConfigurer { 
   @Bean
   public ViewResolver viewResolver() {
      InternalResourceViewResolver bean
        = new InternalResourceViewResolver();
      bean.setViewClass(JstlView.class);
      bean.setPrefix("/WEB-INF/view/");
      bean.setSuffix(".jsp");
      return bean;
   }
}
```










