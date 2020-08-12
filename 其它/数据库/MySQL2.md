# MySQL 指令

---

## 基本概念

### SQL 指令

SQL 指令是用于访问和处理数据库的标准的计算机语言。对于 MySQL 等常用数据库都可以通过使用 SQL 访问和处理数据系统中的数据。

### 注意事项

1. SQL 对大小写不敏感。
2. 标识符应避免与关键字重名！可用反引号（`）为标识符包裹。
3. 注释
    - 单行注释： `# 注释内容`
    - 多行注释： `/* 注释内容 */`
    - 单行注释： `-- 注释内容` 
4. 模式通配符
    - 匹配任意单个字符： `_`
    - 匹配任意数量字符，包括 0 个：`%`
    - 单引号需要进行转义： `\'` 
5. 清除已有语句：`\c`

---

## 服务指令

### 启动/终止服务

```bash
net start mysql           # 启动本机 MySQL 运行
net stop mysql            # 终止本机 MySQL 运行
```

### 连接/断开服务

MySQL 服务运行时，输入连接指令即可连接 MySQL 数据库。

需要输入的属性分别为 (h)IP 地址、(P)端口号、(u)用户名、(p)密码。 端口号若为 3306 可省略，密码可空缺。

```bash
# 本地连接
mysql -h localhost -u root -p 

# 远程连接
mysql -h 10.0.0.51 -P 3306 -u root -p 123456

# 断开连接
mysql> exit
mysql> quit
mysql> /p
```

---

## 管理指令

### 用户管理

MySQL 数据库的全部用户信息保存在 `mysql 库 / user 表`内，用户含有以下属性：

- **user 属性**：用户名
- **host 属性**：允许用户登入的网络
- **authentication_string 属性**：密码

#### 增删改查

能够对用户进行增删改查操作，需要当前用户拥有非常高的数据库权限。

```sql
-- 增加用户(CREATE)
mysql> CREATE USER 'boy'@'localhost' IDENTIFIED BY '';                -- 创建用户 boy 允许从本地网络登录
mysql> CREATE USER 'girl'@'10.0.0.%' IDENTIFIED BY '123456';          -- 创建用户 girl 允许从特定网络登录

-- 删除用户(DROP)
mysql> DROP USER 'girl'@'10.0.0.%';

-- 修改用户(ALTER)
mysql> ALTER USER 'boy'@'localhost' IDENTIFIED BY '123456';

-- 重命名用户(RENAME)
mysql> RENAME USER 'boy'@'localhost' TO 'man'@'localhost';

-- 设置密码
mysql> SET PASSWORD = PASSWORD('123456');                              -- 为当前用户设置密码
mysql> SET PASSWORD FOR 'boy'@'localhost' = PASSWORD('123456');        -- 为指定用户设置密码

-- 查询全部用户信息(DESC/SELECT)
mysql> DESC mysql.user;                                            
mysql> SELECT user,host,authentication_string FROM mysql.user     
```


#### 权限管理

用户权限分为非常多种，包括全局权限、库权限、表权限、列权限等。

```sql

-- 赋予权限(GRANT)
mysql> GRANT SELECT,INSERT ON *.*             -- 赋予用户选择插入权限（所有库的所有表）
    -> TO 'boy'@'localhost'                   -- 不存在将新建用户
    -> IDENTIFIED BY '123456'                 
    -> WITH GRANT OPTION;                     -- （可选）允许用户转授权限

-- 撤消权限(REVOKE)
mysql> REVOKE INSERT ON *.*
    -> FROM 'boy'@'localhost';

-- 查看权限
mysql> SELECT Host,User,Select_priv,Grant_priv
    -> FROM mysql.user
    -> WHERE User='testUser';
```



### 数据库管理

MySQL 内划分为多个互相独立的数据存储区域，调用数据库指令时必须提前声明要使用的数据库。

- **数据库选项信息**

