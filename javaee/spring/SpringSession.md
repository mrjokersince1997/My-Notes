# Spring Session

## Session 共享问题

在 Web 项目开发中，Session 会话管理是一个很重要的部分，用于存储与记录用户的状态或相关的数据。

通常情况下 session 交由容器（tomcat）来负责存储和管理，但是如果项目部署在多台 tomcat 中，则 session 管理存在很大的问题：

1. 多台 tomcat 之间无法共享 session ，当负载均衡跳转到其它 tomcat 时，session 就失效了，用户就退出了登录。

2. 一旦 tomcat 容器关闭或重启也会导致 session 会话失效。

## Spring Session 简介

Spring Session 是 Spring 家族中的一个子项目，Spring Session 提供了用于管理用户会话信息的 API 和实现。

它把 servlet 容器实现的 httpSession 替换为 spring-session ，Session 信息存储在 Redis 或其它数据库中统一管理，解决了 session 共享的问题。


## Spring Session 实现

SessionRepositoryFilter 类是一个 Filter 过滤器，符合 Servlet 的规范定义，用来修改包装请求和响应。这里负责包装切换 HttpSession 至 Spring Session 的请求和响应。

在登录/登出时调用 session.setAttritube 和 session.removeAttritube 方法时，将切换为对 redis 中的 session 进行修改。

## Spring Session 实现

### 导入依赖


```xml
<!-- Spring Boot Redis 依赖  -->
	<dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-starter-data-redis</artifactId>
	</dependency>
    
    <dependency>  
        <groupId>org.springframework.session</groupId>  
        <artifactId>spring-session-data-redis</artifactId>  
	</dependency>   
	
	<dependency>
	    <groupId>org.springframework.session</groupId>
	    <artifactId>spring-session-core</artifactId>
	</dependency>
```


### 配置文件

配置文件 `application.properties`

```properties
# Spring Session 配置
# 数据源
spring.session.store-type=redis
# redis 刷新模式
spring.session.redis.flush-mode=on_save
# redis 命名空间
spring.session.redis.namespace=test_session

# session 过期时间
server.servlet.session.timeout=3600s

# Redis 配置
# Redis数据库索引
spring.redis.database=0
# Redis服务器地址
spring.redis.host=192.168.0.1
# Redis服务器连接端口
spring.redis.port=6379
# Redis服务器连接密码
spring.redis.password=1234
```




> 参考链接 https://www.cnblogs.com/lxyit/p/9672097.html