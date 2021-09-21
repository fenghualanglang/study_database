#### 1.3.2 MySQL之 aiomysql

##### 一、为什么使用aiomysql

##### 一、为什么要使用连接池

- mysql查询每次查询数据都会使用单独connection，使用一次以后就断开，再次使用再次连接

  但是对于mysql，每次连接的开销都很高，遇到高并发高频率开销更高，所以使用连接池

- mysql服务设置超时链接，比如8小时，如果一个连接8个小时都没有操作，mysql会主动的断开连接，当这个连接再次尝试查询的时候就会报错，所以使用连接池，保持可用链接

- 连接池: MySQL中保持着指定数量的可用连接，当一个查询结执行之前从这个池子里取一个连接，查询结束以后将连接放回池子中，这样可以避免频繁的连接数据库，节省大量的资源。 

-  异步的操作的优势在于它可以”同时”的进行多个操作，如果查询只是一个一个的单独查询，那用不用异步其实都无所谓，这里尝试使用异步来同时执行多个操作 

  

##### 二、aiomysql

-  [aiomysql的文档]( https://aiomysql.readthedocs.io/en/latest/ )
- pip install aiomysql

##### 三、简单示例

-  aiomysql.cursors.DeserializationCursor  
-  aiomysql.cursors.DictCursor  反序列化json类型数据

- ```python
  import asyncio
  import aiomysql
  
  async def test_example(loop):
      conn = await aiomysql.connect(
      	host='127.0.0.1', 
      	port=3306,
      	user='root', 
      	password='', 
      	db='mysql',
      	loop=loop
      )
  
      async with conn.cursor() as cur:
          await cur.execute("SELECT Host,User FROM user")
          print(cur.description)
          r = await cur.fetchall()
          print(r)
      conn.close()
  
  loop = asyncio.get_event_loop()
  loop.run_until_complete(test_example(loop))
  ```

- ```python
  import asyncio
  import aiomysql
  
  async def test_example(loop):
      pool = await aiomysql.create_pool(host='127.0.0.1', port=3306,
                                        user='root', password='',
                                        db='mysql', loop=loop)
      async with pool.acquire() as conn:
          async with conn.cursor() as cur:
              await cur.execute("SELECT 42;")
              print(cur.description)
              (r,) = await cur.fetchone()
              assert r == 42
      pool.close()
      await pool.wait_closed()
  
  loop = asyncio.get_event_loop()
  loop.run_until_complete(test_example(loop))
  ```

  <b>注</b>: 更多示例[参考](https://github.com/aio-libs/aiomysql)

