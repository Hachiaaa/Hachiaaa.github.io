Mysql梳理

## 关系模型
1.数据类型
```sql
INT 整型
BIGINT 长整型
REAL   4字节浮点型
DOUBLE  8字节浮点型
CHAR(N)   定长字符串
VARCHAR(N)  变长字符串
BOOLEAN  布尔类型
DATE  日期类型 2020-01-01
TIME  时间类型  10:50:59
DATETIME  日期时间类型  
```
2.关系模型的本质是一个二维表，表的每一行称为记录（Record），表的每一列称为字段（Column），字段定义了数据类型以及是否允许为NULL。  
3.数据库中表与表之间的联系有三种，一对一，一对多，多对一。这种关系在关系型数据库中，主要是通过主键和外键来维护的。  
4.主键是指能够通过某个字段唯一区分不同的记录。主键能够约束两条记录不完全相同，最好不要修改。常见的用于主键的有如下两种类型：
 * 自增整数类型，数据库在插入数据时自动为每一条记录分配一个自增整数。这种通常能满足需求 BIGINT NOT NULL AUTO_INCREMENT  
 * 全局唯一GUID类型

5.联合主键，允许多个字段唯一标识记录，允许一列有重复，但不是所有主键列都重复。这种一般不推荐食用。  
6.相比主键，外键可以把数据与另一张表关联起来，外键是通过外键约束实现的。
```sql
ALTER TABLE <tablename>
ADD CONSTRAINT <foreignname>
FOREIGN KEY (fieldname)
REFERENCES  <tablename>（field）
````
通过外键约束，关系数据库可以保证无法插入无效数据，外键约束会降低数据库的性能，所以大部分应用程序并不设置外键约束。删除外键约束的方式为：
```sql
ALTER TABLE <tablename>
DROP FOREIGN KEY <foreign name>
```
7.数据库索引，索引在关系数据库中是对某一列或多个列进行预排序的数据结构，通过使用索引会大大加快查询速度。创建索引的方式为:
```sql
ALTER TABLE <tablename>
ADD INDEX idx_name(field_name) //field_name可以是多个，可以在括号里依次写上
ALTER TABLE <tablename>
ADD UNIQUE INDEX idx_name(field_name)  //添加一个唯一索引
```
索引的效率取决于索引是否散列，该列的值越互不相同，索引效率越高。

## 查询数据

1.基本查询
```sql
SELECT * FORM <tablename> //也可以不带from 表示计算 
```
2.条件查询
```sql
SELECT * FROM (表名) WHERE (条件表达式) //条件表达式可以加上AND,OR,NOT等逻辑语句
LIKE语句可用于判断相似，如 name LIKE 'ab%'  //%表示任意字符
```
3.投影查询
```sql
SELECT column1 alias1,column2 alias2... FROM  //其中alias表示对相应字段起别名
```
4.排序
```sql
SELECT * FROM <> ORDER BY <field> DESC //排序用ORDER BY字句，DESC表示倒序，默认为升序ASC。其中field可以为多个，表示进一步排序
注意ORDER BY字句需要放在WHERE字句后面
```  
5.分页,通过LIMIT <M> OFFSET <N>实现，每页只显示M条记录
6.聚合查询，统计总数平均数这类计算，可以用聚合函数。
```sql
SELECT COUNT(*) num FORM  //统计表里有多少条记录,并起一个别名
SUM  //统计和，必须为数值类型
AVG  //统计平均值，必须为数值类型
MAX  //计算某一列的最大值，可以为字符串
MIN //计算某一列的最小值，可以为字符串
SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender; //分组聚合
```
7.笛卡尔多表查询
```sql
SELECT
    s.id sid,
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cname
FROM students s, classes c;
//配置别名
```
8.连接查询
> * INNER JOIN 返回两张表都存在的记录
> * LEFT OUTER JOIN 返回左表存在的记录，右表如果不存在相关记录，则相应字段填充为NULL
> * RIGHT OUTER JOIN 返回右表存在的记录，左表如果不存在相关记录，则相应字段填充为NULL
> * FULL OUTER JOIN 返回左右表都存在的记录
```sql
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
INNER JOIN classes c
ON s.class_id = c.id;
```

## 修改数据
1.插入数据
```sql
INSERT INTO <表名> (字段1, 字段2, ...) VALUES (值1, 值2, ...);
```
2.更新数据
```sql
UPDATE <表名> SET 字段1=值1, 字段2=值2, ... WHERE ...; //set 字段可以用表达式，如 SET score=score+10
```
3.删除数据
```sql
DELETE FROM <表名> WHERE ...;
```

## 其他常用Sql命令
```sql
CREATE DATABASE test //创建数据库
DROP DATABASE test //删除数据库
USE test //切换数据库
SHOW TABLES  //查看所有表
DESC test_table //查看表的结构
DROP TABLE test_table  //删除表
CREATE test_table //创建表
ALTER TABLE students ADD COLUMN birth VARCHAR(10) NOT NULL; //增加一列
ALTER TABLE students CHANGE COLUMN birth birthday VARCHAR(20) NOT NULL;ALTER TABLE students CHANGE COLUMN birth birthday VARCHAR(20) NOT NULL; //修改列
ALTER TABLE students DROP COLUMN birthday; //删除列
REPLACE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99); //插入或替换
INSERT INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99) ON DUPLICATE KEY UPDATE name='小明', gender='F', score=99; //插入或更新
INSERT IGNORE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99); //插入或忽略
INSERT INTO statistics (class_id, average) SELECT class_id, AVG(score) FROM students GROUP BY class_id; //写入查询结果
```

 
