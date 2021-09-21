# MongoDB的操作

#### 一、mongo 与mysql 术语对比

| MySQL | MongoDB | 解释/说明                            |
| -------------- | ---------------- | ------------------------------------ |
| database       | database         | 数据库                               |
| table          | collections      | 数据库表/集合                        |
| row            | document         | 数据记录行/文档                      |
| column         | field            | 数据字段/域                          |
| index          | index            | 索引                                 |
| table joins    | -                | 表链接， MongoDB不支持               |
| primary key    | primary key      | 主键，MongoDB自动将_id字段设置为主键 |

#### 二、 Mysql与MongoDB操作命令对比 
<img src="../images/mongodb-grammar-01.png" alt="mongodb-01" style="zoom:100%;" />
#### 三、 mongodb 增删改查操作
<img src="../images/mongodb-01.png" alt="mongodb-01" style="zoom:70%;" />
<img src="../images/mongodb-02.png" alt="mongodb-01" style="zoom:70%;" />
<img src="../images/mongodb-03.png" alt="mongodb-03" style="zoom:70%;" />

| 比较运算符号 |          | 比较运算符号 |          |
| ------------ | -------- | ------------ | -------- |
| $lt          | 小于     | $gt          | 大于     |
| $lte         | 小于等于 | $gte         | 大于等于 |
| $ne          | 不等于   |           -   |    -      |
<img src="../images/mongodb-04.png" alt="mongodb-04" style="zoom:70%;" />
<img src="../images/mongodb-05.png" alt="mongodb-05" style="zoom:70%;" />
<img src="../images/mongodb-06.png" alt="mongodb-06" style="zoom:70%;" />
<img src="../images/mongodb-07.png" alt="mongodb-07" style="zoom:70%;" />
<img src="../images/mongodb-08.png" alt="mongodb-08" style="zoom:70%;" />
<img src="../images/mongodb-09.png" alt="mongodb-09" style="zoom:70%;" />
<img src="../images/mongodb-10.png" alt="mongodb-10" style="zoom:70%;" />