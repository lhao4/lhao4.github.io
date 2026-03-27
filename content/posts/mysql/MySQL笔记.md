---
title: "MySQL学习笔记"
date: 2024-06-01
categories: ["数据库"]
tags: ["MySQL"]
---

# MySQL 学习笔记

## 一、数据库概述

### 1.1 主流数据库

#### 关系型数据库（RDBMS）

| 数据库 | 特点 |
|--------|------|
| **Oracle Database** | 企业级关系型数据库，具有强大的事务处理能力和高并发支持，适合金融、电信等关键业务场景 |
| **MySQL** | 最流行的开源关系型数据库之一，轻量级架构，广泛应用于 Web 应用程序开发 |
| **Microsoft SQL Server** | 微软推出的旗舰级关系型数据库，与 Windows 及 .NET 深度集成 |
| **PostgreSQL** | 功能强大的开源对象关系型数据库，严格遵循 SQL 标准，支持 JSON、GIS 等高级数据类型 |
| **SQLite** | 专为嵌入式系统设计的轻量级数据库，零配置，适合移动应用和物联网设备 |

#### 非关系型数据库（NoSQL）

| 类型 | 代表产品 | 特点 |
|------|----------|------|
| **键值存储** | Redis、DynamoDB | 基于内存的高性能存储，支持多种数据结构 |
| **文档存储** | MongoDB、Couchbase | 采用 BSON/JSON 格式，支持动态 schema |
| **列族存储** | Cassandra、HBase | 分布式架构，适合海量数据存储 |
| **图数据库** | Neo4j、Amazon Neptune | 专注于图数据模型，适合关系网络分析 |
| **时序数据库** | InfluxDB、TimescaleDB | 专为时间序列数据优化 |
| **搜索引擎** | Elasticsearch | 基于 Lucene，支持全文检索和实时分析 |

### 1.2 数据库基本概念

| 概念 | 说明 |
|------|------|
| **DBMS** | 数据库管理系统，提供数据定义、操作及安全控制功能 |
| **数据库** | 按数据模型组织的结构化数据集合，由关联表构成 |
| **表（Table）** | 数据库基本单元，由行列组成 |
| **列（Column）** | 表的垂直字段，定义数据类型与约束 |
| **行（Row）** | 表的水平记录，包含各列值 |
| **主键（Primary Key）** | 唯一标识记录的列或列组合 |
| **SQL** | 关系型数据库标准语言，支持数据定义、操作及权限控制 |

---

## 二、数据库基础操作

### 2.1 数据库管理命令

```sql
-- 显示所有数据库
SHOW DATABASES;

-- 创建数据库
CREATE DATABASE [IF NOT EXISTS] 数据库名;

-- 使用数据库
USE 数据库名;

-- 查看当前数据库信息
SELECT DATABASE();      -- 当前连接的数据库
SELECT VERSION();       -- 数据库版本
SELECT NOW();           -- 当前日期时间
SELECT USER();          -- 当前用户

-- 删除数据库
DROP DATABASE [IF EXISTS] 数据库名;

-- 显示数据库中的表
SHOW TABLES;
```

---

## 三、数据类型

### 3.1 整数类型

| 类型 | 存储空间 | 有符号范围 | 无符号范围 |
|------|----------|------------|------------|
| TINYINT | 1字节 | -128 ~ 127 | 0 ~ 255 |
| SMALLINT | 2字节 | -32768 ~ 32767 | 0 ~ 65535 |
| MEDIUMINT | 3字节 | -8388608 ~ 8388607 | 0 ~ 16777215 |
| INT/INTEGER | 4字节 | -2147483648 ~ 2147483647 | 0 ~ 4294967295 |
| BIGINT | 8字节 | -9223372036854775808 ~ 9223372036854775807 | 0 ~ 18446744073709551615 |

```sql
-- 使用无符号类型
CREATE TABLE users (
    age TINYINT UNSIGNED,  -- 0~255
    score INT UNSIGNED
);
```

### 3.2 浮点型和定点型

| 类型 | 说明 |
|------|------|
| FLOAT | 单精度浮点数，约 7 位有效数字 |
| DOUBLE | 双精度浮点数，约 15 位有效数字 |
| DECIMAL(M,D) | 定点数，M 是总位数，D 是小数位数，精确计算 |

```sql
-- 金额计算推荐使用 DECIMAL
CREATE TABLE orders (
    amount DECIMAL(10, 2)  -- 最大 99999999.99
);
```

### 3.3 日期时间类型

| 类型 | 格式 | 范围 |
|------|------|------|
| DATE | YYYY-MM-DD | '1000-01-01' ~ '9999-12-31' |
| TIME | HH:MM:SS | '-838:59:59' ~ '838:59:59' |
| DATETIME | YYYY-MM-DD HH:MM:SS | '1000-01-01 00:00:00' ~ '9999-12-31 23:59:59' |
| TIMESTAMP | YYYY-MM-DD HH:MM:SS | '1970-01-01 00:00:01' UTC ~ '2038-01-19 03:14:07' UTC |
| YEAR | YYYY | 1901 ~ 2155 |

### 3.4 字符串类型

| 类型 | 说明 |
|------|------|
| CHAR(M) | 固定长度，最大 255 字符，存储时右侧填充空格 |
| VARCHAR(M) | 可变长度，最大 65535 字节，只占用实际长度+1/2字节 |
| TINYTEXT | 最大 255 字节 |
| TEXT | 最大 65535 字节 |
| MEDIUMTEXT | 最大 16MB |
| LONGTEXT | 最大 4GB |
| ENUM | 枚举类型，只能选一个值 |
| SET | 集合类型，可以选多个值 |

#### CHAR 和 VARCHAR 的区别

| 特性 | CHAR | VARCHAR |
|------|------|---------|
| 存储方式 | 固定长度 | 可变长度 |
| 最大长度 | 255 字符 | 65535 字节 |
| 空间效率 | 可能浪费空间 | 节省空间 |
| 查询效率 | 更高 | 稍低 |
| 尾部空格 | 自动去除 | 保留 |

```sql
CREATE TABLE vc(
    v VARCHAR(4),
    c CHAR(4)
);

INSERT INTO vc VALUES('ab  ', 'ab  ');

-- 查询结果
SELECT LENGTH(v), LENGTH(c) FROM vc;  -- 结果: 4, 2
SELECT * FROM vc WHERE c = 'ab  ';    -- 能匹配到
SELECT * FROM vc WHERE v = 'ab  ';    -- 不能匹配
```

### 3.5 BLOB 类型

| 类型 | 最大大小 |
|------|----------|
| TINYBLOB | 255 字节 |
| BLOB | 64KB |
| MEDIUMBLOB | 16MB |
| LONGBLOB | 4GB |

---

## 四、DDL（数据定义语言）

