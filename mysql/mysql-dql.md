### DQL语言   其一

#### 一、基础查询

- ```
  - SELECT 查询列表 FROM 表名; 
  - 查询列表可以是字段、常量、函数、表达式
  - 查询结果是一个虚拟表
  ```

-  <b>查询单个字段 </b>

  ```sql
  SELECT 字段名 FROM 表名; 
  ```

- <b> 查询多个字段 </b>

  ```sql
  SELECT 字段名,字段名 FROM 表名; 
  ```

- <b> 查询所有字段  </b>

  ```sql
  SELECT * FROM 表名; 
  ```

- <b> 查询常量  </b>

  ```sql
  SELECT 常量值;
  
  注意：字符型和日期型的常量值必须用单引号引起来，数值型不需要
  ```

- <b> 查询函数 </b>

  ```sql
  SELECT 函数名(实参列表); 
  ```

- <b> 查询表达式  </b>

  ```sql
   SELECT 100/25; 
  ```

- <b> 起别名  </b>

  ```sql
  SELECT 字段名 AS "别名" FROM 表名;
  
  注意：别名可以使用单引号、双引号引起
  来当只有一个单词时，可以省略
  引号当有多个单词且有空格或特殊符号时，不
  能省略AS可以省略 
  ```

- <b> 去重复  </b>

  ```sql
  SELECT DISTINCT 字段名 FROM 表名; 
  ```

- <b> 做加法  </b>

  ```sql
  SELECT 数值+数值; 直接运算
  SELECT 字符+数值; 首先先将字符转换为整数，如果转换成功，则继续运算，如果转换失败，则默认为0，然后继续运算
  SELECT NULL+数值; NULL和任何数值参与运算结果都是NULL
  ```

- <b>【补充】ifnull函数  </b>

  ```sql
  SELECT IFNULL(字段名, 指定值) FROM 表名; 判
  
  断某字段或表达式是否为null，如果为null，返回指定的值，否则返回原本的值 
  ```

- <b>【补充】isnull函数  </b>

  ```sql
  SELECT ISNULL(字段名) FROM 表名;判
  
  断某字段或表达式是否为null，如果是null，则返回1，否则返回0 
  ```

#### 二、条件查询

-  <b>条件运算符：>   >=    <    <=    =   <=>   !=   <> </b>

  ```sql
  查询工资>12000的员工信息
  SELECT FROM employees WHERE salary > 12000 ;
  ```

-  <b>逻辑运算符：and、or、not </b>

  ```mysql
  查询工资>12000 and 工资<18000的员工信息 
  SELECT FROM employees WHERE salary > 12000 AND salary < 18000 ;
  
  查询工资<=12000||工资>=18000的员工信息
  SELECT FROM employees WHERE salary > 12000 OR salary < 18000 ;
  
  查询工资<=12000||工资>=18000的员工信息
  SELECT * FROM employees WHERE NOT (salary > 12000 AND salary < 18000) ;
  ```

- <b>like：%任意多个字符、_任意单个字符，如果有特殊字符，需要使用escape转义</b>

  ```sql
  查询员工名中第一个字符为B、第四个字符为d的员工信息 
  SELECT  * FROM employees WHERE last_name LIKE 'B__d%' ;
  ```

- <b> between and </b>

  ```sql
  查询员工编号在100到120之间的员工信息 
  SELECT FROM employees WHERE employee_id BETWEEN 100 AND 120 ;
  ```

- <b> NOT BETWEEN 100 AND   120 </b>

  ```sql
  查询员工编号不在100到120之间的员工信息
  SELECT FROM employees WHERE employee_id NOT BETWEEN 100 AND 120 ;
  ```

- <b>IN  </b>

  ```sql
  查询员工的工种编号是 IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号 
  SELECT last_name,  job_id FROM employees WHERE job_id IN ('IT_PROT', 'AD_VP', 'AD_PRES') ;
  
  in列表的值类型必须一致或兼容，in列表中不支持通配符%和_
  ```

- <b> IS NULL  </b>

  ```sql
  查询没有奖金的员工名和奖金率
  SELECT last_name, commission_pct FROM employees WHERE commission_pct IS NULL ;
  ```

