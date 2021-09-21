## 第九章 数据备份与恢复

#### 9.1、数据导出

- 命令行下具体用法如下：

  ```sql
  mysqldump -u用戶名 -p密码 -d 数据库名 表名 > 文件保存路径;
  ```

- 导出整个数据库结构和数据

  ```sql
  mysqldump -h localhost -uroot -p123456 database > dump.sql
  ```

- 导出单个数据表结构和数据

  ```sql
  mysqldump -h localhost -uroot -p123456  database table > dump.sql
  ```

- 导出整个数据库结构（不包含数据）

  ```sql
  mysqldump -h localhost -uroot -p123456  -d database > dump.sql
  ```

- 导出单个数据表结构（不包含数据）

  ```sql
  mysqldump -h localhost -uroot -p123456  -d database table > dump.sql
  ```

-  导出查询结果 

  ```shell
  mysqldump -u用户名 -p -c --default-character-set=字符集 数据库名 数据表 --where="查询条件" > 保存路径和文件名
  ```

  导入查询结果：mysql -u用户名 -p  -c  --default-character-set=字符集 数据库名 < 保存路径和文件名



### 9.2、数据导入

- 导入某张数据表

  ```sql
  格式：mysql -u账户 -p密码
       use 数据库名;
       source 文件保存路径
  ```

  ```sql
  mysql -uroot -p123456
  use test;
  source D:/emp.sql
  ```

- 导入整个数据库

  ```sql
  格式：mysql -u账户 -p密码 < 文件保存路径
  mysql -uroot -p123456 < test.sql
  ```