Data Definition Language，用于定义和管理数据库对象和结构。

### 4.1 创建表

```sql
CREATE TABLE [IF NOT EXISTS] 表名(
    字段1 字段类型 [列级约束条件],
    字段2 字段类型 [列级约束条件],
    ...
    [表级约束条件]
);

-- 示例
CREATE TABLE users(
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20) NOT NULL,
    email VARCHAR(50) UNIQUE,
    age INT DEFAULT 18,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 4.2 查看表结构

```sql
-- 查看表的基本结构
DESC 表名;
DESCRIBE 表名;

-- 查看表的创建语句
SHOW CREATE TABLE 表名;

-- 查看表的索引
SHOW INDEX FROM 表名;
```

### 4.3 修改表

```sql
-- 添加字段
ALTER TABLE 表名
ADD COLUMN 新列名 数据类型 [约束条件] [FIRST | AFTER 列名];

-- 修改字段类型
ALTER TABLE 表名
MODIFY COLUMN 列名 数据类型 [约束条件];

-- 修改字段位置
ALTER TABLE 表名
MODIFY COLUMN 列名 数据类型 FIRST | AFTER 列名;

-- 修改字段名
ALTER TABLE 表名
CHANGE COLUMN 旧列名 新列名 数据类型;

-- 删除字段
ALTER TABLE 表名
DROP COLUMN 列名;

-- 重命名表
ALTER TABLE 旧表名 RENAME TO 新表名;
RENAME TABLE 旧表名 TO 新表名;

-- 删除表
DROP TABLE [IF EXISTS] 表1 [, 表2, 表3...];
```

### 4.4 练习

```sql
-- 1. 创建文章表
CREATE TABLE article (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    author VARCHAR(255) NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 2. 创建订单表
CREATE TABLE orders (
    id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date DATETIME NOT NULL,
    amount DECIMAL(10, 2) NOT NULL
);

-- 3. 修改员工表
-- 添加奖金字段
ALTER TABLE emp ADD emp_bonus DECIMAL(10, 2);
-- 修改数据类型
ALTER TABLE emp MODIFY emp_bonus DECIMAL(12, 2);
-- 修改字段名
ALTER TABLE emp CHANGE emp_bonus bonus DECIMAL(12, 2);
-- 删除字段
ALTER TABLE emp DROP COLUMN bonus;
```

---

## 五、约束

### 5.1 约束类型概览

| 约束类型 | 关键字 | 说明 |
|----------|--------|------|
| 非空约束 | NOT NULL | 字段必须有值，不能为空 |
| 默认约束 | DEFAULT | 未指定值时使用默认值 |
| 唯一约束 | UNIQUE | 字段值必须唯一，允许 NULL |
| 主键约束 | PRIMARY KEY | 唯一标识每行记录，非空且唯一 |
| 外键约束 | FOREIGN KEY | 建立表与表之间的关系 |
| 自增约束 | AUTO_INCREMENT | 自动递增，通常用于主键 |

### 5.2 非空约束

```sql
-- 创建表时设置
CREATE TABLE users(
    id INT NOT NULL,
    name VARCHAR(20) NOT NULL
);

-- 已有字段添加非空约束
ALTER TABLE users
MODIFY COLUMN name VARCHAR(20) NOT NULL;

-- 删除非空约束
ALTER TABLE users
MODIFY COLUMN name VARCHAR(20);
```

**注意**：NULL 值和空字符串 '' 是不同的，NULL 表示没有值，空字符串表示值为空字符串。

### 5.3 默认约束

```sql
CREATE TABLE users(
    id INT NOT NULL DEFAULT 666,
    name VARCHAR(20),
    status TINYINT DEFAULT 1
);

INSERT INTO users(name) VALUES('李四');  -- id 默认为 666
```

### 5.4 唯一约束

```sql
-- 列级约束
CREATE TABLE users(
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(50) UNIQUE
);

-- 表级约束（支持多列联合唯一）
CREATE TABLE users(
    id INT,
    name VARCHAR(20),
    CONSTRAINT uk_name_id UNIQUE(id, name)
);

-- 添加唯一约束
ALTER TABLE users
ADD CONSTRAINT uk_email UNIQUE(email);

-- 删除唯一约束
ALTER TABLE users
DROP INDEX uk_email;
```

### 5.5 主键约束

```sql
-- 列级主键
CREATE TABLE users(
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20)
);

-- 表级主键（支持联合主键）
CREATE TABLE scores(
    student_id INT,
    course_id INT,
    score DECIMAL(5, 2),
    PRIMARY KEY(student_id, course_id)
);

-- 删除主键
ALTER TABLE users DROP PRIMARY KEY;
```

**主键设计原则**：
- 每个表都应该定义主键
- 主键值不应该修改
- 使用与业务无关的列作为主键（通常使用自增 ID）

### 5.6 外键约束

```sql
-- 创建父表
CREATE TABLE departments(
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL
);

