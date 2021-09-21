#### 1.3.1 MySQL之pymysql

1. 安装pymysql

   ```python
   pip install pymysql
   ```

2.  创建连接对象 

   ```python
   conn = pymysql.connect(
       * 参数host：连接的mysql主机，如果本机是'localhost'
       * 参数port：连接的mysql主机的端口，默认是3306
       * 参数user：连接的用户名
       * 参数password：连接的密码
       * 参数database or db：数据库的名称
       * 参数charset：通信采用的编码方式，推荐使用utf8
       * 参数connect_timeout: 连接时引发异常之前的超时(default: 10, min: 1, max: 31536000)
       * 参数cursorclass 使用的自定义游标类
   )
   ```

    **连接对象操作说明:** 

   ```python
   *  关闭连接 conn.close()
   *  提交数据 conn.commit()
   *  回滚数据 conn.rollback()
   ```

3.  获取游标对象 

   获取游标对象的目标就是要执行sql语句，完成对数据库的增、删、改、查操作。代码如下:

   ```py
    # 调用连接对象的cursor()方法获取游标对象   
    cur =conn.cursor()
   ```

   **游标操作说明:**

   - 使用游标执行SQL语句: execute(operation [parameters ]) 执行SQL语句，返回受影响的行数

     主要用于执行insert、update、delete、select等语句

   <b>fetchone()、fetchmany()、fetchall() 、区别与联系:</b>

   - 如果select本身取的时候有多条数据时：

     cur.fetchone()：将只取最上面的第一条结果，返回单个元组如('id','title')，然后多次使用cur.fetchone()，依次取得下一条结果，直到为空。

     cur.fetchmany(size)：将只取最上面的size条结果，返回单个元组如('id','title')，然后多次使用cur.fetchmany()，依次取得下size条结果，直到为空。

     cur.fetchall() :将返回所有结果，返回二维元组，如(('id','title'),('id','title')),

   - 如果select本身取的时候只有一条数据时：

     cur.fetchone()：将只返回一条结果，返回单个元组如('id','title')。

     cur.fetchmany(size)：也将返回所有结果，返回二维元组，如(('id','title'),)

     cur.fetchall() :也将返回所有结果，返回二维元组，如(('id','title'),),

   - 如果select 没有结果 ：

     cur.fetchone() ：没有结果 则返回 None

     cur.fetchmany(size) ：没有结果 则返回 ()

     cur.fetchall() ：没有结果 则返回 ()

   - 关闭游标: cur.close(),表示和数据库操作完成

4. 简单示例

   ```python
   import pymysql
   from pymysql.cursors import DictCursor
   conn = pymysql.connect(
       host='localhost',
       port=3306,
       user='root',
       password='1qaz2wsx',
       database='leqiaosu',
       charset='utf8',
       cursorclass=DictCursor
   )
   cursor = conn.cursor()
   sql = "select * from user;"
   row_count = cursor.execute(sql)
   print("SQL执行影响的行数%d" % row_count)
   
   # 取出结果集中一行数据
   print(cursor.fetchone())
   
   # 取出结果集中指定的行数
   print(cursor.fetchmany(3))
   
   # 取出结果集中的所有数据
   print(cursor.fetchall())
   
   # 关闭游标
   cursor.close()
   # 关闭连接
   conn.close()
   ```