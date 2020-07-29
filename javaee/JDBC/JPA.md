# JPA

---

## JPA 相关概念

### ORM 框架

(Object Relational Mapping) 建立 Java 程序实体类与数据库表之间的映射关系。使用 ORM 框架进行编程 Java 程序会根据开发者配置，在运行时自动把数据对象持久化到数据库中，比直接使用 JDBC 编程更为方便和强大。

常见的 ORM 框架有 Hibernate, MyBatis 等。

### JPA 规范

(Java Persistence API) Java 程序和数据库连接的 Java EE 标准，本质上是一种 ORM 规范。使用户不必在 Java 程序中书写 SQL 语句就能直接把数据对象持久化到数据库中，由数据库厂商负责具体实现。

#### JDBC 和 JPA 的区别

- JDBC 是面向 SQL 的规范和接口，用户仍需要在 java 程序中书写 SQL 语句。
- JPA 是直接面向数据对象的规范和接口，可以通过直接操作对象来实现持久化，大大简化了操作的繁杂度。

P.S. Hibernate 是符合 JPA 规范的，而 MyBatis 却不符合，因为 MyBatis 还需要书写 SQL 语句。

https://www.jianshu.com/p/c14640b63653

---

## Spring JPA

Spring 框架中提供了对数据操作的框架 SpringData ； SpringData 框架下则提供了基于 JPA 标准操作数据的模块 SpringData JPA 。

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;


### 实体类

类

`@Entity` 声明实体类，自动对应数据库表（必选）

`@Table(name = "AUTH_USER")` 声明了数据库实体对应的表名，如果没有默认表名和实体名一致。

属性

`@Id` 声明属性对应数据库字段是主键。

`@Column(length = 32)` 用来声明实体属性的表字段的定义。
1. name - 属性对应数据库字段名，默认和属性名称一致。
2. length - 属性对应数据库字段长度，默认 255。
3. 属性对应数据库字段类型会自动推断。


```java
@Entity
@Table(name = "AUTH_USER")
public class UserDO {
    @Id
    private Long id;
    @Column(length = 32)
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

运行时，系统自动将数据表给我们建好了。


我们要实现一个增加、删除、修改、查询功能的持久层服务，那么我只需要声明一个接口，这个接口继承
org.springframework.data.repository.Repository<T, ID> 接口或者他的子接口就行。这里为了功能的完备，我们继承了 org.springframework.data.jpa.repository.JpaRepository<T, ID> 接口。其中 T 是数据库实体类，ID 是数据库实体类的主键。
然后再简单的在这个接口上增加一个 @Repository 注解就结束了。

```java
@Repository
public interface UserDao extends JpaRepository<UserDAO, id> {
}
```

 UserDO userDO = new UserDO();
        userDO.setId(1L);
        userDO.setName("风清扬");