## 第五章 DDL语言

### 一、数据库的管理

#### 1. 库的创建

```sql
CREATE DATABASE 【IF NOT EXISTS】 库名 【 CHARACTER SET 字符集名】;
```

#### 5.1.2、库的修改

```sql
修改库字符集
ALTER DATABASE 库名 CHARACTER SET 字符集名; 
```

#### 5.1.3、库的删除

```sql
DROP DATABASE 【IF EXISTS】 库名;
```

### 二、表的管理

#### 5.2.1、表的创建

```sql
CREATE TABLE 【IF NOT EXISTS】 表名 (
  字段名 字段类型 【约束】,
  字段名 字段类型 【约束】,
  ...
  字段名 字段类型 【约束】
) ;
```

#### 5.2.2、表的修改

- <font color=red> 添加列 </font>

  ```sql
  ALTER TABLE 表名 ADD COLUMN 列名 类型 【FIRST|AFTER 字段名】; 
  ```

- <font color=red> 修改列的类型或约束 </font>

  ```sql
  ALTER TABLE 表名 MODIFY COLUMN 列名 新类型 【新约束】; 
  ```

- <font color=red> 修改列名 </font>

  ```sql
  ALTER TABLE 表名 CHANGE COLUMN 旧列名 新列名 类型; 
  ```

- <font color=red> 删除列 </font>

  ```sql
  ALTER TABLE 表名 DROP COLUMN 列名; 
  ```

- <font color=red> 修改表名 </font>

  ```sql
  ALTER TABLE 表名 RENAME 【TO】 新表名; 
  ```

#### 5.2.3、表的删除

- <font color=red> 方式一： </font>

  ```sql
  DROP TABLE 【IF EXISTS】 表名; 
  ```

- <font color=red> 方式二： </font>

  ```sql
  TRUNCATE TABLE 【IF EXISTS】 表名; 
  ```

#### 5.2.4、表的复制

- <font color=red>  复制表的结构 </font>

  ```sql
  CREATE TABLE 表名 LIKE 旧表;
  ```

- <font color=red> 复制表的某些字段 </font>

  ```sql
  CREATE TABLE 表名 SELECT 字段1, 字段2,... FROM 旧表 WHERE 0;
  ```

- <font color=red> 复制表的结构+数据 </font>

  ```sql
  CREATE TABLE 表名  SELECT 查询列表 FROM 旧表 【WHERE 筛选条件】; 
  ```

- <font color=red> 复制表的某些字段+数据 </font>

  ```sql
  CREATE TABLE 表名  SELECT 字段1,字段2,... FROM 旧表 【WHERE 筛选条件】; 
  ```

### 三、数据类型

- <font color=red><b>数值型</b></font>
  
| 数值型 | TINYINT | SMALLINT | MEDIUMINT | INT/INTEGER | BIGINT |
| ------ | ------- | -------- | --------- | ----------- | ------ |
| 字节   | 1       | 2        | 3         | 4           | 8      |
  1. 都可以设置无符号和有符号，默认有符号，通过unsigned设置无符号
  2. 如果对数据没有特殊要求，则优先考虑使用INT/INTEGER

- <font color=red><b>字符型</b></font>

| 类型 | CHAR         | VARCHAR      | BINARY       | VARBINARY    | ENUM | SET  | TEXT | BLOB           |
| ---- | ------------ | ------------ | ------------ | ------------ | ---- | ---- | ---- | -------------- |
| 描述 | 固定长度字符 | 可变长度字符 | 二进制字符串 | 二进制字符串 | 枚举 | 集合 | 文本 | 二进制大型对象 |
  1. char：固定长度的字符，写法为char(M)，最大长度不能超过M，其中M可以省略，默认为1
  2. varchar：可变长度的字符，写法为varchar(M)，最大长度不能超过M，其中M不可以省略
  3. 如果对数据没有特殊要求，则优先考虑使用VARCHAR

- <font color=red><b>浮点型</b></font>

