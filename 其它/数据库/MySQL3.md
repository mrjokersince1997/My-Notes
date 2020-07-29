# MySQL 优化

---

## 分表设计

1. MySQL 数据库限制每个表最多存储 4096 列，并且每一行数据大小不能超过 65535 字节。
2. 数据量到达百万级以上时，会导致修改表结构、备份、恢复都有非常大的困难。
3. 数据量越大，装载进内存缓冲池时所占用的内存也就越大，缓冲池无法一次性装载时就会频繁进行磁盘 IO ，大大降低查询速率。

因此当表数据量过大时，就要进行分表操作：

- 水平分表：数据项分开存储。
- 垂直分表：按字段拆分。

### 分表原则

1. 经常一起使用的数据放到一个表中，避免更多的关联操作。

2. 尽量做到冷热数据分离，减小表的宽度，减少磁盘 IO，保证热数据的内存缓存命中率（表越宽，）；更有效的利用缓存，，避免读入无用的冷数据；
   

### 范式

对表进行逻辑拆分的准则。范式级别越高，存储数据冗余越小，但相应表之间的关系也越复杂导致难以维护。一般使用第三范式。

- **1NF** 第一范式 关系数据库一定符合条件

- **2NF** 第二范式 不能产生部分依赖（非主键字段不能被主键中部分字段唯一确定）

- **3NF** 第三范式 不能存在传递依赖（非主键字段不能被其它非主键字段唯一确定）

---

## 语句设计

### 语句查询

- **查询语句执行次数**

```sql
-- 查询数据库各类型语句执行次数
mysql> SHOW [SESSION] STATUS;                      -- 当前连接
mysql> SHOW GLOBAL STATUS;                         -- 数据库开启后

mysql> SHOW SESSION STATUS LIKE 'Com_insert%';     -- 查询插入语句执行次数
mysql> SHOW GLOBAL STATUS LIKE 'Innodb_rows_%';    -- Innodb 专用，查询影响行数
```

- **查询当前执行语句**

```sql
mysql> SHOW PROCESSLIST;            -- 查看数据库所有连接信息，包含正在执行的 SQL 语句


mysql> SHOW PROFILES;                           -- 查看当前连接执行的所有指令：ID 和 执行时间
mysql> SHOW PROFILE FOR QUERY 5;                -- 显示第 5 条指令执行的具体信息                
mysql> SHOW PROFILE CPU FOR QUERY 5;            -- 显示第 5 条指令各步骤的执行时间
```

- **解释语句执行方式**

```sql
mysql> EXPLAIN 具体语句;                 -- 解释语句执行的状况（重要）
```

### 语句优化


1. fileSort 排序，没有索引时利用文件系统排序，效率低。
2. index 排序，如果通过索引能直接返回数据，效率高。（只能返回有索引的字段）


对于 fileSort 排序，增大排序区大小满足排序需求，可以提高排序效率。

对语句的优化，主要就是对于索引的运用。





1. 避免使用子查询，可以把子查询优化为 join 操作。子查询不能利用索引。

2. 对应同一列进行 or 判断时，使用 in 代替 or。in 的值不要超过 500 个，in 操作可以更有效的利用索引，or 大多数情况下很少能利用到索引。


分组时，默认先排序后分组。会生成临时表。


1. 通过 ORDER BY null 不排序直接分组。
2. 上索引，有索引不临时。


#### LIMIT 分页查询

使用连表查询

```sql
SELECT * FROM student ORDER BY id LIMIT 2000000, 10;        # 浪费时间，排序 2000000 条后筛选
SELECT * FROM student s, (SELECT id FROM student ORDER BY id LIMIT 2000000, 10) t WHERE s.id = t.id;
```


## 优化方式

### 插入操作

#### 批量插入

注意：txt文件各个字段间，要用一个"table"键的距离隔开。一行只写一条数据。批量执行文本中的 SQL 语句。

```
mysql ->load data infile 'E:/student.txt' into table student;
```