属性|含义|备注
-|-|-
CHARACTER SET|编码方式|默认为 utf8mb4
COLLATE|校对规则|默认为 utf8mb4_general_ci

```sql
-- 查看所有数据库
mysql> SHOW DATABASES;

-- 进入/切换数据库
mysql> USE mydb;

-- 查看当前数据库
mysql> SELECT DATABASE();

-- 创建数据库
mysql> CREATE DATABASE [IF NOT EXISTS] mydb;
mysql> CREATE DATABASE [IF NOT EXISTS] mydb CHARACTER SET utf8mb4;

-- 删除数据库
mysql> DROP DATABASE [IF EXISTS] mydb;

-- 查看数据库选项信息
mysql> SHOW CREATE DATABASE mydb;

-- 修改数据库选项信息
mysql> ALTER DATABASE mydb CHARACTER SET utf8;
```

### 表管理

- **表属性**

属性|含义|备注
-|-|-
CHARSET|字符集|默认使用数据库字符集
ENGINE|存储引擎|默认为 InnoDB
DATA DIRECTORY| 数据文件目录|
INDEX DIRECTORY|索引文件目录|
COMMENT|表注释|

*如果表标记为 TEMPORARY 则为临时表，在连接断开时表会消失。*

- **列属性**

属性|含义|备注
-|-|-
PRIMARY KEY | 主键 | 标识记录的字段。可以为字段组合，不能为空且不能重复。
INDEX | 普通索引 | 可以为字段组合，建立普通索引。
UNIQUE| 唯一索引 | 可以为字段组合，不能重复，建立唯一索引。
NOT NULL | 非空 | （推荐）不允许字段值为空。
DEFAULT | 默认值 | 设置当前字段的默认值。
AUTO_INCREMENt | 自动增长 | 字段无需赋值，从指定值（默认 1）开始自动增长。表内只能存在一个且必须为索引。
COMMENT| 注释 | 字段备注信息。
FOREIGN KEY | 外键 | 该字段关联到其他表的主键。默认建立普通索引。

#### 表操作

```sql
-- 查看所有表
mysql> SHOW TABLES;

-- 创建表
mysql> CREATE [TEMPORARY] TABLE [IF NOT EXISTS] student
       (
           id INT(8) PRIMARY KEY AUTO_INCREMENT=20190001,
           name VARCHAR(50) NOT NULL,
           sex INT COMMENT 'Male 1，Female 0',
           access_time DATE DEFAULT GETDATE(),
           major_id INT FOREIGN KEY REFERENCES major(id) 
       )ENGINE=InnoDB;

mysql> CREATE TABLE grade
       (
           student_id INT,
           course_id INT,
           grade INT,
           PRIMARY KEY (student_id,course_id),
           CONSTRAINT fk_grade_student FOREIGN KEY (student_id) REFERENCES student(id),
           CONSTRAINT fk_grade_course FOREIGN KEY (course_id) REFERENCES course(id)
       );

-- 删除表
mysql> DROP TABLE [IF EXISTS] student;

-- 清空表数据（直接删除表，再重新创建）
mysql> TRUNCATE [TABLE] student;

-- 查看表结构
mysql> SHOW CREATE TABLE student;
mysql> DESC student;

-- 修改表属性
mysql> ALTER TABLE student ENGINE=MYISAM;

-- 重命名表
mysql> RENAME TABLE student TO new_student;
mysql> RENAME TABLE student TO mydb.new_student;      

-- 复制表
mysql> CREATE TABLE new_student LIKE student;                  -- 复制表结构
mysql> CREATE TABLE new_student [AS] SELECT * FROM student;    -- 复制表结构和数据


-- 检查表是否有错误
mysql> CHECK TABLE tbl_name [, tbl_name] ... [option] ...
-- 优化表
mysql> OPTIMIZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...
-- 修复表
mysql> REPAIR [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ... [QUICK] [EXTENDED] [USE_FRM]
-- 分析表
mysql> ANALYZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...
```