-- 创建子表
CREATE TABLE employees(
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    dept_id INT,
    CONSTRAINT fk_dept 
        FOREIGN KEY(dept_id) 
        REFERENCES departments(id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```

**外键注意事项**：
- 外键字段可以为 NULL（称为孤儿数据）
- 创建表时先创建父表，再创建子表
- 插入数据时先插入父表，再插入子表
- 删除时先删除子表数据，再删除父表数据
- 子表外键类型要与父表主键类型一致

**级联操作**：
- `ON DELETE CASCADE`：父表删除时，子表相关记录自动删除
- `ON DELETE SET NULL`：父表删除时，子表外键设为 NULL
- `ON UPDATE CASCADE`：父表更新时，子表外键自动更新

---

## 六、DML（数据操作语言）

Data Manipulation Language，用于操作数据库中的实际数据。

### 6.1 插入数据

```sql
-- 插入完整行（不推荐，不安全）
INSERT INTO 表名
VALUES(值1, 值2, ...);

-- 指定列插入（推荐）
INSERT INTO 表名(列1, 列2, ...)
VALUES(值1, 值2, ...);

-- 插入多条数据
INSERT INTO 表名(列1, 列2, ...)
VALUES 
    (值1, 值2, ...),
    (值1, 值2, ...),
    (值1, 值2, ...);

-- 从其他表插入
INSERT INTO 表名(列1, 列2)
SELECT 列1, 列2 FROM 其他表 WHERE 条件;
```

**注意**：单条 INSERT 语句插入多条数据比多条 INSERT 语句更快。

### 6.2 更新数据

```sql
UPDATE 表名
SET 列1 = 新值1,
    列2 = 新值2,
    ...
WHERE 条件;
```

**⚠️ 警告**：记得加 WHERE 条件，否则会更新整个表！

### 6.3 删除数据

```sql
-- 删除指定数据
DELETE FROM 表名
WHERE 条件;

-- 删除所有数据（逐行删除，可回滚）
DELETE FROM 表名;

-- 清空表（快速，不可回滚，重置自增）
TRUNCATE TABLE 表名;
```

**⚠️ 警告**：
- DELETE 是物理删除，但在事务中可以回滚
- 使用 UPDATE 或 DELETE 前，先用 SELECT 测试 WHERE 条件

### 6.4 练习

```sql
-- 创建学生信息表
CREATE TABLE student_info (
    student_id INT PRIMARY KEY,
    student_name VARCHAR(50) NOT NULL,
    age INT,
    birth_date DATE
);

-- 插入数据
INSERT INTO student_info (student_id, student_name, age, birth_date) 
VALUES 
    (1, '张三', 20, '2002-05-08'),
    (2, '李四', 21, '2001-09-06'),
    (3, '王五', 19, '2003-12-15');

-- 更新数据
UPDATE student_info 
SET age = 21, birth_date = '2001-05-08' 
WHERE student_name = '张三';

-- 删除数据
DELETE FROM student_info 
WHERE age < 21;
```

---

## 七、DQL（数据查询语言）

### 7.1 基本查询

```sql
-- 查询指定字段
SELECT 字段1, 字段2, ... FROM 表名;

-- 查询全部字段
SELECT * FROM 表名;

-- 使用别名
SELECT 字段 AS 别名 FROM 表名;
SELECT 字段 别名 FROM 表名;  -- AS 可省略

-- 条件查询
SELECT * FROM 表名 WHERE 条件;
```

### 7.2 运算符

#### 算术运算符
```sql
+  -  *  /  %

-- 示例：计算年工资
SELECT ename, sal * 12 AS annual_sal FROM emp;
```

#### 比较运算符

| 运算符 | 说明 |
|--------|------|
| = | 等于 |
| <> 或 != | 不等于 |
| > | 大于 |
| < | 小于 |
| >= | 大于等于 |
| <= | 小于等于 |
| IS NULL | 为空 |
| IS NOT NULL | 不为空 |
| BETWEEN AND | 在范围内 |
| IN | 在列表中 |
| NOT IN | 不在列表中 |
| LIKE | 模糊匹配 |

#### 逻辑运算符
```sql
AND  OR  NOT
```

#### LIKE 通配符

| 通配符 | 说明 |
|--------|------|
| % | 匹配任意数量字符（包括零个） |
| _ | 匹配单个字符 |

```sql
-- 查询姓名中包含 's' 的员工
SELECT * FROM emp WHERE ename LIKE '%s%';

-- 查询工作以 'MAN' 结尾的员工
SELECT * FROM emp WHERE job LIKE '%MAN';

-- 查询姓名第二个字符为 'a' 的员工
SELECT * FROM emp WHERE ename LIKE '_a%';
```

### 7.3 查询处理

#### 排序 ORDER BY

```sql
SELECT * FROM 表名
ORDER BY 字段1 [ASC | DESC], 字段2 [ASC | DESC];

-- 示例
SELECT * FROM emp ORDER BY sal DESC;           -- 按薪水降序
SELECT * FROM emp ORDER BY deptno ASC, sal DESC; -- 先按部门升序，再按薪水降序
```

**注意**：默认不是按主键排序的。

#### 限制数量 LIMIT

```sql
-- 返回前 N 条
SELECT * FROM 表名 LIMIT N;

-- 从第 M 条开始（从 0 开始计数），返回 N 条
SELECT * FROM 表名 LIMIT M, N;

-- MySQL 8.0+ 推荐语法
SELECT * FROM 表名 LIMIT N OFFSET M;

-- 示例：分页查询（每页 10 条，第 3 页）
SELECT * FROM emp LIMIT 20, 10;
```

#### 去重 DISTINCT

```sql
-- 单列去重
SELECT DISTINCT job FROM emp;

-- 多列去重（组合唯一）
SELECT DISTINCT job, deptno FROM emp;
```

#### 组合查询 UNION

```sql
SELECT 字段1, 字段2 FROM 表1
UNION
SELECT 字段1, 字段2 FROM 表2;

-- UNION ALL 保留重复行
SELECT 字段1, 字段2 FROM 表1
UNION ALL
SELECT 字段1, 字段2 FROM 表2;
```

**UNION 规则**：
- 每个查询必须包含相同数量的列
- 列必须拥有相似的数据类型
- UNION 自动去除重复行，UNION ALL 保留重复行

### 7.4 分组查询

#### GROUP BY

```sql
SELECT 分组字段, 聚合函数(字段)
FROM 表名
WHERE 条件
GROUP BY 分组字段;

-- 示例：统计每个部门的人数
SELECT deptno, COUNT(*) 
FROM emp 
GROUP BY deptno;

-- 多字段分组
SELECT deptno, job, COUNT(*)
FROM emp
GROUP BY deptno, job;
```

**注意**：
- 如果分组列中有 NULL 值，NULL 会作为一个分组返回
- GROUP BY 子句必须出现在 WHERE 之后，ORDER BY 之前

#### HAVING 过滤分组

```sql
SELECT deptno, COUNT(*)
FROM emp
GROUP BY deptno
HAVING COUNT(*) > 5;

-- WHERE + HAVING
SELECT deptno, COUNT(*)
FROM emp
WHERE sal > 1000
GROUP BY deptno
HAVING COUNT(*) > 2;
```

**WHERE vs HAVING**：
- WHERE：分组前过滤，过滤行
- HAVING：分组后过滤，过滤分组

### 7.5 SELECT 执行顺序

```
1. FROM      -- 确定数据来源
2. WHERE     -- 过滤行
3. GROUP BY  -- 分组
4. HAVING    -- 过滤分组
5. SELECT    -- 选择列
6. DISTINCT  -- 去重
7. ORDER BY  -- 排序
8. LIMIT     -- 限制结果数量
```

### 7.6 练习

```sql
-- 1. 按照员工入职日期降序排序
SELECT * FROM emp ORDER BY hiredate DESC;

-- 2. 查询 1981 年后入职的员工，按薪水升序排序
SELECT * FROM emp WHERE hiredate > '1981-12-31' ORDER BY sal;

-- 3. 查询员工中薪水前 5 名
SELECT * FROM emp ORDER BY sal DESC LIMIT 5;

-- 4. 查询薪水在 5-8 名的员工
SELECT * FROM emp ORDER BY sal DESC LIMIT 4, 4;

-- 5. 查询所有领导的编号（去重）
SELECT DISTINCT mgr FROM emp WHERE mgr IS NOT NULL;

-- 6. 统计每个岗位的人数
SELECT job, COUNT(*) FROM emp GROUP BY job;

-- 7. 计算每个工作岗位的最高薪水，由低到高排序
SELECT job, MAX(sal) AS max_sal 
FROM emp 
GROUP BY job 
ORDER BY max_sal ASC;

-- 8. 找出平均薪水大于 2000 的岗位
SELECT job, AVG(sal) 
FROM emp 
GROUP BY job 
HAVING AVG(sal) > 2000;
```

---

## 八、常用函数

### 8.1 数值函数

| 函数 | 说明 | 示例 |
|------|------|------|
| ABS(x) | 返回 x 的绝对值 | ABS(-5) = 5 |
| CEIL(x) | 向上取整 | CEIL(3.2) = 4 |
| FLOOR(x) | 向下取整 | FLOOR(3.8) = 3 |
| ROUND(x, y) | 四舍五入，保留 y 位小数 | ROUND(3.14159, 2) = 3.14 |
| TRUNCATE(x, y) | 截断小数 | TRUNCATE(3.14159, 2) = 3.14 |
| MOD(x, y) | 返回 x 除以 y 的余数 | MOD(10, 3) = 1 |
| RAND() | 生成 0-1 的随机数 | RAND() |
| POW(x, y) | x 的 y 次方 | POW(2, 3) = 8 |
| SQRT(x) | 平方根 | SQRT(16) = 4 |

### 8.2 字符串函数

| 函数 | 说明 | 示例 |
|------|------|------|
| CONCAT(s1, s2, ...) | 字符串连接 | CONCAT('Hello', ' ', 'World') |
| CONCAT_WS(sep, s1, s2, ...) | 用分隔符连接 | CONCAT_WS('-', '2024', '01', '01') |
| LENGTH(str) | 返回字符串长度（字节） | LENGTH('abc') = 3 |
| CHAR_LENGTH(str) | 返回字符数 | CHAR_LENGTH('中国') = 2 |
| LOWER(str) / LCASE(str) | 转小写 | LOWER('ABC') = 'abc' |
| UPPER(str) / UCASE(str) | 转大写 | UPPER('abc') = 'ABC' |
| LTRIM(str) | 删除左侧空格 | |
| RTRIM(str) | 删除右侧空格 | |
| TRIM(str) | 删除两侧空格 | |
| SUBSTRING(str, pos, len) | 截取子串 | SUBSTRING('Hello', 2, 3) = 'ell' |
| LEFT(str, n) | 返回左边 n 个字符 | |
| RIGHT(str, n) | 返回右边 n 个字符 | |
| REPLACE(str, from, to) | 替换字符串 | REPLACE('abc', 'b', 'x') = 'axc' |
| INSTR(str, substr) | 返回子串位置 | INSTR('abc', 'b') = 2 |
| FORMAT(x, n) | 格式化数字 | FORMAT(1234567.89, 2) = '1,234,567.89' |

### 8.3 日期时间函数

| 函数 | 说明 |
|------|------|
| CURDATE() / CURRENT_DATE() | 当前日期 |
| CURTIME() / CURRENT_TIME() | 当前时间 |
| NOW() / SYSDATE() | 当前日期时间 |
| DATE(date) | 提取日期部分 |
| TIME(date) | 提取时间部分 |
| YEAR(date) | 提取年份 |
| MONTH(date) | 提取月份 |
| DAY(date) | 提取日期 |
| HOUR(time) | 提取小时 |
| MINUTE(time) | 提取分钟 |
| SECOND(time) | 提取秒 |
| WEEK(date) | 返回是一年中的第几周 |
| DATE_ADD(date, INTERVAL expr unit) | 日期加法 |
| DATE_SUB(date, INTERVAL expr unit) | 日期减法 |
| DATEDIFF(date1, date2) | 计算两个日期相差天数 |
| TIMESTAMPDIFF(unit, date1, date2) | 计算时间差 |
| UNIX_TIMESTAMP() | 返回 UNIX 时间戳 |
| FROM_UNIXTIME(unix) | UNIX 时间戳转日期 |
| DATE_FORMAT(date, format) | 格式化日期 |
| STR_TO_DATE(str, format) | 字符串转日期 |

```sql
-- 日期格式化
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s');
SELECT DATE_FORMAT(hiredate, '%Y年%m月%d日') FROM emp;

-- 日期计算
SELECT DATE_ADD(NOW(), INTERVAL 7 DAY);      -- 7天后
SELECT DATE_SUB(NOW(), INTERVAL 1 MONTH);    -- 1个月前
SELECT DATEDIFF('2024-12-31', '2024-01-01'); -- 相差天数

-- 常用格式符
-- %Y: 4位年份  %y: 2位年份
-- %m: 月份(00-12)  %c: 月份(0-12)
-- %d: 日期(00-31)  %e: 日期(0-31)
-- %H: 小时(00-23)  %h: 小时(01-12)
-- %i: 分钟  %s: 秒
-- %p: AM/PM
```

### 8.4 聚合函数

| 函数 | 说明 |
|------|------|
| COUNT(*) | 统计行数（包括 NULL） |
| COUNT(列) | 统计非 NULL 的行数 |
| COUNT(DISTINCT 列) | 统计去重后的行数 |
| SUM(列) | 求和 |
| AVG(列) | 平均值 |
| MAX(列) | 最大值 |
| MIN(列) | 最小值 |

**注意**：
- 聚合函数会自动忽略 NULL 值
- 聚合函数不能作为 WHERE 子句的条件

### 8.5 流程控制函数

| 函数 | 说明 |
|------|------|
| IF(condition, true_value, false_value) | 条件判断 |
| IFNULL(column, value) | 如果为 NULL 返回 value |
| NULLIF(expr1, expr2) | 相等返回 NULL，否则返回 expr1 |
| CASE WHEN ... THEN ... END | 多条件判断 |

```sql
-- IF 示例
SELECT ename, IF(sal > 2000, 'High', 'Low') AS sal_level FROM emp;

-- IFNULL 示例（处理 NULL 参与计算）
SELECT ename, sal + IFNULL(comm, 0) AS total FROM emp;

-- CASE 示例
SELECT ename,
    CASE 
        WHEN sal < 1000 THEN 'C'
        WHEN sal < 2000 THEN 'B'
        ELSE 'A'
    END AS grade
FROM emp;

-- CASE 另一种形式
SELECT ename,
    CASE job
        WHEN 'MANAGER' THEN '经理'
        WHEN 'SALESMAN' THEN '销售'
        ELSE '其他'
    END AS job_cn
FROM emp;
```

### 8.6 练习

```sql
-- 1. 将员工姓名转小写
SELECT LOWER(ename) FROM emp;

-- 2. 查询姓名长度为 5 的员工
SELECT * FROM emp WHERE CHAR_LENGTH(ename) = 5;

-- 3. 显示所有员工姓名的首字母
SELECT LEFT(ename, 1) FROM emp;

-- 4. 将入职日期显示为不带 - 的格式
SELECT DATE_FORMAT(hiredate, '%Y%m%d') FROM emp;

-- 5. 求员工的平均薪水，保留 2 位小数
SELECT ROUND(AVG(sal), 2) FROM emp;

-- 6. 计算公司每月发出的薪水总数
SELECT SUM(sal + IFNULL(comm, 0)) AS total_sal FROM emp;

-- 7. 查询每个员工的年收入
SELECT ename, (sal + IFNULL(comm, 0)) * 12 AS annual_income FROM emp;

-- 8. 查询领导人数
SELECT COUNT(DISTINCT mgr) FROM emp WHERE mgr IS NOT NULL;
```

---

## 九、多表查询

### 9.1 连接查询

多表连接查询时如果没有连接条件限制，会产生笛卡尔积现象（结果数为两表记录数的乘积）。

```sql
-- 笛卡尔积（14 * 4 = 56 条记录）
SELECT * FROM emp, dept;
```

### 9.2 内连接 INNER JOIN

返回两个表中满足连接条件的记录。

```sql
-- 标准语法
SELECT * FROM emp 
JOIN dept ON emp.deptno = dept.deptno;

-- 旧语法
SELECT * FROM emp, dept 
WHERE emp.deptno = dept.deptno;

-- 等值连接
SELECT e.ename, d.dname
FROM emp e
JOIN dept d ON e.deptno = d.deptno;

-- 非等值连接：查询员工的薪水等级
SELECT e.ename, e.sal, s.grade
FROM emp e
JOIN salgrade s ON e.sal BETWEEN s.losal AND s.hisal;

-- 自连接：查询员工及其领导
SELECT e1.ename AS employee, e2.ename AS manager
FROM emp e1
JOIN emp e2 ON e1.mgr = e2.empno;
```

### 9.3 外连接 OUTER JOIN

返回匹配的记录，同时返回某张表的所有记录（未匹配部分用 NULL 填充）。

```sql
-- 左外连接：返回左表所有记录
SELECT e.ename, d.dname
FROM emp e
LEFT JOIN dept d ON e.deptno = d.deptno;

-- 右外连接：返回右表所有记录
SELECT e.ename, d.dname
FROM emp e
RIGHT JOIN dept d ON e.deptno = d.deptno;

-- 全外连接（MySQL 不支持，可用 UNION 模拟）
SELECT * FROM emp LEFT JOIN dept ON ...
UNION
SELECT * FROM emp RIGHT JOIN dept ON ...;
```

### 9.4 子查询

嵌套在其他 SQL 语句中的查询。

#### 单行子查询

```sql
-- 查询薪水比公司平均薪水高的员工
SELECT ename, sal 
FROM emp 
WHERE sal > (SELECT AVG(sal) FROM emp);
```

#### 多行子查询

```sql
-- 使用 IN
SELECT * FROM emp 
WHERE deptno IN (SELECT deptno FROM dept WHERE dname LIKE '%E%');

-- 使用 ALL
SELECT * FROM emp
WHERE sal > ALL (SELECT AVG(sal) FROM emp GROUP BY deptno);

-- 使用 ANY/SOME
SELECT * FROM emp
WHERE sal > ANY (SELECT AVG(sal) FROM emp GROUP BY deptno);
```

#### 相关子查询

```sql
-- 查询部门名和部门人数
SELECT dname, 
    (SELECT COUNT(*) FROM emp WHERE emp.deptno = dept.deptno) AS emp_count
FROM dept;
```

#### EXISTS 子查询

```sql
-- 查询有员工的部门
SELECT * FROM dept d
WHERE EXISTS (SELECT 1 FROM emp e WHERE e.deptno = d.deptno);

-- 查询没有员工的部门
SELECT * FROM dept d
WHERE NOT EXISTS (SELECT 1 FROM emp e WHERE e.deptno = d.deptno);
```

### 9.5 练习

```sql
-- 1. 查询员工名及对应部门名
SELECT e.ename, d.dname 
FROM emp e 
JOIN dept d ON e.deptno = d.deptno;

-- 2. 查询员工名、领导名和部门名
SELECT e.ename, m.ename AS manager, d.dname
FROM emp e
LEFT JOIN emp m ON e.mgr = m.empno
JOIN dept d ON e.deptno = d.deptno;

-- 3. 查询薪水高于公司平均薪水的员工
SELECT ename, sal 
FROM emp 
WHERE sal > (SELECT AVG(sal) FROM emp);

-- 4. 查询每个部门的平均薪水及对应等级
SELECT t.deptno, t.avg_sal, s.grade
FROM (SELECT deptno, AVG(sal) AS avg_sal FROM emp GROUP BY deptno) t
JOIN salgrade s ON t.avg_sal BETWEEN s.losal AND s.hisal;
```

---

## 十、索引

### 10.1 索引概述

索引是存储引擎用于快速找到数据的一种数据结构。

**优点**：
- 减少服务器需要扫描的数据量
- 帮助服务器避免排序和临时表
- 将随机 I/O 变为顺序 I/O

**缺点**：
- 占用物理空间
- 创建和维护需要时间
- 增删改操作需要维护索引

### 10.2 常见索引类型

| 类型 | 说明 |
|------|------|
| B+树索引 | 最常用的索引类型，支持范围查找和排序 |
| 哈希索引 | 基于哈希表，只支持等值查询，InnoDB 自适应 |
| 全文索引 | 用于全文搜索，InnoDB 5.6+ 支持 |
| 空间索引 | 用于地理空间数据 |

### 10.3 索引操作

```sql
-- 创建索引
CREATE INDEX 索引名 ON 表名(列名);
CREATE INDEX 索引名 ON 表名(列名(前缀长度));  -- 前缀索引

-- 创建唯一索引
CREATE UNIQUE INDEX 索引名 ON 表名(列名);

-- 创建联合索引
CREATE INDEX 索引名 ON 表名(列1, 列2, 列3);

-- 查看索引
SHOW INDEX FROM 表名;

-- 删除索引
DROP INDEX 索引名 ON 表名;

-- 分析查询是否使用索引
EXPLAIN SELECT * FROM emp WHERE ename = 'SMITH';
```

### 10.4 EXPLAIN 分析

| 字段 | 说明 |
|------|------|
| id | 查询序列号 |
| select_type | 查询类型（SIMPLE, PRIMARY, SUBQUERY 等） |
| table | 访问的表 |
| type | 访问类型（system > const > eq_ref > ref > range > index > ALL） |
| possible_keys | 可能使用的索引 |
| key | 实际使用的索引 |
| key_len | 使用索引的长度 |
| rows | 估计需要扫描的行数 |
| Extra | 额外信息 |

**type 类型说明**：
- **system/const**：单表最多一行，根据主键或唯一索引查询
- **eq_ref**：唯一索引扫描，对于每个索引键，表中只有一条记录匹配
- **ref**：非唯一索引扫描
- **range**：索引范围扫描（BETWEEN, <, >, IN）
- **index**：扫描全部索引树
- **ALL**：全表扫描（需要优化）

### 10.5 索引设计原则

1. **为常作为查询条件的字段建立索引**
2. **为经常需要排序、分组的字段建立索引**
3. **创建唯一性索引**
4. **限制索引的数目**
5. **小表不建议建索引**（数据量小于百万）
6. **尽量使用前缀索引**（长字符串）
7. **删除不再使用的索引**

### 10.6 最左前缀原则

创建多列联合索引时，查询条件必须从索引的最左列开始匹配。

```sql
-- 创建联合索引 (a, b, c)
CREATE INDEX idx_abc ON emp(a, b, c);

-- 能使用索引
SELECT * FROM emp WHERE a = 1;
SELECT * FROM emp WHERE a = 1 AND b = 2;
SELECT * FROM emp WHERE a = 1 AND b = 2 AND c = 3;
SELECT * FROM emp WHERE a = 1 AND c = 3;  -- 只使用 a 的索引（索引下推）

-- 不能使用索引
SELECT * FROM emp WHERE b = 2;
SELECT * FROM emp WHERE c = 3;
SELECT * FROM emp WHERE b = 2 AND c = 3;
```

### 10.7 索引失效场景

```sql
-- 1. 在索引列上使用函数或表达式
SELECT * FROM emp WHERE YEAR(hiredate) = 1981;  -- 失效
SELECT * FROM emp WHERE hiredate BETWEEN '1981-01-01' AND '1981-12-31';  -- 有效

-- 2. 使用 != 或 <>
SELECT * FROM emp WHERE sal != 5000;  -- 可能失效

-- 3. 使用 OR 条件（部分情况）
SELECT * FROM emp WHERE empno = 7396 OR job = 'CLERK';  -- 可能失效

-- 4. LIKE 以 % 开头
SELECT * FROM emp WHERE ename LIKE '%S%';  -- 失效
SELECT * FROM emp WHERE ename LIKE 'S%';   -- 有效

-- 5. 类型转换
SELECT * FROM emp WHERE empno = '7396';  -- 可能失效

-- 6. 使用 NOT IN
SELECT * FROM emp WHERE deptno NOT IN (10, 20);  -- 可能失效
```

---

## 十一、视图

视图是由查询结果形成的虚拟表，不存储实际数据。

### 11.1 视图操作

```sql
-- 创建视图
CREATE VIEW 视图名 AS 查询语句;

-- 修改视图
ALTER VIEW 视图名 AS 查询语句;

-- 删除视图
DROP VIEW [IF EXISTS] 视图名;

-- 查看视图定义
SHOW CREATE VIEW 视图名;
```

### 11.2 视图示例

```sql
-- 创建视图
CREATE VIEW emp_dept_view AS
SELECT e.empno, e.ename, d.dname, d.loc
FROM emp e
JOIN dept d ON e.deptno = d.deptno;

-- 查询视图
SELECT * FROM emp_dept_view;

-- 通过视图更新数据（有限制）
UPDATE emp_dept_view SET loc = 'NEW YORK' WHERE empno = 7369;
```

### 11.3 视图特点

- **重用 SQL 语句**
- **简化复杂查询**
- **安全性**：限制用户只能访问特定数据
- **逻辑独立性**：底层表结构变化时，视图可以保持不变

---

## 十二、存储过程和函数

### 12.1 存储过程

存储过程是一组预编译的 SQL 语句和流程控制语句。

```sql
-- 创建存储过程
DELIMITER //
CREATE PROCEDURE 存储过程名(
    [IN | OUT | INOUT] 参数名 数据类型
)
BEGIN
    -- 逻辑代码
END //
DELIMITER ;

-- 调用存储过程
CALL 存储过程名(参数);

-- 删除存储过程
DROP PROCEDURE [IF EXISTS] 存储过程名;
```

### 12.2 参数类型

| 类型 | 说明 |
|------|------|
| IN | 输入参数（默认），存储过程内部只读 |
| OUT | 输出参数，存储过程可以修改并返回 |
| INOUT | 既可输入又可输出 |

### 12.3 变量和流程控制

```sql
-- 声明变量
DECLARE 变量名 数据类型 [DEFAULT 默认值];

-- 变量赋值
SET 变量名 = 值;
SELECT 列名 INTO 变量名 FROM 表名;

-- IF 语句
IF 条件 THEN
    -- 代码
ELSEIF 条件 THEN
    -- 代码
ELSE
    -- 代码
END IF;

-- CASE 语句
CASE 
    WHEN 条件1 THEN 代码1;
    WHEN 条件2 THEN 代码2;
    ELSE 代码3;
END CASE;

-- WHILE 循环
WHILE 条件 DO
    -- 代码
END WHILE;

-- REPEAT 循环
REPEAT
    -- 代码
UNTIL 条件 END REPEAT;

-- LOOP 循环
标签: LOOP
    -- 代码
    LEAVE 标签;  -- 退出循环
END LOOP;
```

### 12.4 存储过程示例

```sql
-- 计算前 n 项和
DELIMITER //
CREATE PROCEDURE calc_sum(IN n INT, OUT result INT)
BEGIN
    DECLARE total INT DEFAULT 0;
    WHILE n > 0 DO
        SET total = total + n;
        SET n = n - 1;
    END WHILE;
    SET result = total;
END //
DELIMITER ;

-- 调用
SET @sum = 0;
CALL calc_sum(100, @sum);
SELECT @sum;
```

### 12.5 自定义函数

```sql
-- 创建函数
DELIMITER //
CREATE FUNCTION 函数名(参数 数据类型)
RETURNS 返回类型
DETERMINISTIC  -- 声明确定性（相同输入产生相同输出）
BEGIN
    -- 函数逻辑
    RETURN 值;
END //
DELIMITER ;

-- 调用函数
SELECT 函数名(参数);

-- 删除函数
DROP FUNCTION [IF EXISTS] 函数名;
```

```sql
-- 示例：获取员工最高薪水
DELIMITER //
CREATE FUNCTION get_max_sal()
RETURNS DECIMAL(10, 2)
DETERMINISTIC
BEGIN
    DECLARE max_sal DECIMAL(10, 2);
    SELECT MAX(sal) INTO max_sal FROM emp;
    RETURN max_sal;
END //
DELIMITER ;

-- 调用
SELECT get_max_sal();
```

### 12.6 游标

游标用于逐行处理查询结果。

```sql
DELIMITER //
CREATE PROCEDURE cursor_demo()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE emp_name VARCHAR(20);
    DECLARE emp_sal DECIMAL(10, 2);
    
    -- 声明游标
    DECLARE cur CURSOR FOR 
        SELECT ename, sal FROM emp;
    
    -- 声明句柄：数据读取完毕时设置 done = 1
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;
    
    -- 打开游标
    OPEN cur;
    
    -- 循环读取
    read_loop: LOOP
        FETCH cur INTO emp_name, emp_sal;
        IF done = 1 THEN
            LEAVE read_loop;
        END IF;
        -- 处理每一行数据
        SELECT emp_name, emp_sal;
    END LOOP;
    
    -- 关闭游标
    CLOSE cur;
END //
DELIMITER ;
```

**注意**：DECLARE 的顺序必须是：局部变量 → 游标 → 句柄

---

## 十三、触发器

触发器在表的 INSERT、UPDATE、DELETE 操作时自动执行。

### 13.1 触发器操作

```sql
-- 创建触发器
CREATE TRIGGER 触发器名
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON 表名
FOR EACH ROW
BEGIN
    -- 触发器逻辑
END;

-- 删除触发器
DROP TRIGGER [IF EXISTS] 触发器名;

-- 查看触发器
SHOW TRIGGERS;
```

### 13.2 NEW 和 OLD

| 操作 | NEW | OLD |
|------|-----|-----|
| INSERT | 新插入的行 | 无 |
| UPDATE | 更新后的行 | 更新前的行 |
| DELETE | 无 | 被删除的行 |

### 13.3 触发器示例

```sql
-- 创建日志表
CREATE TABLE emp_log(
    id INT AUTO_INCREMENT PRIMARY KEY,
    operation VARCHAR(10),
    empno INT,
    ename VARCHAR(20),
    opt_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 创建触发器：记录员工删除操作
CREATE TRIGGER trg_emp_delete
AFTER DELETE ON emp
FOR EACH ROW
BEGIN
    INSERT INTO emp_log(operation, empno, ename)
    VALUES('DELETE', OLD.empno, OLD.ename);
END;

-- 创建触发器：部门更新时自动给员工涨工资
CREATE TRIGGER trg_dept_update
AFTER UPDATE ON dept
FOR EACH ROW
BEGIN
    UPDATE emp
    SET sal = sal + 100
    WHERE deptno = NEW.deptno;
END;
```

---

## 十四、事务和锁

### 14.1 事务概述

事务是一组原子性的 SQL 查询，要么全部执行成功，要么全部失败回滚。

### 14.2 事务操作

```sql
-- 开启事务
START TRANSACTION;
-- 或
BEGIN;

-- 提交事务
COMMIT;

-- 回滚事务
ROLLBACK;

-- 设置保存点
SAVEPOINT 保存点名;

-- 回滚到保存点
ROLLBACK TO 保存点名;

-- 查看自动提交设置
SHOW VARIABLES LIKE 'autocommit';

-- 设置自动提交
SET autocommit = 1;  -- 开启（默认）
SET autocommit = 0;  -- 关闭
```

### 14.3 ACID 特性

| 特性 | 说明 |
|------|------|
| **原子性 (Atomicity)** | 事务是最小工作单元，要么全部成功，要么全部失败 |
| **一致性 (Consistency)** | 数据库总是从一个一致状态转换到另一个一致状态 |
| **隔离性 (Isolation)** | 事务的修改在提交前对其他事务不可见 |
| **持久性 (Durability)** | 事务一旦提交，修改永久保存 |

### 14.4 并发问题

| 问题 | 说明 |
|------|------|
| **脏读 (Dirty Read)** | 读到其他事务未提交的数据 |
| **不可重复读 (Non-repeatable Read)** | 同一事务内多次读取同一数据，结果不一致 |
| **幻读 (Phantom Read)** | 同一事务内按相同条件查询，结果集数量不同 |

### 14.5 隔离级别

| 隔离级别 | 脏读 | 不可重复读 | 幻读 | 说明 |
|----------|------|------------|------|------|
| READ UNCOMMITTED | ✓ | ✓ | ✓ | 未提交读，性能最好，问题最多 |
| READ COMMITTED | ✗ | ✓ | ✓ | 提交读，Oracle 默认 |
| REPEATABLE READ | ✗ | ✗ | ✓ | 可重复读，MySQL 默认 |
| SERIALIZABLE | ✗ | ✗ | ✗ | 串行化，性能最差，最安全 |

```sql
-- 查看隔离级别
SELECT @@transaction_isolation;  -- MySQL 8.0+
SELECT @@tx_isolation;           -- MySQL 5.7

-- 设置隔离级别
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

### 14.6 锁机制

#### 锁类型

| 锁类型 | 说明 |
|--------|------|
| **共享锁 (S Lock)** | 读锁，允许事务读取数据 |
| **排他锁 (X Lock)** | 写锁，允许事务删除或更新数据 |

#### 锁兼容性

|  | X 锁 | S 锁 |
|--|------|------|
| **X 锁** | 冲突 | 冲突 |
| **S 锁** | 冲突 | 兼容 |

#### 意向锁

| 锁类型 | 说明 |
|--------|------|
| **意向共享锁 (IS)** | 事务有意向对表中某些行加共享锁 |
| **意向排他锁 (IX)** | 事务有意向对表中某些行加排他锁 |

#### 锁定读

```sql
-- 加排他锁
SELECT * FROM emp WHERE empno = 7369 FOR UPDATE;

-- 加共享锁
SELECT * FROM emp WHERE empno = 7369 LOCK IN SHARE MODE;
```

#### 行锁算法

| 锁类型 | 说明 |
|--------|------|
| **Record Lock** | 锁定单行记录 |
| **Gap Lock** | 间隙锁，锁定范围但不包含记录 |
| **Next-Key Lock** | Record Lock + Gap Lock，锁定范围并包含记录 |

---

## 十五、DCL（数据控制语言）

Data Control Language，用于管理用户权限。

### 15.1 用户管理

```sql
-- 创建用户
CREATE USER '用户名'@'主机' IDENTIFIED BY '密码';

-- 示例
CREATE USER 'zhangsan'@'localhost' IDENTIFIED BY '123456';
CREATE USER 'lisi'@'%' IDENTIFIED BY '123456';  -- % 表示任意主机

-- 修改密码
ALTER USER '用户名'@'主机' IDENTIFIED BY '新密码';
SET PASSWORD FOR '用户名'@'主机' = PASSWORD('新密码');

-- 删除用户
DROP USER '用户名'@'主机';

-- 查看所有用户
SELECT user, host FROM mysql.user;
```

### 15.2 权限管理

#### 授权

```sql
GRANT 权限列表 ON 数据库.表 TO '用户名'@'主机';

-- 常用权限
-- ALL PRIVILEGES: 所有权限
-- SELECT: 查询权限
-- INSERT: 插入权限
-- UPDATE: 更新权限
-- DELETE: 删除权限
-- CREATE: 创建数据库/表
-- DROP: 删除数据库/表
-- INDEX: 创建/删除索引
-- ALTER: 修改表结构
-- EXECUTE: 执行存储过程

-- 示例
GRANT SELECT ON mydb.* TO 'zhangsan'@'localhost';  -- 只读权限
GRANT ALL PRIVILEGES ON mydb.* TO 'lisi'@'%';      -- 所有权限
GRANT SELECT, INSERT, UPDATE ON mydb.emp TO 'wangwu'@'%';
```

#### 撤销权限

```sql
REVOKE 权限列表 ON 数据库.表 FROM '用户名'@'主机';

-- 示例
REVOKE INSERT ON mydb.emp FROM 'wangwu'@'%';
REVOKE ALL PRIVILEGES ON mydb.* FROM 'lisi'@'%';
```

#### 刷新权限

```sql
FLUSH PRIVILEGES;
```

### 15.3 查看权限

```sql
-- 查看当前用户权限
SHOW GRANTS;

-- 查看指定用户权限
SHOW GRANTS FOR '用户名'@'主机';
```

### 15.4 权限示例

```sql
-- 1. 创建只读用户
CREATE USER 'readonly'@'%' IDENTIFIED BY 'readonly123';
GRANT SELECT ON *.* TO 'readonly'@'%';

-- 2. 创建应用用户（只能操作特定数据库）
CREATE USER 'appuser'@'%' IDENTIFIED BY 'app123';
GRANT SELECT, INSERT, UPDATE, DELETE ON myapp.* TO 'appuser'@'%';

-- 3. 创建管理员用户
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'admin123';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;

-- 刷新权限
FLUSH PRIVILEGES;
```

---

## 十六、数据库设计

### 16.1 三范式

#### 第一范式（1NF）

**要求**：每列具有原子性，字段不可分割。

```
❌ 不满足 1NF：
| 学生编号 | 学生姓名 | 联系方式              |
|----------|----------|-----------------------|
| 1001     | 张三     | zs@163.com,13678653456|

✅ 满足 1NF：
| 学生编号 | 学生姓名 | 邮箱         | 电话        |
|----------|----------|--------------|-------------|
| 1001     | 张三     | zs@163.com   | 13678653456 |
```

#### 第二范式（2NF）

**要求**：在 1NF 基础上，非主键字段必须完全依赖主键（消除部分依赖）。

```
❌ 不满足 2NF（联合主键：学生编号 + 课程编号）：
| 学生编号 | 课程编号 | 学生姓名 | 成绩 |
|----------|----------|----------|------|
| 1001     | 001      | 张三     | 90   |

问题：学生姓名只依赖学生编号，不依赖课程编号

✅ 满足 2NF：
学生表：| 学生编号 | 学生姓名 |
成绩表：| 学生编号 | 课程编号 | 成绩 |
```

#### 第三范式（3NF）

**要求**：在 2NF 基础上，非主键字段必须直接依赖主键（消除传递依赖）。

```
❌ 不满足 3NF：
| 学生编号 | 班级编号 | 学生姓名 | 班级名   |
|----------|----------|----------|----------|
| 1001     | 01       | 张三     | 一年一班 |

问题：班级名依赖班级编号，班级编号依赖学生编号（传递依赖）

✅ 满足 3NF：
学生表：| 学生编号 | 班级编号 | 学生姓名 |
班级表：| 班级编号 | 班级名   |
```

### 16.2 范式总结

| 范式 | 要求 |
|------|------|
| 1NF | 字段不可分割 |
| 2NF | 有主键，非主键完全依赖主键 |
| 3NF | 没有传递依赖 |

**注意**：实际设计中可能需要适当反范式化以提高查询性能。

---

## 十七、常用技巧

### 17.1 批量插入优化

```sql
-- 方式 1：单条 INSERT（慢）
INSERT INTO emp(ename) VALUES('A');
INSERT INTO emp(ename) VALUES('B');
INSERT INTO emp(ename) VALUES('C');

-- 方式 2：多条 VALUES（快）
INSERT INTO emp(ename) VALUES('A'), ('B'), ('C');

-- 方式 3：LOAD DATA（最快）
LOAD DATA INFILE '/path/to/data.csv' 
INTO TABLE emp 
FIELDS TERMINATED BY ',' 
LINES TERMINATED BY '\n';
```

### 17.2 分页优化

```sql
-- 低效分页（大数据量时慢）
SELECT * FROM emp LIMIT 1000000, 10;

-- 优化方式 1：使用主键范围
SELECT * FROM emp WHERE empno > 1000000 LIMIT 10;

-- 优化方式 2：使用覆盖索引
SELECT * FROM emp 
JOIN (SELECT empno FROM emp LIMIT 1000000, 10) t 
ON emp.empno = t.empno;
```

### 17.3 常用系统表查询

```sql
-- 查看所有数据库
SHOW DATABASES;
SELECT schema_name FROM information_schema.schemata;

-- 查看所有表
SHOW TABLES;
SELECT table_name FROM information_schema.tables 
WHERE table_schema = '数据库名';

-- 查看表结构
DESC 表名;
SELECT * FROM information_schema.columns 
WHERE table_schema = '数据库名' AND table_name = '表名';

-- 查看索引
SHOW INDEX FROM 表名;
SELECT * FROM information_schema.statistics 
WHERE table_schema = '数据库名' AND table_name = '表名';

-- 查看当前连接
SHOW PROCESSLIST;
SELECT * FROM information_schema.processlist;
```

### 17.4 数据导入导出

```sql
-- 导出数据（命令行）
mysqldump -u root -p 数据库名 > backup.sql
mysqldump -u root -p 数据库名 表名 > table_backup.sql

-- 导入数据（命令行）
mysql -u root -p 数据库名 < backup.sql

-- 导出为 CSV
SELECT * INTO OUTFILE '/path/to/output.csv'
FIELDS TERMINATED BY ','
OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\n'
FROM 表名;
```

---

## 附录：常用命令速查

### 数据库操作
```sql
SHOW DATABASES;
CREATE DATABASE dbname;
USE dbname;
DROP DATABASE dbname;
```

### 表操作
```sql
SHOW TABLES;
DESC tablename;
CREATE TABLE ...;
ALTER TABLE ...;
DROP TABLE tablename;
TRUNCATE TABLE tablename;
```

### 数据操作
```sql
SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY ... LIMIT ...;
INSERT INTO ... VALUES ...;
UPDATE ... SET ... WHERE ...;
DELETE FROM ... WHERE ...;
```

### 用户权限
```sql
CREATE USER 'user'@'host' IDENTIFIED BY 'password';
GRANT privileges ON db.table TO 'user'@'host';
REVOKE privileges ON db.table FROM 'user'@'host';
DROP USER 'user'@'host';
FLUSH PRIVILEGES;
```

### 事务控制
```sql
START TRANSACTION;
COMMIT;
ROLLBACK;
SAVEPOINT spname;
ROLLBACK TO spname;
```