1. 按主键顺序插入更高效！生成有序 txt 更好。
2. 关闭唯一性校验：`SET UNIQUE CHECK = 0` ，导入完成后记得开启。
3. 事务提交，手动提交事务：`SET AUTOCOMMIt = 0` ，导入完成后记得开启。









---

## 批量执行

### 导入导出


1
mysqldump -uroot -pMyPassword databaseName tableName1 tableName2 > /home/foo.sql
mysqldump -u 用户名 -p 数据库名 数据表名 > 导出的文件名和路径 

导出整个数据库

1
mysqldump -u root -p databaseName > /home/test.sql   (输入后会让你输入进入MySQL的密码)
mysql导出数据库一个表，包括表结构和数据
mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名和路径

1
mysqldump -u root -p databaseName tableName1 > /home/table1.sql
如果需要导出数据中多张表的结构及数据时，表名用空格隔开

1
mysqldump -u root -p databaseName tableName01 tableName02 > /home/table.sql
仅导出数据库结构

1
mysqldump -uroot -pPassWord -d databaseName > /home/database.sql
仅导出表结构

1
mysqldump -uroot -pPassWord -d databaseName tableName > /home/table.sql
将语句查询出来的结果导出为.txt文件
1
mysql -uroot -pPassword database1 -e "select * from table1" > /home/data.txt
　　

数据导入
常用source 命令 
进入mysql数据库控制台，mysql -u root -p 
mysql>use 数据库 
使用source命令，后面参数为脚本文件(.sql) 

1
mysql>source /home/table.sql

### 存储过程

将大批量、经常重复执行的 SQL 语句集合预存储在数据库里，外部程序可以直接调用，减少了不必要的网络通信代价。

---

## SQL 安全

### SQL 注入


服务器向数据库发送的 SQL 语句往往包含用户输入：

```sql
-- 登录验证

SELECT * FROM users WHERE id = ' 用户输入1 ' and password = ' 用户输入2 ';
```

如果攻击者在用户输入中插入 `'`、`or`、`#` ，就可以改变 SQL 语句的功能。达到想要的目的。

```sql
-- 返回 alice 用户信息，登录 alice 账号
SELECT * FROM users WHERE id = ' alice'# ' and password = ' 用户输入2 ';
-- 返回全部用户信息，通常会默认登录首个账号（管理员）
SELECT * FROM users WHERE id = ' ' or 1# ' and password = ' 用户输入2 ';
SELECT * FROM users WHERE id = ' ' or '1 ' and password = ' 用户输入2 ';     -- AND 优先级高，先执行 AND 再执行 OR
 -- 直接删库
SELECT * FROM users WHERE id = ' '; DROP TABLE users;#' ' and password = ' 用户输入2 ';
```


在执行攻击时，攻击者可能需要知道数据库表信息，譬如表名，列名等。

1. 通过错误信息发现（输入错误语句获取）：在进行开发的时候尽量不要把出错信息打印到页面上，使用专门的错误页。
2. 通过盲注发现：

比如在用户输入中插入以下字符（如果表名首字母 ASCII 大于 97 则休眠 5s），就可以根据数据返回时间判断数据库表信息。

```sql
SELECT * FROM users WHERE id = ' alice' and if((select ascii(substr((select table_name from information_schema.tables where table_schema= database() limit 0,1),1,1)))>97,sleep(5),1) # ' and password = ' 用户输入2 ';
```


### SQL 注入防御

(prepare_statement) 为避免 SQL 注入攻击，新版本后端语言(PHP/Java) 都支持对输入 SQL 语句预处理：会自动检查用户输入并对单引号用`\`做强制转义， MySQL 数据库收到转义后的单引号也会用 setString 方法做转义处理。

学习网站开发的人就再也不用担心sql注入的威胁了。

---


### 内存优化

InnoDB 用一块内存区做缓存池，既缓存数据也缓存索引。

linux mysql 配置文件 user-my.cnf

innodb_buffer_pool_size = 512M  （默认128m）

innodb_log_buffer_size  日志缓存大小，过于小会频繁写入磁盘

### 日志管理

---