#### 列操作

```sql
-- 添加字段
mysql> ALTER TABLE student ADD [COLUMN] age INT;               -- 默认添加在最后一行
mysql> ALTER TABLE student ADD [COLUMN] age INT AFTER sex;     -- 添加在指定字段后
mysql> ALTER TABLE student ADD [COLUMN] age INT FIRST;         -- 添加在第一行

--修改字段
mysql> ALTER TABLE student MODIFY [COLUMN] id SMALLINT;        -- 修改字段属性
mysql> ALTER TABLE student CHANGE [COLUMN] id new_id INT;      -- 修改字段名

-- 删除字段
mysql> ALTER TABLE student DROP [COLUMN] age;   

-- 编辑主键
mysql> ALTER TABLE student ADD PRIMARY KEY(id,age);           
mysql> ALTER TABLE student DROP PRIMARY KEY;                 

-- 编辑外键
mysql> ALTER TABLE student ADD CONSTRAINT fk_student_class FOREIGN KEY(cid) REFERENCES class(id);
mysql> ALTER TABLE student DROP FOREIGN KEY fk_student_class;  
```

---

## 数据指令

### 增删改查

**插入数据**，如果已有主键值则插入数据失败。

```sql
mysql> INSERT INTO student
    -> (ID,name,grade)
    -> VALUES(755,'王东浩',80);
```

**插入并替换数据**，如果已有主键值则先删除再插入。

```sql
mysql> REPLACE INTO student
    -> (ID,name,grade)
    -> VALUES(755,'王东浩',80);
```

**更新数据**

```sql
mysql> UPDATE student
    -> SET name='孙鹏',grade=60
    -> WHERE id=753;
```

**删除数据**

```sql
mysql> DELETE FROM student
    -> WHERE id=754;
```

**查询数据**

```sql
mysql> SELECT id,name FROM student               -- 按条件查询数据
    -> WHERE id BETWEEN 753 and 755;

mysql> SELECT * FROM student;                    -- 查询全部数据
```

#### 条件语句

- DISTINCT 关键字用于对查询结果去重，必须放于所有字段前。只有多个字段全部相等才会被去重。
  
```sql
mysql> SELECT DINTINCE age,sex FROM student;     -- 查询数据并去重
```

- WHERE 语句用于指定 更新/删除/查询 的操作范围，如果不设定范围将对全部数据进行操作。

```sql
mysql> SELECT * FROM student WHERE id = 100;
mysql> SELECT * FROM student WHERE id != 100;
mysql> SELECT * FROM student WHERE id [NOT] BETWEEN 30 AND 50;
mysql> SELECT * FROM student WHERE id [NOT] IN (30, 35 ,50);
mysql> SELECT * FROM student WHERE grade IS [NOT] NULL;
```

- LIKE 语句用于对字符串进行模糊匹配：`%`代表任意多个字符 `_`代表一个字符 `/`代表转义

```sql
mysql> SELECT * FROM student WHERE name LIKE 'Tom%';
```


### 分组排序

#### 数据分组

分组函数|功能
-|-
count|个数
sum|总和
max|最大值
min|最小值
avg|求平均值
group_concat|组内字符串拼接

1. GROUP 语句指定数据的分组方式，如果不含则默认把全部数据合并为一条数据。（本质是生成临时表）
2. AS 关键字为表或者列起别名，可省略。
3. HAVING 语句对分组后的结果进行筛选。

```sql
-- 查询班级总数
mysql> SELECT COUNT(*) FROM class;                    -- 全部合并

-- 查询各年级人数
mysql> SELECT grade, SUM(class.student_num) AS nums 
    -> FROM class 
    -> GROUP BY grade                                 -- 各班数据按年级合并
    -> HAVING SUM(class.student_num) > 200;           -- 筛选人数大于 200 的年级
```

