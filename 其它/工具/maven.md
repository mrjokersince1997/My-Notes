# Maven


---

## 第三方文件

### Libraries 库

在 Java 项目开发中，我们需要大量导入其他开发者已经完成的 Java 文件供自己使用。

为便于开发，我们把所有导入的第三方文件放入项目的 Libraries 库中， Java 程序通过 `import` 语句可以直接调用。

*指定 JDK 版本和文件储存位置后，JDK 文件也保存在 Libraries 库中。*

### JAR 文件

(Java Archive File) Java 的一种文档格式。实际是 Java 文件压缩后的 ZIP 文件，又名文件包。

Libraries 库里的第三方文件都是以 jar 文件形式保存，Java 程序导入时会自动解析其中的 Java 代码并使用。

---

## Maven

### Maven 功能

项目管理工具，为项目自动加载需要的 Jar 包。

用户只需要在配置文件中注明所需要的第三方文件和路径，Maven 会自动将 JAR 文件导入到项目的 Libraries 库中。

### Maven 配置

在 IDEA 的 Settings/Maven 中，可以对 Maven 进行配置：

1. **Maven 安装位置**：默认为 IDEA 自带，可配置为本地安装。
2. **XML 配置文件位置**
3. **Libraries 库位置**

![mavenidea](mavenidea.PNG)

### XML 文件

