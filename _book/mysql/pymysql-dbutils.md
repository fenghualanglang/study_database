#### 1.3.2 MySQL之 DBUtils 



##### 一、为什么要使用连接池

1.  mysql查询每次查询数据都会使用单独connection，使用一次以后就断开，再次使用再次连接

   但是对于mysql，每次连接的开销都很高，遇到高并发高频率开销更高，所以使用连接池

2. mysql服务设置超时链接，比如8小时，如果一个连接8个小时都没有操作，mysql会主动的断开连接，当这个连接再次尝试查询的时候就会报错，所以使用连接池，保持可用链接

3. 连接池: MySQL中保持着指定数量的可用连接，每次重新获取一个有效的连接进行查询操作

   执行完查询语句以后要将connection关闭，注意这里的关闭并不是真正的关闭，而只是将连接返回给连接池让其它人使用

4. pymysql本身不具有连接池功能，需要借助DBUtils，DBUtils是一套Python数据库连接池包，并允许对非线程安全的数据库接口进行线程安全包装。

##### 二、安装方法及优点

* `pip install DBUtils `
* PooledDB ：提供线程间可共享的数据库连接，并自动管理连接。
* SharedDBConnection创建一批连接，供所有线程共享使用

##### 三、方法示例

-  ```python
  实例化池对象，写入池的配置信息;创建一批连接放入连接池，共享使用
  POOL = PooledDB()
  池连接
  conn = POOL.connection()
  执行SQL语句
  conn.cursor().execute('select * from user')
  获取执行SQL后的返回
  conn.cursor().fetchall()
  未关闭连接，只是将连接放回池子，供所有线程共享使用；当线程终止时，连接自动关闭；
  conn.close()
  ```

-  ```python
	maxconnections: 接池允许的最大连接数,0和None表示没有限制(默认)
	mincache: 初始化时,连接池至少创建的空闲连接,0表示不创建
	maxcached: 连接池中空闲的最多连接数,0和None表示没有限制
	maxshared:连接池中最多共享的连接数量,0和None表示全部共享(其实没什么用)
	blocking: 连接池中如果没有可用共享连接后,是否阻塞等待,True表示等等,False表示不等待然后报错
	setsession: 开始会话前执行的命令列表
	ping: ping Mysql服务器检查服务是否可用
  ```

- ```python
  import pymysql
  from DBUtils.PooledDB import PooledDB
  
  POOL = PooledDB(
    	creator=pymysql,  # 使用链接数据库的模块
    	maxconnections=6,  # 连接池允许的最大连接数，0和None表示不限制连接数
    	mincached=2,  # 初始化时，链接池中至少创建的空闲的链接，0表示不创建
    	maxcached=5,  # 链接池中最多闲置的链接，0和None不限制
    	maxshared=3,  # 链接池中最多共享的链接数量，0和None表示全部共享。
  
    	# PS: 无用，因为pymysql和MySQLdb等模块的 threadsafety都为1，所有值无论设置为多少，
    	# _maxcached永远为0，所以永远是所有链接都共享。
  
    	blocking=True,  # 连接池中如果没有可用连接后，是否阻塞等待。True，等待；False，不等待然后报错
    	maxusage=None,  # 一个链接最多被重复使用的次数，None表示无限制
    	setsession=[],  # 开始会话前执行的命令列表。
    	ping=0, # ping MySQL服务端，检查是否服务可用。
  
    	# 如：0 = None = never,
    	# 1 = default = whenever it is requested,
    	# 2 = when a cursor is created,
    	# 4 = when a query is executed,
    	# 7 = always
  
    	host='127.0.0.1',
    	port=3306,
    	user='root',
    	password='123456',
    	database='test',
  	charset='utf8'
  	)
  	
  def func():
  
    	# 检测当前正在运行连接数的是否小于最大链接数，如果不小于则：等待或报raise TooManyConnections异常
    	# 否则 则优先去初始化时创建的链接中获取链接 SteadyDBConnection。
    	# 然后将SteadyDBConnection对象封装到PooledDedicatedDBConnection中并返回。
    	# 如果最开始创建的链接没有链接，则去创建一个SteadyDBConnection对象，再封装到PooledDedicatedDBConnection中并返回。
  	# 一旦关闭连接，其实并没有真正关闭，只是将连接返回给连接池
  
    	conn = POOL.connection()
    	# print(th, '链接被拿走了', conn1._con)
  	# print(th, '池子里目前有', pool._idle_cache, '\r\n')
  
    	cursor = conn.cursor()
    	cursor.execute('select * from user')
    	result = cursor.fetchall()
    	print(result)
    	conn.close()
  	connection.close()
  
    if __name__ == '__main__':
        
    	func()
  ```
  <b>参考</b>https://blog.csdn.net/weixin_40976261/article/details/89057633
  
