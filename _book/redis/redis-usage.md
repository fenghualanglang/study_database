

## 2.2 redis操作类型

 Redis是一个开源的Key-Value存储<br>redis层面，永远只有一个键（字符串对象，值的种类有字符串对象，队列对象，集合对象，hash对象）


1. <font color="black" size=4><b>string类型</b></font>
	```python
	增删改查：set get del 
	decrby（减少指定整数）
	incrby（增加指定整数）
	incrbyfloat（增加指定的单精度）
	批量：mset mget 
	自增, 自减：incr decr
	追加：append（返回长度）
	删除所有的key   flushall
	获取getrange
	```
2. <font color="black" size=4><b>hash类型</b></font>
   
	```python
	增删改查：hset hget
	hgetall keys
	hkeys keys
	hvals keys
	hdel keys field
	```
3. <font color="black" size=4><b>list类型</b></font>
	lisy是基于双向链表实现的，用于评论系统，新闻分页列表，消息队列等
	```python
	lpush 左边插入 返回个数
	rpush 右边插入 返回个数
	lpop  移除左边 返回移除的值
	rpop  移除右边
	llen  列表个数
	lrange key start stop 下标 （0 -1全部，0 1 前两个，-2 -1 倒数最后两个）
	```
4. <font color="black" size=4><b>set集合</b></font>
	```python
	增删改查： sadd srem/spop(随机弹出元素) smembers
	个数 scard
	```
5. <font color="black" size=4><b>zset有序集合</b></font>
	```python
	 增删改查： zadd
	```

[经典文档](http://redisdoc.com/)