Maven 采用 XML 配置文件来注明项目所需要的第三方文件和路径。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <artifactId>stream-platform</artifactId>
        <groupId>com.company.stream</groupId>
        <version>1.1.1-SNAPSHOT</version>
    </parent>

    <groupId>com.company.stream</groupId>
    <artifactId>stream-platform-server</artifactId>

    <properties>
        <datatables.version>1.10.16</datatables.version>
        <font-awesome.version>4.7.0</font-awesome.version>
        <bootstrap.version>3.3.7</bootstrap.version>
        <jquery.version>3.2.1</jquery.version>
        <locator.version>0.32</locator.version>

        <mysql-connector.version>6.0.6</mysql-connector.version>
        <mysql.version>8.0.13</mysql.version>
        <c3p0.version>0.9.5.2</c3p0.version>

        <mybatis.version>1.3.1</mybatis.version>
        <lombok.version>1.16.20</lombok.version>

        <hystrix.version>1.5.12</hystrix.version>
        <hibernate-validator.version>6.0.9.Final</hibernate-validator.version>
        <javax-validation.version>2.0.0.Final</javax-validation.version>
        <company.jigsaw.version>2.1.2</company.jigsaw.version>
        <company.config.client.version>3.13.0</company.config.client.version>

        <generator.version>1.3.5</generator.version>
        <tinypinyin.version>2.0.3</tinypinyin.version>
        <jsqlparser.version>0.9.5</jsqlparser.version>
        <modelmapper.version>1.1.0</modelmapper.version>

        <javax.mail.version>1.4.7</javax.mail.version>

        <swagger.version>2.2.2</swagger.version>
        <zkclient.version>0.10</zkclient.version>
        <jedis.version>2.8.1</jedis.version>

        <spring.boot.version>2.1.6.RELEASE</spring.boot.version>
    </properties>

    <repositories>
        <repository>
            <id>libs-release</id>
            <name>libs-release</name>
            <url>http://jfrog.cloud.company.domain/libs-release</url>
            <releases><enabled>false</enabled></releases>
            <snapshots><enabled>false</enabled></snapshots>
        </repository>
        <repository>
            <id>libs-release-company-maven-release</id>
            <name>libs-release-company-maven-release</name>
            <url>http://jfrog.cloud.company.domain/company-maven-cloudservice</url>
            <releases><enabled>false</enabled></releases>
            <snapshots><enabled>false</enabled></snapshots>
        </repository>
        <repository>
            <id>libs-snapshot</id>
            <name>libs-snapshot</name>
            <url>http://jfrog.cloud.company.domain/libs-snapshot</url>
            <releases><enabled>false</enabled></releases>
            <snapshots><enabled>true</enabled></snapshots>
        </repository>
    </repositories>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring.boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>redis.clients</groupId>
                <artifactId>jedis</artifactId>
                <version>${jedis.version}</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.version}</version>
            </dependency>
            <dependency>
                <groupId>com.netflix.hystrix</groupId>
                <artifactId>hystrix-core</artifactId>
                <version>${hystrix.version}</version>
            </dependency>
            <dependency>
                <groupId>com.mchange</groupId>
                <artifactId>c3p0</artifactId>
                <version>${c3p0.version}</version>
            </dependency>
            <dependency>
                <groupId>org.webjars</groupId>
                <artifactId>font-awesome</artifactId>
                <version>${font-awesome.version}</version>
            </dependency>
            <dependency>
                <groupId>org.webjars</groupId>
                <artifactId>bootstrap</artifactId>
                <version>${bootstrap.version}</version>
            </dependency>
            <dependency>
                <groupId>org.webjars</groupId>
                <artifactId>jquery</artifactId>
                <version>${jquery.version}</version>
            </dependency>
            <dependency>
                <groupId>org.webjars</groupId>
                <artifactId>webjars-locator</artifactId>
                <version>${locator.version}</version>
            </dependency>
            <dependency>
                <groupId>org.webjars</groupId>
                <artifactId>datatables</artifactId>
                <version>${datatables.version}</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql-connector.version}</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
            </dependency>
            <dependency>
                <groupId>com.github.promeg</groupId>
                <artifactId>tinypinyin</artifactId>
                <version>${tinypinyin.version}</version>
            </dependency>
            <dependency>
                <groupId>com.github.promeg</groupId>
                <artifactId>tinypinyin-lexicons-java-cncity</artifactId>
                <version>${tinypinyin.version}</version>
            </dependency>
            <dependency>
                <groupId>com.101tec</groupId>
                <artifactId>zkclient</artifactId>
                <version>${zkclient.version}</version>
                <exclusions>
                    <exclusion>
                        <groupId>org.slf4j</groupId>
                        <artifactId>slf4j-log4j12</artifactId>
                    </exclusion>
                </exclusions>
            </dependency>
            <dependency>
                <groupId>javax.mail</groupId>
                <artifactId>mail</artifactId>
                <version>${javax.mail.version}</version>
            </dependency>
            <dependency>
                <groupId>com.fasterxml.jackson.dataformat</groupId>
                <artifactId>jackson-dataformat-yaml</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>org.modelmapper</groupId>
                <artifactId>modelmapper</artifactId>
                <version>${modelmapper.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>junit</groupId>
                    <artifactId>junit</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.restdocs</groupId>
            <artifactId>spring-restdocs-mockmvc</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.session</groupId>
            <artifactId>spring-session-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-yaml</artifactId>
        </dependency>
        <dependency>
            <groupId>org.modelmapper</groupId>
            <artifactId>modelmapper</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
        </dependency>
        <dependency>
            <groupId>javax.mail</groupId>
            <artifactId>mail</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
        </dependency>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
        </dependency>
        <dependency>
            <groupId>javax.inject</groupId>
            <artifactId>javax.inject</artifactId>
            <version>1</version>
        </dependency>
        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-library</artifactId>
            <version>2.11.7</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
        </dependency>
        <dependency>
            <groupId>com.squareup.okhttp3</groupId>
            <artifactId>okhttp</artifactId>
        </dependency>
        <dependency>
            <groupId>com.101tec</groupId>
            <artifactId>zkclient</artifactId>
        </dependency>
        <dependency>
            <groupId>com.company.config</groupId>
            <artifactId>config-client</artifactId>
        </dependency>
    </dependencies>

    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <version>${spring.boot.version}</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>repackage</goal> 
                            </goals>
                        </execution>
                    </executions>
                </plugin>
                <plugin>
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-maven-plugin</artifactId>
                    <version>${generator.version}</version>
                    <configuration>
                        <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
                        <verbose>true</verbose>
                        <overwrite>true</overwrite>
                    </configuration>
                    <dependencies>
                        <dependency>
                            <groupId>mysql</groupId>
                            <artifactId>mysql-connector-java</artifactId>
                            <version>${mysql.version}</version>
                        </dependency>
                    </dependencies>
                </plugin>
            </plugins>
        </pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