#### 数据排序

- ORDER 语句指定数据显示顺序，ASC 为升序 / DESC 为降序。
- LIMIT 语句对排序后的数据进行筛选，指定起始序号和总数量。

```sql
-- 查询学生信息
mysql> SELECT * 
    -> FROM student 
    -> ORDER BY grade DESC, ID ASC                   -- 按成绩降序排列，若相同按学号升序排列
    -> LIMIT 10,20;                                  -- 筛选第 11 - 30 名
```


### 多表查询

#### 嵌套查询

1. FROM 型：子语句返回一个表，且必须给子查询结果取别名。
2. WHERE 型：子语句返回一个值，不能用于 UPDATE。

```sql
-- FROM 型
mysql> SELECT * 
    -> FROM (SELECT * FROM tb WHERE id > 0) AS subfrom 
    -> WHERE id > 1;

-- WHERE 型
mysql> SELECT * 
    -> FROM tb
    -> WHERE money = (SELECT max(money) FROM tb);
```


#### 合并查询

1. 默认为 DISTINCT 形式，不同表查询到的相同数据只展示一个。
2. 设置为 ALL 则不同表查询到的相同结果重复展示。
    
```sql
-- DISTINCT 形式
mysql> (SELECT * FROM student WHERE id < 10) 
    -> UNION
    -> (SELECT * FROM student WHERE id > 20);

-- ALL 形式
mysql> (SELECT * FROM student1) 
    -> UNION ALL 
    -> (SELECT * FROM student2);
```

#### 连表查询

- **内连接 INNER JOIN**：（默认）未指定连接条件时，自动查找相同字段名匹配连接条件。

```sql
mysql> SELECT s.id,s.name,c.name
    -> FROM student s JOIN class c
    -> ON e.cid = c.id;

mysql> SELECT * 
    -> FROM student s, class c 
    -> WHERE s.id = c.id; 
```

- **交叉连接 CROSS JOIN**：未指定连接条件时，视为无连接条件。

```sql
mysql> SELECT *
    -> FROM boy CROSS JOIN girl;                 -- 显示所有交配可能


mysql> SELECT *
-> FROM boy, girl;                               -- 等价写法
```

- **外连接 OUTER JOIN**：如果数据不存在，也会出现在连接结果中。
    
  - **LEFT JOIN**：左表数据一定显示，没有匹配右表数据用 null 填充。
  - **RIGHT JOIN**：右表数据一定显示，没有匹配左表数据用 null 填充。
  - **FULL JOIN**：两表数据一定显示，没有匹配数据用 null 填充。

```sql
mysql> SELECT s.id,s.name,c.name                   -- 显示学生的班级信息
    -> FROM student s LEFT JOIN class c            -- 没有班级的学生也会显示
    -> ON s.cid = c.id;

-- 先筛选再连接（效率等价，但如果有大量重复值提前筛选可以提高效率）
mysql> SELECT s.id,s.name,c.name    
    -> FROM student s LEFT JOIN (SELECT DINTINCT id, name FROM class) c       
    -> ON s.cid = c.id;
```



---

## 高级指令

### 索引

- **索引类型**

索引名称|索引类型|字段类型|备注
-|-|-|-
PRIMARY KEY|主索引|主键|字段值不能重复，也不能为空。
INDEX|普通索引|自定义字段|无，效率低。
UNIQUE|唯一索引|自定义字段|字段值不能重复，效率高。
FULLTEXT|文本索引|自定义字段|无，用于文本检索。

