## 第四章 DML语言

### 一、 插入语句

#### 1、语法

```sql
方式一：
INSERT INTO 表名(字段名,...) VALUES(值,...);

方式二：
INSERT INTO 表名 SET 字段名=值,字段名=值,...;
```

#### 2、特点

<div><font size=2.5>
1. 要求值的类型和字段的类型要一致或兼容<br>
2. 字段的个数和顺序不一定与原始表中的字段个数和顺序一致，但必须保证值和字段一一对应<br>
3. 假如表中有可以为null的字段，注意可以通过以下两种方式插入null值：<br>
&nbsp&nbsp&nbsp&nbsp①字段和值都省略、②字段写上，值使用null<br>
4. 字段和值的个数必须一致<br>
5. 字段名可以省略，默认所有列v
6. 方式一支持一次插入多行，语法如下：INSERT INTO 表名【(字段名,…)】 VALUES(值,…),(值,…),…;<br>
7. 方式一支持子查询，语法如下：INSERT INTO 表名 查询语句;
</font>
</div>

```sql
INSERT INTO 
	beauty ( id, NAME, sex, borndate, phone, photo, boyfriend_id )
VALUES
	( 15, '唐艺昕', '女', '1997-12-05', '15633029014', NULL, 2 );
```

```sql
INSERT INTO 
	beauty 
SET 
	id = 19,
	name = '张倩倩',
	sex = '女',
	borndate = '1997-12-05',
	phone = '15633029014',
	photo = null,
	boyfriend_id = 3;
```

### 二、 更新语句

- <font color=red>单表更新 </font>

  ```sql
  UPDATE 表名 SET 列 = 值,... WHERE 查询条件;
  ```

  ```sql
  修改beauty表中姓唐的女神的电话为13899888899
  UPDATE beauty SET phone = '13899888899' WHERE NAME LIKE '唐%';
  ```

- <font color=red>多表更新 </font>

  ```sql
  UPDATE 
    表1 别名,
    表2 别名 
  SET
    列 = 值,
    ...
  WHERE 连接条件 AND 筛选条件 ;
  ```

  ```sql
  修改张无忌的女朋友的手机号为13899888899，魅力值为1000
  UPDATE 
  	boys bo,
  	beauty b 
  SET 
  	b.`phone` = '13899888899',
  	bo.`userCP` = 1000 
  WHERE
  	bo.`id` = b.`boyfriend_id` AND bo.`boyName` = '张无忌';
  ```

  ```sql
  UPDATE 
    表1 别名 
    INNER | LEFT | RIGHT JOIN 表2 别名 ON 连接条件
  SET 
    列 = 值,
    ...
  WHERE 筛选条件 ;
  ```

  ```sql
  修改张无忌的女朋友的手机号为13899888899，魅力值为1000
  UPDATE 
  	boys bo
  	INNER JOIN beauty b ON bo.`id` = b.`boyfriend_id` 
  SET 
  	b.`phone` = '13899888899',
  	bo.`userCP` = 1000 
  WHERE
  	bo.`boyName` = '张无忌';
  ```

### 三、删除语句

- <font color=red>单表删除  </font>

  ```sql
  DELETE FROM 表名 【WHERE 筛选条件 】;
  ```

  ```sql
  删除手机号以9结尾的女神信息
  DELETE FROM beauty WHERE phone LIKE '%9';
  ```

- <font color=red>多表删除（级联删除）  </font>

  ```sql
  DELETE 
    表1的别名,
    表2的别名 
  FROM
    表1 别名,
    表2 别名 
  WHERE 连接条件 AND 筛选条件 ;
  ```

  ```sql
  DELETE 
    b,
    bo 
  FROM
    beauty b,
    boys bo 
  WHERE
    b.`boyfriend_id` = bo.`id` AND bo.`boyName` = '黄晓明';
  ```

  ```sql
  DELETE 
    表1的别名,
    表2的别名 
  FROM
    表1 别名 
    INNER | LEFT | RIGHT JOIN 表2 别名 ON 连接条件 
  WHERE 筛选条件 ;
  ```

  ```sql
  删除黄晓明的信息以及他女朋友的信息
  DELETE 
    b,
    bo 
  FROM
  	beauty b
  	INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id` 
  WHERE
  	bo.`boyName` = '黄晓明';
  ```
  - <font color=red>删除重复数据，保留最后一条记录  </font>

  删除重复数据，保留最后一条记录

  ```sql
  delete from users where id not in (
    select t.max_id from 
    (select max(id) as max_id from users group by identity_id,name) as t
    );
  ```

#### 四、创建视图

- 创建视图(视图本质查询快照)

  ```sql
  create view vw_emp_dept as
  select eno, ename, dname from tb_emp t1 inner join tb_dept t2 on t1.dno=t2.dno;
  ```

- 查询语句

  ```sql
  select ename，dname from vw_emp_dept;
  ```

- 删除视图

  ```
  drop view vw_emp_dept;
  ```

  





