### 2.3.8 python操作redis之redis.Redis与redis.StrictRedis区别

redis-py提供两个类<font color=red>Redis</font>和<font color=red>StrictRedis</font>用于实现Redis的命令

- <font color=red>StrictRedis</font>用于实现大部分官方的命令,并使用官方的语法和命令
- <font color=red>Redis</font>是StrictRedis的子类,用于向后兼容旧版本的redis-py。 

官方推荐使用StrictRedis方法。Redis类和redis-cli操作有些不一样, 三个方面。 

- <font color=red>LREM</font>:参数'num'和'value'的顺序交换了一下,cli是 lrem queueName 0 'string' , 0是所有<br>

  但是Redis这个类,把控制和string调换了。

- <font color=red>ZADD</font>:实现时score和value的顺序不小心弄反了,后来有人用了,就这样了

- <font color=red>SETEX</font>:time和value的顺序反了

- Redis的连接池的方法:
  ```python
  pool = redis.ConnectionPool(host='localhost', port=6379, db=0)
  r = redis.Redis(connection_pool=pool)
  ```
  
- StrictRedis的连接池的实现方式:
  ```python
  pool = redis.ConnectionPool(host='127.0.0.1', port=6379)
  r = redis.StrictRedis(connection_pool=pool)
  ```

官方的创建redis的时候,都可以添加什么参数

```python
class redis.StrictRedis(
    host='localhost', 
    port=6379, 
    db=0, 
    password=None, 
    socket_timeout=None, 
    connection_pool=None, 
    charset=‘utf-8’, 
    errors=‘strict’, 
    decode_responses=False, 
    unix_socket_path=None
)
```

Implementation of the Redis protocol.

This abstract class provides a Python interface to all Redis commands and an implementation of the Redis protocol.

Connection and Pipeline derive from this, implementing how the commands are sent and received to the Redis server

redis的对于有些编码入库的问题,redis的连接附加的参数里面,默认编码是utf-8,但是如果你非要用GBK那就需要指明你的chardet和decode_responses为True 。