- <b> IS NOT NULL </b>

  ```sql
  SELECT  last_name,  commission_pct FROM  employees WHERE commission_pct IS NOT NULL ;
  
  =、!=不能用来判断NULL
  <=>、is null 、is not null可以用来判断NULL
  ```

### 三、排序查询

- ```sql
  SELECT  查询列表 FROM  表 【WHERE 筛选条件】ORDER BY 排序列表 【asc | desc】 ;
  
  排序列表可以是单个字段、多个字段、别名、函数、表达式
  asc代表升序，desc代表降序，如果不写，默认是asc
  order by的位置一般放在查询语句的最后（除limit语句之外）
  ```

- <b> ORDER BY ... DESC  </b>

  ```sql
  按单个字段排序：查询员工信息，要求按工资降序
  SELECT FROM employees ORDER BY salary DESC ;
  
  按多个字段查询：查询员工信息，要求先按工资降序，再按员工编号升序
  SELECT * FROM  employees ORDER BY salary DESC, employee_id ASC ;
  
  按别名排序查询：查询员工信息，要求按员工年薪升序
  SELECT  *,  salary * 12 * (1+ IFNULL(commission_pct, 0)) 年薪 FROM  employees ORDER BY 年薪 ASC ;
  
  按函数排序查询：查询员工信息，要求按员工名字的长度降序
  SELECT  LENGTH(last_name),  last_name FROM  employees ORDER BY LENGTH(last_name) DESC ;
  
  按表达式排序：查询员工信息，要求按员工年薪升序
  SELECT  *, salary * 12 * (1+ IFNULL(commission_pct, 0)) FROM  employees ORDER BY salary * 12 * (1+ IFNULL(commission_pct, 0)) ASC ;
  ```

### 四、字符串函数

- ```sql
  SELECT 函数名(实参列表) 【FROM 表】;
  ```

- <b>concat  连接字符 </b> 

  ```sql
  SELECT CONCAT('Hello',' ','World') AS out_put; 
  ```

- <b>substr 截取子串 </b> 

  ```sql
  截取从指定索引处后面所有字符
  SELECT SUBSTR('李莫愁爱上了陆展元',7)  AS out_put;
  
  截取从指定索引处指定字符长度的字符
  SELECT SUBSTR('李莫愁爱上了陆展元',1,3) AS out_put;
  ```

- <b>replace  替换字符</b>

  ```sql
  SELECT REPLACE('张无忌爱上了周芷若','周芷若','赵敏') AS out_put;
  ```

- <b>upper 变大写 </b> 

  ```sql
  SELECT UPPER('john') AS out_put;
  ```

- <b>lower  变小写 </b>

  ```sql
  SELECT LOWER('john') AS out_put;
  ```

- <b>lpad  左填充 </b>

  ```sql
  SELECT LPAD('殷素素',10,'*') AS out_put;
  ```

-  <b>rpad 右填充 </b> 

  ```sql
  SELECT LPAD('殷素素',10,'*') AS out_put;
  ```

- <b>length 获取字节长度 </b> 

  ```sql
  SELECT LENGTH('john') AS out_put;
  ```

- <b>trim 去除前后空格</b> 

  ```sql
  删除指定字符的左右空格
  SELECT LENGTH(TRIM('    张翠山    ')) AS out_put;
  
  删除指定字符的指定字符
  SELECT TRIM('aaa' FROM 'aaaaaaaaa张翠山aaaaaaaaa')  AS out_put;
  ```

- <b>instr 获取子串第一次出现的索引 </b>

  ```sql
  SELECT INSTR('杨不悔爱上了殷六侠','殷六侠') AS out_put;
  
  返回子串第一次出现的索引，如果找不到返回0
  ```

### 五、 数学函数 

-  <b>round  四舍五入</b> 

  ```sql
  默认四舍五入
  SELECT ROUND(-1.55) AS out_put;
  
  指定小数位数
  SELECT ROUND(1.567,2) AS out_put;
  
  四舍五入和符号无关
  ```

