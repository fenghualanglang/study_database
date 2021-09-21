### DQL语言   子查询

#### 一、子查询

嵌套在其它语句内部的select语句称为子查询或内查询，外面的语句可以是insert、delete、update、select等，一般select作为外面语句较多，外面如果为select语句，则此语句称为外查询或主查询 

- ```sql
  - 子查询放在小括号内
  - 子查询一般放在条件的右侧
  - 子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果
  - 标量子查询，一般搭配着单行操作符使用：>、>=、<、<=、!=、<>、=、<=>
  - 列子查询，一般搭配着多行操作符使用：in、not in、any、some、all、exits
  ```

##### <font color=red>1.1  <b>select  后面：标量子查询 </b>结果集为一行一列</font>

```sql
查询每个部门的员工个数
SELECT
	d.*,
	( SELECT COUNT( * ) FROM employees e WHERE e.department_id = d.`department_id` ) 个数 
FROM
	departments d;
```

##### <font color=red>1.2  <b>from  后面：表子查询 </b> 结果集为多行多列</font>

```sql
查询每个部门平均工资的工资等级
SELECT
	ag_dep.*,
	g.`grade_level` 
FROM
	( SELECT AVG( salary ) ag, department_id FROM employees GROUP BY department_id ) ag_dep
	INNER JOIN job_grades g ON ag_dep.ag BETWEEN lowest_sal 
	AND highest_sal;
```

##### <font color=red>1.3  <b> where 或  having后面 </b></font>

- <font size=2 color=blue><b>标量子查询：结果集为一行一列</b></font>

  ```sql
  查询最低工资的员工姓名和工资
  SELECT
  	last_name,
  	salary 
  FROM
  	employees 
  WHERE
  	salary = ( SELECT MIN( salary ) FROM employees );
  ```

- <font size=2 color=blue><b>列子查询：结果集为多行一列</b></font>

  ```sql
  查询所有是领导的员工姓名
  SELECT
  	last_name 
  FROM
  	employees 
  WHERE
  	employee_id IN ( SELECT DISTINCT manager_id FROM employees );
  ```

  ```sql
  返回其它工种中比job_id为'IT_PROG'工种任一工资低的员工的员工号、姓名、job_id以及salary
  SELECT
  	employee_id,
  	last_name,
  	job_id,
  	salary 
  FROM
  	employees 
  WHERE
  	salary < ANY ( SELECT DISTINCT salary FROM employees WHERE job_id = 'IT_PROG' ) 
  	AND job_id <> 'IT_PROG';
  ```

  ```sql
  返回其它部门中比job_id为'IT_PROG'部门所有工资都低的员工的员工号、姓名、job_id以及salary
  SELECT
  	employee_id,
  	last_name,
  	job_id,
  	salary 
  FROM
  	employees 
  WHERE
  	salary < ALL ( SELECT DISTINCT salary FROM employees WHERE job_id = 'IT_PROG' ) 
  	AND job_id <> 'IT_PROG';
  ```

  ```sql
  返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工的员工号、姓名、job_id以及salary
  SELECT
  	employee_id,
  	last_name,
  	job_id,
  	salary 
  FROM
  	employees 
  WHERE
  	salary < ALL ( SELECT DISTINCT salary FROM employees WHERE job_id = 'IT_PROG' ) 
  	AND job_id <> 'IT_PROG';
  ```

- <font size=2 color=blue><b>行子查询：结果集为多行多列</b></font>

  ```sql
  查询员工编号最小并且工资最高的员工信息
  SELECT
  	* 
  FROM
  	employees 
  WHERE
  	(employee_id, salary) = (SELECT MIN( employee_id ), MAX( salary ) FROM employees);
  ```

##### <font color=red>1.4  exists  后面 </font>

```sql
查询有员工的部门名
SELECT
	department_name 
FROM
	departments d 
WHERE
	EXISTS ( SELECT * FROM employees e WHERE e.`department_id` = d.`department_id` );
```

#### 二、分页查询

```sql
SELECT 
  查询列表 
FROM
  表1 别名1
【连接类型】 JOIN 表2 别名2 ON 连接条件 
【WHERE 分组前的筛选】
【GROUP BY 分组字段】 
【HAVING 分组后的筛选 】
【ORDER BY 排序字段 ASC|DESC】
LIMIT 【offset, 】size ;
```

##### 2.1 limit  m;   从第0个查询， 向后推m个

```sql
从第0个查询， 向后推5个
Select * from sys_user limit 5;

返回数据  id 0 ---- 5   的数据
```

##### 2.2 limit m, n;   从第m个查询， 向后推n个

```sql
从第5个查询， 向后推10
Select * from sys_user  limit 5 , 10

返回数据  id 6 ----15   的数据
```

##### 2.3 limit   m  offset  n ;    limit (向后推的m数) offset (从第n条开始)

`offset代表起始索引，起始索引从0开始，size代表条目个数`

```
从第10个开始查询， 向后查询5个
Select * from sys_user  limit 5 offset 10;

返回id 11---15  的数据
```

##### 2.4 分页语句：select 查询列表 from 表 limit (page-1)*size, size;

#### 三、联合查询

```sql
查询语句1
union 【all】
查询语句2
union 【all】
...
```

1. 要查询的结果来自于多个表且多个表没有直接的连接关系，但查询的信息一致时，可以使用联合查询

2. 要求多条查询语句的查询列数是一致的

3. 要求多条查询语句的查询的每一列的类型和顺序最好一致

4. union关键字默认去重，如果使用union all可以包含重复项

```sql
查询中国用户中男性的信息以及外国用户中年男性的用户信息
SELECT id, cname FROM t_ca WHERE csex='男'
UNION ALL
SELECT t_id, tname FROM t_ua WHERE tGender='male';
```

   