| 类型   | TINYINT            | SMALLINT              |
| ------ | ------------------ | --------------------- |
| 定点数 | DEC(M,D) ：M+2字节 | DECIMAL(M,D)：M+2字节 |
| 浮点数 | FLOAT(M,D) ：4字节 | DOUBLE(M,D)：8字节    |

  1. M代表整数部位+小数部位的个数，D代表小数部位
  2. M和D都可以省略，但对于定点数，M默认为10，D默认为0
  3. 如果精度要求较高，则优先考虑使用定点数

- <font color=red><b>日期型</b></font>

| 类型 | YEAR | DATE | TIME | DATETIME  | TIMESTAMP |
| ---- | ---- | ---- | ---- | --------- | --------- |
| 描述 | 年份 | 日期 | 时间 | 日期+时间 | 日期+时间 |

  1. TIMESTAMP比较容易受时区、语法模式、版本的影响，更能反映当前时区的真实时间
  2. DATETIME只能反映出插入时的当地时区
  3. 如果对数据没有特殊要求，则优先考虑使用DATETIME

### 5.4、常见约束

#### 1. 含义与分类

 约束是一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性 

1. NOT NULL：非空，该字段的值必填
2. UNIQUE：唯一，该字段的值不可重复
3. DEFAULT：默认，该字段的值不用手动插入有默认值
4. CHECK：检查，MySQL不支持
5. PRIMARY KEY：主键，该字段的值不可重复并且非空 unique+not null
6. FOREIGN KEY：外键，该字段的值引用了另外的表的字段

#### 2. <font>创建表时添加约束 </font>

```sql
CREATE TABLE 表名(
	字段名 字段类型 NOT NULL,#非空
    字段名 字段类型 DEFAULT 值,#默认
	字段名 字段类型 PRIMARY KEY,#主键
	字段名 字段类型 UNIQUE,#唯一
	CONSTRAINT 约束名 FOREIGN KEY(字段名) REFERENCES 主表(被引用列)
) ;
```

#### 3. <font > 修改表时添加或删除约束 </font>

- <font color=red>添加非空（列级约束）</font>

  ```sql
  ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 NOT NULL;
  ```

- <font color=red>删除非空</font>

  ```sql
  ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型;
  ```

- <font color=red>添加默认（列级约束）</font>

  ```sql
  ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 DEFAULT 值;
  ```

- <font color=red>删除默认</font>

  ```sql
  ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型;
  ```

- <font color=red>添加主键（列级约束）</font>

  ```sql
  ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 PRIMARY KEY;
  ```

- <font color=red>添加主键（表级约束）</font>

  ```sql
  ALTER TABLE 表名 add 【CONSTRAINT 约束名】 PRIMARY KEY(字段名);
  ```

- <font color=red>删除主键</font>

  ```sql
  ALTER TABLE 表名 DROP PRIMARY KEY;
  ```

- <font color=red>添加唯一（列级约束）</font>

  ```sql
  ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 UNIQUE;
  ```

- <font color=red>添加唯一（表级约束）</font>

  ```sql
  ALTER TABLE 表名 add 【CONSTRAINT 约束名】 UNIQUE(字段名);
  ```

- <font color=red>删除唯一</font>

  ```sql
  ALTER TABLE 表名 DROP INDEX 索引名;
  ```

### 5.5、自增长列

- <font color=red> 创建表时添加自增长列 </font>

  ```sql
  CREATE TABLE 表名 (
    字段名 字段类型 约束 AUTO_INCREMENT
  ) ;
  ```

- <font color=red> 修改表时添加自增长列 </font>

  ```sql
  ALTER TABLE 表 MODIFY COLUMN 字段名 字段类型 约束 AUTO_INCREMENT; 
  ```

- <font color=red> 修改表时删除自增长列 </font>

  ```sql
  ALTER TABLE 表 MODIFY COLUMN 字段名 字段类型 约束; 
  ```

  

#### 数据的三范式

第一范式： 任何一张表都应该有主键，并且每个字段原子性不可再分

第二范式：建议在第一范式的基础上，所有非主键字段完全依赖主键，不能产生部分依赖

多对多（学生表， 教师表，学生教师关系表） 

第三范式：建议在第二范式基础上，所有非主键字段直接依赖主键，不能产生部分依赖(传递依赖)

学生表 id name class_id   [class_name]这个端依赖class_id,传递依赖

班级表 id class_name 

注：以冗余换速度

