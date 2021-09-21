### 2.3.1 python操作redis之string字符串
```python
# -*- coding: utf-8 -*-
import redis
#这个redis 连接不能用，请根据自己的需要修改
r =redis.Redis(host="106.13.168.8",port=6379,password="123456")
```
1. <font color="red">set </font>命令用于设置给定 key 的值。<br>如果 key 已经存储其他值， SET 就覆写旧值，且无视类型。
   ```python
   print r.set('123','123') # 插入成功后返回True
   print r.set('123','[123,dffd]') # 插入成功后返回True
   ```
2. <font color="red">get </font>命令用于获取指定 key 的值。<br>如果 key 不存在，返回 None 。<br>如果key 储存的值不是字符串类型，返回一个错误。

  ```python
  print r.get('123')  #返回的结果是[123,dffd]
  print r.get('1234')#返回的结果是 None
  ```
3. <font color="red">getrange</font> 命令用于获取存储在指定 key 中字符串的子字符串。字符串的截取范围由 start 和 end 两个偏移量决定(包括 start 和 end 在内)。
  ```python
  print r.set('getrange','wo shi hao ren ')
  print r.getrange('getrange',2,4)   #返回的结果是sh
  print r.getrange('getrange',2,6)    #返回的结果是shi
  print r.getrange('getrange',2,10)    #返回的结果是shi hao
  ```
4. <font color="red">getset </font>命令用于设置指定 key 的值，并返回 key 旧的值<br>当 key 存在但不是字符串类型时，返回一个错误。
  ```python
  print r.getset('getrange','hello word') #返回的结果是wo shi hao ren
  print r.getset('getrange11','hello word') #当键不存在的时候，返回的结果是None
  ```
5. <font color="red">getbit</font> 命令用于对 key 所储存的字符串值，获取指定偏移量上的位(bit)字符串值指定偏移量上的位(bit)。<br>当偏移量 getrange 比字符串值的长度大，或者 key 不存在时，返回 0。

  ```python
  print r.get('getrange')  #返回的结果是    hello word
  print r.getbit('getrange',1) #返回的结果是    1
  print r.getbit('getrange',3) #返回的结果是    0
  print r.getbit('getr22222ange',3) #字符串的键不存在的时候返回的结果是    0
  print r.getbit('getrange',300) #偏移量超过字符串长度，返回的结果是  0
  ```
6. <font color="red">mget </font>命令返回所有(一个或多个)给定 key 的值。 <br>如果给定的 key 里面，有某个 key 不存在，那么这个 key 返回特殊值 None
  ```python
  print r.set('1','1')   #返回的结果是    True
  print r.set('11','11')  #返回的结果是   True
  print r.set('111','111')    #返回的结果是  True
  print r.mget('1','11','111')    #返回的结果是    ['1', '11', '111']
  print r.mget('1','11','222222')    #因为键222222不存在，返回的结果是   ['1', '11', None]
  ```
7. <font color="red">setbit</font> 命令用于对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。
  ```python
  print r.get('getrange')  #返回的结果是    hello word
  print r.setbit('getrange',4,10086) #返回的结果是    1
  print r.getbit('getrange',10086)  #返回的结果是    0
  print r.setbit('getrange',5,100861) #返回的结果是    0
  print r.getbit('getrange',100861)  #返回的结果是    0
  ```

8. <font color="red">setex </font>命令为指定的 key 设置值及其过期时间。如果 key 已经存在， setex 命令将会替换旧的值。
  ```python
  print r.setex('1','123',10) #返回的结果是    True
  import time
  time.sleep(5)
  print r.ttl('1')    #返回的结果是    4
  print r.get('1')    #返回的结果是    123
  time.sleep(6)
  print r.get('1')    #返回的结果是    None
  ```
9. <font color="red">setnx（SET if Not eXists）</font> 命令在指定的 key 不存在时，为 key 设置指定的值。设置成功，返回 1 。 设置失败，返回 0 。
  ```python
  print r.exists('22')     #返回的结果是    False
  print r.setnx('22','2222222')    #返回的结果是    True
  print r.setnx('22','123456')     #返回的结果是    False
  print r.get('22')    #返回的结果是    2222222
  ```
10. <font color="red">setrange</font> 命令用指定的字符串覆盖给定 key 所储存的字符串值，覆盖的位置从偏移量 offset 开始。
  ```python
  print r.set('11','hello world') #返回的结果是    True
  print r.setrange('11',6,"redis")    #返回的结果是    11
  print r.get('11')   #返回的结果是    hello redis
  ```