- <b>ceil  向上取整 </b> 

  ```sql
  SELECT CEIL(-1.02) AS out_put;
  
  向上取整，返回>=该参数的最小整数
  ```

- <b>floor 向下取整 </b> 

  ```sql
  SELECT FLOOR(-9.99) AS out_put;
  
  向下取整，返回<=该参数的最大整数
  ```

- <b>mod  取模运算（a-a/b*b） </b> 

  ```sql
  SELECT MOD(10,3) AS out_put
  ```

- <b>truncate  保留小数的位数，不进行四舍五入 </b> 

  ```sql
  SELECT TRUNCATE(1.69999,1) AS out_pu
  ```

- <b>rand  获取随机数，返回0-1之间的小数 </b> 

  ```sql
  SELECT RAND() AS out_put
  ```

### 六、 日期函数 

- <b>now  返回当前日期+时间 </b>

  ```sql
  SELECT NOW() AS out_put; 
  ```

-  <b>curdate  返回当前日期 </b> 

  ```sql
  SELECT CURDATE() AS out_put; 
  ```

- <b>curtime  返回当前时间 </b>

  ```sql
  SELECT CURTIME() AS out_put; 
  ```

-  <b>year 返回年</b>

  ```sql
  SELECT YEAR(NOW()) 年; 
  ```

- <b>month   返回月</b>

  ```sql
  SELECT MONTH(NOW()) 月; 
  ```

-  <b>day    返回日</b>

  ```sql
  SELECT DAY(NOW()) 日; 
  ```

-  <b>hour   小时</b>

  ```sql
  SELECT HOUR(NOW()) 时; 
  ```

-  <b>minute   分钟</b>

  ```sql
  SELECT MINUTE(NOW()) 分; 
  ```

- <b>second   秒 </b>

  ```sql
  SELECT SECOND(NOW()) 秒; 
  ```

- <b>monthname  以英文形式返回月</b> 

  ```sql
  SELECT MONTHNAME(NOW()) 月名; 
  ```

- <b>datediff    返回两个日期相差的天数 </b>

  ```sql
  SELECT DATEDIFF('1995-2-7','1995-2-6') AS out_put; 
  ```

- <b>date_format   将日期转换成字符 </b> 

  ```sql
  SELECT DATE_FORMAT(NOW(),'%Y年%m月%d日') AS out_put; 
  ```

- <b>str_to_date     将字符转换成日期 </b> 

  ```sql
  格式符：
  %Y：四位的年份
  %y：二位的年份
  %m：二位的月份（01，02，...，12）
  %c：一位的月份（1，2，...，12）
  %d：日（01，02，...，31）
  %H：小时（24小时制）
  %h：小时（12小时制）
  %i：分钟（00，01，02，...，59）
  %s：秒（00，01，02，...，59）
  ```

  ```sql
  SELECT STR_TO_DATE('1998-3-2','%Y-%c-%d') AS out_put; 
  ```

### 七、 控制函数 

- <b>if  判断函数 </b> 

  ```sql
  IF(条件表达式，表达式1，表达式2)：
  如果条件表达式成立，返回表达式1，否则返回表达式2
  
  SELECT IF(10 < 5, '大', '小') AS out_put; 
  ```

- <b>case 分支函数 </b>

  <font color=red><b>case的格式一</b></font>

  ```sql
  CASE 变量或字段或表达式 
  	WHEN 常量1 THEN 值1 
  	WHEN 常量2 THEN 值2
  	...
  ELSE 值n 
  END ;
    
  案例：查询员工的工资，要求
  	部门号=30，显示的工资为1.1倍
  	部门号=40，显示的工资为1.2倍
  	部门号=50，显示的工资为1.3倍
  	其它部门，显示的工资为原工资
  	
  SELECT 
    salary 原始工资,
    department_id,
    CASE
      department_id 
      WHEN 30 THEN salary * 1.1 
      WHEN 40 THEN salary * 1.2 
      WHEN 50 THEN salary * 1.3 
      ELSE salary 
    END AS 新工资 
  FROM
    employees ;
  ```

  <font color=red><b>case的格式二</b></font>

  ```sql
  CASE
      WHEN 条件1 THEN 值1 
      WHEN 条件2 THEN 值2
      ...
      ELSE 值n 
  END
  
  案例：查询员工的工资情况
      如果工资>20000，显示A级别
      如果工资>15000，显示B级别
      如果工资>10000，显示C级别
      否则，显示D级别
  
  SELECT 
    salary,
    CASE
      WHEN salary > 20000 THEN 'A' 
      WHEN salary > 15000 THEN 'B' 
      WHEN salary > 10000 THEN 'C' 
      ELSE 'D' 
    END AS 工资级别 
  FROM
    employees ;
  ```

