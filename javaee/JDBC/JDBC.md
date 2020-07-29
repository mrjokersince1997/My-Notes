# JDBC

---

## JDBC 简介

JDBC 是 Java EE 提供的数据库接口，负责连接 java 程序和后台数据库。安装数据库驱动程序后，开发者可以按照 JDBC 规范直接在 Java 程序上对数据库进行操作，由数据库厂商负责具体实现。


### 驱动安装

1. 下载 MySQL 驱动包，解压后得到 jar 库文件：http://dev.mysql.com/downloads/connector/j/
	
2. 打开 IDE，在对应项目中 configure build path 导入 jar 库文件。

---

## JDBC 编程

JDBC 常用工具类位于 sql 包内，使用时需导入：`import java.sql.*` 。使用时可能 抛出 SQLException 异常。

### 加载驱动

JDBC 首先要使用反射机制加载驱动类，并创建其对象。

```java
Class.forName("com.mysql.cj.jdbc.Driver");          // MySQL 数据库驱动
Class.forName("oracle.jdbc.driver.OracleDriver");   // Oracle 数据库驱动
```

### 连接数据库 Connection

JDBC 由 Connection 类负责连接数据库，参数中输入数据库 URL、账号、密码。

```java
// 连接本地 RUNOOB 数据库，需设置时区
static final String DB_URL = "jdbc:mysql://localhost:3306/RUNOOB?useSSL=false&serverTimezone=UTC";

static final String USER = "root";            // 数据库账号
static final String PASS = "123456";          // 数据库密码

Connection conn = DriverManager.getConnection(DB_URL,USER,PASS);    // 建立连接
conn.close();                                                       // 关闭连接
```

### 执行语句 Statement

JDBC 由 Statement 类负责发送 SQL 语句。

```java
Statement stmt = = conn.createStatement();         // 创建 Statement 对象

// executeQuery 执行查询操作，返回 ResultSet 结果集
ResultSet rs = stmt.executeQuery("SELECT * FROM websites"); 
// executeUpdate 执行更新操作，返回 int 数据表示受影响行数
int len = stmt.executeUpdate("DELETE * FROM websites"); 
     
stmt.close();                                      // 关闭 Statement 对象
```

### 返回查询结果 ResultSet

JDBC 由 ResultSet 类返回 select 语句执行结果，读取 executeQuery 方法返回的数据。

```java
ResultSet rs = stmt.executeQuery(sql);             // 获取返回结果
               
while(rs.next()){                                  // 输出返回结果
    System.out.println(rs.getString("area_id"));    
}
```

---

## JDBC 进阶

### 预编译 PreparedStatement

PreparedStatement 类继承自 Statement 类，在 JDBC 开发中用来取代前者。有以下两个优势：

1. 可对 SQL 语句进行预编译，可以灵活地修改 SQL 语句，提高开发效率。 
2. 把用户输入单引号转义，防止恶意注入，保护数据库安全。

```java
Connection connection = DriverManager.getConnection();
String sql = "INSERT INTO test(id,name) VALUES (?,?)";
PreparedStatement stmt = connection.preparedStatement(sql);   // 创建对象并预编译
stmt.setInt(1, 755);                                          // 在第一个占位符(?)位置插入数字
stmt.setString(2, "MrJoker");                                 // 在第二个占位符(?)位置插入字符串
stmt.executeUpdate();                                         // 更新并执行
```

### 批处理 executeBath

PreparedStatement 类可以通过 executeBath 方法批量处理 SQL 语句，进一步提高效率。其返回值为一个 int[] 数组。

```java
Connection connection = DriverManager.getConnection();
String sql = "INSERT INTO test(id,name) VALUES (?,?)";
PreparedStatement stmt = connection.prepareStatement(sql);
for (int i = 1; i <= 1000; i++) {
    stmt.setInt(1, i);
    stmt.setString(2, (i + "号士兵"));
    stmt.addBatch();                                           // 语句添加到批处理序列中
}
preparedStatement.executeBatch();                              // 语句发送给数据库批量处理
preparedStatement.clearBatch();                                // 清空批处理序列
```


### 大文本和二进制数据

- clob 用于存储大文本

- blob用于存储二进制数据

---

## JDBC 示例

```java
// 适用于 JDK 1.8 以后版本

import java.sql.*; 

public class MySQLTest{

    static final String JDBC_DRIVER = "com.mysql.cj.jdbc.Driver";  
    static final String DB_URL = "jdbc:mysql://localhost:3306/RUNOOB?useSSL=false&serverTimezone=UTC";
    static final String USER = "root"; 
    static final String PASS = "123456";

    public static void useMethod（）{
        Connection conn = null;                  
        PreparedStatement stmt = null;                    
        try{
            Class.forName(JDBC_DRIVER);                                
            conn = DriverManager.getConnection(DB_URL,USER,PASS);    
            stmt = conn.preparedStatement("SELECT id, name, url FROM websites");  
            ResultSet rs = stmt.executeQuery();        
            while(rs.next()){
                System.out.println(rs.getString("area_id"));   
            }
            rs.close(); 
            stmt.close(); 
            conn.close();                    
        }catch(SQLException se){         // 处理 JDBC 错误
            se.printStackTrace(); 
        }catch(Exception e){             // 处理 Class.forName 错误
            e.printStackTrace(); 
        }finally{                                                 
            try{ 
                if(stmt != null) stmt.close(); 
            }catch(SQLException se2){}
            try{
                if(conn != null) conn.close(); 
            }catch(SQLException se){} 
        }
     }
}
```