11. <font color="red">strlen</font> 命令用于获取指定 key 所储存的字符串值的长度。<br>当 key 储存的不是字符串值时，返回一个错误。
  ```python
  print r.get('11')   #返回的结果是    hello redis
  print r.strlen('11') #返回的结果是   11
  print r.strlen('121') # 当key不存在的时候，返回的结果是   0
  ```
12. <font color="red">mset </font>命令用于同时设置一个或多个 key-value 对。<br>
    ```python
第一种方法
    print r.mset(name1="1", name2='2')  #返回的结果是   True
    ```
    ```python
第二种方法
	print r.mset({"name3":'3', "name4":'4'})     #返回的结果是   True
	print r.get('name1')     #返回的结果是   1
	print r.get('name2')     #返回的结果是   2
	print r.get('name3')     #返回的结果是   3
	print r.get('name4')     #返回的结果是   4
	```
13. <font color="red">metnx</font>令用于所有给定 key 都不存在时，同时设置一个或多个 key-value 对。<br>
     当所有 key 都成功设置，返回 1 。<br> 如果所有给定 key 都设置失败(至少有一个 key 已经存在)，那么返回 0 。
  ```python
	print r.msetnx(name5="5", name6='6')     #返回的结果是  True
	print r.msetnx(name5="55", name7='7')    #返回的结果是   False

	print r.get('name5')     #返回的结果是   5
	print r.get('name6')     #返回的结果是   6
	print r.get('name7')     #返回的结果是  None
  ```
14. <font color="red">psetex 命令以毫秒为单位设置 key 的生存时间。注意：SETEX 命令那样，以秒为单位。
  ```python
  print r.psetex('name8',1000,"nihao")    #返回的结果是   True
  print r.pttl('name8')   #返回的结果是  690
  print r.get('name8')    #返回的结果是   nihao   时间过期后返回的是  None
  ```
15. <font color="red">Incr </font>命令将 key 中储存的数字值增一<br>
    如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCR 操作。<br>
  如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。<br>
  本操作的值限制在 64 位(bit)有符号数字表示之内。
  ```python
  print r.set('11',20)     #返回的结果是  True
  print r.incr('11')   #返回的结果是  21
  print r.get('11')   #返回的结果是  21
  print r.incr('11',amount=2)  #返回的结果是  23
  print r.get('11')   #返回的结果是  23
  print r.incr('21',amount=2)  #返回的结果是  2
  print r.incr('22')  #返回的结果是  1
  ```
16. <font color="red">Redis Incrby</font> 命令将 key 中储存的数字加上指定的增量值。<br>
    如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCRBY 命令。<br>
  如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。

  ```python
  print r.set("13",13)    #返回的结果是  True
  print r.incrby('13',12) #返回的结果是  25
  print r.incrby('15',12) #返回的结果是  12
  print r.incrby('15',12) #返回的结果是  24
  ```
17. <font color="red">Redis Incrbyfloat</font> 命令为 key 中所储存的值加上指定的浮点数增量值。<br>
    如果 key 不存在，那么 INCRBYFLOAT 会先将 key 的值设为 0 ，再执行加法操作。
  ```python
    print r.set('1',1.1)     #返回的结果是  True
    print r.incrbyfloat('1',2.32)    #返回的结果是  3.42
    print r.get('1')     #返回的结果是  3.42
    print r.incrbyfloat('1',amount=1.2) #返回的结果是  4.62
    ```
18. <font color="red">Redis Decr </font>命令将 key 中储存的数字值减一。<br>
    如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 DECR 操作。<br>
  如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。<br>
  本操作的值限制在 64 位(bit)有符号数字表示之内。
  ```python
    print r.decr('22')    #如果 key  22 不存在，返回的结果是  -1
    print r.set('bb',20)    #返回的结果是 True
    print r.decr('bb',amount=2)  #返回的结果是  18
    print r.decr('cc')   #返回的结果是  -1
    ```
19. <font color="red">Redis Append</font> 命令用于为指定的 key 追加值。<br>
    如果 key 已经存在并且是一个字符串， APPEND 命令将 value 追加到 key 原来的值的末尾。<br>
  如果 key 不存在， APPEND 就简单地将给定 key 设为 value ，就像执行 SET key value 一样。
  ```python
    print r.set("11",12)    #返回的结果是 True
    print r.append('11',12)      #返回的结果是 4
    print r.append('11',1)   #返回的结果是 5
    print r.append('11',2)   #返回的结果是 6
    print r.get('11')    #返回的结果是 121212
      
    print r.exists('14')    #返回的结果是 False
    print r.append('14','hello world')  #返回的结果是 11
    print r.get('14')   #返回的结果是 hello world
  ```
  <p>转载:[blackball9](https://www.cnblogs.com/blackball9/p/11970358.html)</p>


