### 八、 其它函数 

- <b>version   当前数据库的版本 </b>

  ```sql
  SELECT VERSION(); 
  ```

- <b>database   当前打开的数据库 </b>

  ```sql
  SELECT DATABASE();
  ```

- <b>user  当前登录的用户 </b> 

  ```sql
  SELECT USER(); 
  ```

- <b>password(‘字符’)  返回该字符的密码形式</b>

  ```sql
  SELECT PASSWORD('123456');
  ```

-  <b>md5(‘字符’)  返回该字符的md5加密形式</b>

  ```sql
  SELECT MD5('123456');
  ```

### 九、分组函数

 - ```sql
   SELECT 函数名(实参列表) 【FROM 表】; 
   
   ```

1、sum、avg一般用于处理数值型，max、min、count可以处理任何类型
   2、以上分组函数都忽略null值
   3、可以和distinct搭配实现去重的运算：select sum(distinct 字段) from 表;
   4、一般使用count(*)用作统计行数
   5、和分组函数一同查询的字段要求是group by后的字段
   ```
   
- <b>sum   求和 </b> 

  ```sql
  SELECT SUM(salary) FROM employees;
   ```

- <b>avg    平均值 </b> 

  ```sql
  SELECT AVG(salary) FROM employees;
  ```

- <b>max  最大值 </b> 

  ```sql
  SELECT MAX(salary) FROM employees;
  ```

- <b>min   最小值 </b> 

  ```sql
  SELECT MIN(salary) FROM employees;
  ```

- <b>count 计算个数 </b>

  ```sql
  SELECT COUNT(salary) FROM employees; 
  ```

### 十、分组查询

- ```sql
  SELECT 
    查询列表 
  FROM
    表 
  【where 筛选条件】 
  GROUP BY 分组的字段 
  【having 分组后的筛选】
  【order BY 排序的字段】 ;
  ```

  ```sql
  1、和分组函数一同查询的字段必须是group by后出现的字段
  2、筛选分为两类：分组前筛选和分组后筛选
              	 针对的表				 语句位置			   连接的关键字
  	分组前筛选	 分组前的原始表			group by前			where
  	分组后筛选	 分组后的结果集			group by后			having
  	
  3、分组可以按单个字段也可以按多个字段
  4、分组可以搭配着排序使用
  ```

-  <b>GROUP BY 分组的字段  </b>

  ```sql
  查询每个工种的员工平均工资 
  SELECT AVG(salary),  job_id FROM  employees GROUP BY job_id ;
  
  查询每个位置的员工部门个数
  SELECT COUNT(*),  location_id FROM  departments GROUP BY location_id ;
  
  查询有奖金的每个领导手下员工的平均工资
  SELECT AVG(salary), manager_id FROM  employees WHERE commission_pct IS NOT NULL GROUP BY manager_id ;
  
  查询哪个部门的员工个数>5
  SELECT  COUNT(*),  department_id FROM  employees GROUP BY department_id HAVING COUNT(*) > 5 ;
  
  查询每个工种有奖金的员工的最高工资>6000的最高工资和公众编号，按最高工资升序
  SELECT  MAX(salary) m,  job_id FROM employees WHERE commission_pct IS NOT NULL 
  GROUP BY job_id HAVING m > 6000 ORDER BY m ;
  
  查询每个工种每个部门的最低工资并按最低工资降序
  SELECT  MIN(salary), job_id,  department_id FROM  employees GROUP BY job_id, department_id ORDER BY MIN(salary) DESC ;
  ```

  