```sql
-- 查询索引
mysql> SHOW INDEX FROM student;

-- 创建索引
mysql> CREATE [UNIQUE|FULLTEXT] INDEX idx_student_age 
    -> [USING BTREE]                                           -- 指定索引类型，默认 B+ 树
    -> ON student(age);                                        -- 指定索引属性

mysql> ALTER TABLE student ADD INDEX [idx_student_age](id,age);   
mysql> ALTER TABLE student ADD UNIQUE [uniq_student_age](age);         
mysql> ALTER TABLE student ADD FULLTEXE [ft_student_age](age);  

-- 删除索引
mysql> DROP INDEX idx_student_age ON student;

mysql> ALTER TABLE student DROP INDEX idx_student_age;                 
```


### 视图

**视图算法**

算法| 名称| 含义
-|-|-
UNDEFINED|未定义(默认)|MySQL 自主选择相应的算法。
MERGE |合并 |视图的查询语句，与外部查询需要先合并再执行。
TEMPTABLE | 临时表 | 将视图执行完毕后形成临时表，再做外层查询.


**更新选项**

算法| 名称| 含义
-|-|-
CACADED|级联(默认)| 满足所有视图条件才能进行数据更新。
LOCAL | 本地 | 满足本视图条件就能进行数据更新。

```sql
-- 创建视图
mysql> CREATE VIEW view_student
    -> AS (SELECT * FROM student);

mysql> CREATE ALGORITHM = MERGE
    -> VIEW view_student
    -> AS (SELECT * FROM student)
    -> WITH LOCAL CHECK OPTION;        

-- 查看结构
mysql> SHOW CREATE VIEW view_student;

-- 删除视图
mysql> DROP VIEW [IF EXISTS] view_student;

-- 修改视图结构（慎用）
mysql> ALTER VIEW view_student
    -> AS (SELECT * FROM student);
```


### 事务

开启事务后，所有输入的 SQL 语句将被认作一个不可分割的整体，在提交时统一执行。

如果在输入过程中出现问题，可以手动进行回滚。在输入过程中可以设置保存点。

```sql
-- 事务开启
mysql> START TRANSACTION;
mysql> BEGIN;
-- 事务提交
mysql> COMMIT;
-- 事务回滚
mysql> ROLLBACK;

-- 保存点
mysql> SAVEPOINT mypoint;                     -- 设置保存点
mysql> ROLLBACK TO SAVEPOINT mypoint;         -- 回滚到保存点
mysql> RELEASE SAVEPOINT mypoint;             -- 删除保存点
```


InnoDB 存储引擎支持关闭自动提交，强制开启事务：任何操作都必须要 COMMIT 提交后才能持久化数据，否则对其他客户端不可见。

```sql
mysql> SET AUTOCOMMIT = 0|1;             -- 0 表示关闭自动提交，1 表示开启自动提交。
```

### 锁定

MySQL 可以手动对表/行锁定，防止其它客户端进行不正当地读取和写入。

```sql
-- 锁定
mysql> LOCK TABLES student [AS alias];          
-- 解锁
mysql> UNLOCK TABLES;
```

### 触发器

触发程序是与表有关的数据库对象，监听记录的增加、修改、删除。当出现特定事件时，将激活该对象执行 SQL 语句。

1. MySQL 数据库只支持**行级触发器**：如果一条 INSERT 语句插入 N 行数据，语句级触发器只执行一次，行级触发器要执行 N 次。

2. 在触发器中，可以使用 `OLD` 和 `NEW` 表示该行的新旧数据。删除操作只有 `OLD`，增加操作只有 `NEW` 。

```sql
-- 查看触发器
mysql> SHOW TRIGGERS;

-- 创建触发器
mysql> CREATE TRIGGER my_trigger 
    -> BEFORE INSERT                    -- 触发时间 BEFORE/AFTER 触发条件 INSERT/UPDATE/DELETE
    -> ON student                       -- 监听表必须是永久性表
    -> FOR EACH ROW                     -- 行级触发器
    -> BEGIN
    -> INSERT INTO student_logs(id,op,op_time，op_id) VALUES(null,'insert',now(),new.id)
    -> END;

-- 删除触发器
mysql> DROP TRIGGER [schema_name.]trigger_name;
```