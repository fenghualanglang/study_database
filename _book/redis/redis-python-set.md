### 2.3.5 python操作redis之set集合
```python
-*- coding: utf-8 -*-
import redis
r =redis.Redis(host="106.13.168.8",port=6379,password="123456")
```


1. <font color="red">Sadd </font>命令将一个或多个成员元素加入到集合中，已经存在于集合的成员元素将被忽略。<br>
   假如集合 key 不存在，则创建一个只包含添加的元素作成员的集合。<br>当集合 key 不是集合类型时，返回一个错误。

   ```python
   print r.sadd("1",1)     #输出的结果是1
   print r.sadd("1",2)     #输出的结果是1
   print r.sadd("1",2)     #因为2已经存在，不能再次田间，所以输出的结果是0
   print r.sadd("1",3,4)   #输出的结果是2
   print r.sinter("1")     #输出的结果是set(['1', '3', '2', '4'])
   ```

2. <font color="red">Scard </font>命令返回集合中元素的数量。集合的数量。<br>当集合 key 不存在时，返回 0 。

   ```python
   print r.sadd("2",1)         #输出的结果是1
   print r.sadd("2",2,3,4,5)   #输出的结果是4
   print r.scard("2")          #输出的结果是5
   ```

3. <font color="red">Sdiff </font>命令返回给定集合之间的差集。<br>不存在的集合 key 将视为空集。

   ```python
   print r.sadd("31",1,2,3,4,5,6)      #输出的结果是6
   print r.sadd("32",4,5,6,7,8,9)      #输出的结果是6
   print r.sdiff(31,32)            #输出的结果是set(['1', '3', '2']) 在31不在32内元素
   print r.sdiff(32,31)            #输出的结果是set(['9', '8', '7']) 在32不在31内元素
   print r.sdiff(31,31)            #输出的结果是set([])
   ```

4. <font color="red">Sdiffstore </font>命令将给定集合之间的差集存储在指定的集合中。<br>如果指定的集合 key 已存在，则会被覆盖。

   ```python
   print r.sadd("41",1,2,3,4,5,6)      #输出的结果是6
   print r.sadd("42",4,5,6,7,8,9)      #输出的结果是6
   print r.sadd("43",0)                #输出的结果是1
   print r.sdiffstore("43","41","42")  #输出的结果是3 覆盖原来的43的元素
   print r.sinter("43")                 #输出的结果是 set(['1', '3', '2'])
   ```

5. <font color="red">Sinter</font> 命令返回给定所有给定集合的交集。 <br>不存在的集合 key 被视为空集。 <br>当给定集合当中有一个空集时，结果也为空集(根据集合运算定律)。

   ```python
   print r.sadd("51",3,4,5,6)      #输出的结果是4
   print r.sadd("52",1,2,3,4)      #输出的结果是4
   print r.sinter(51,52)           #输出的结果是set(['3', '4'])
   print r.sadd("53",1,2,3,4,5,6)  #输出的结果是6
   print r.sadd("54",3,4,5,6,7,8,9)#输出的结果是7
   print r.sinter(53,54)           #输出的结果是set(['3', '5', '4', '6'])
   print r.sinter(53,56)           #输出的结果是set([])
   ```

6. <font color="red">Sinterstore </font>命令将给定集合之间的交集存储在指定的集合中。<br>如果指定的集合已经存在，则将其覆盖。

   ```python
   print r.sadd("61",3,4,5,6)      #输出的结果是4
   print r.sadd("62",1,2,3,4)      #输出的结果是4
   print r.sadd("63",0)            #输出的结果是1
   print r.sinterstore(63,61,62)   #输出的结果是2
   print r.sinter(63)              #输出的结果是set(['3', '4'])
   ```

7. <font color="red">Sismember</font> 命令判断成员元素是否是集合的成员。<br>
   如果成员元素是集合的成员，返回 1 。 <br>如果成员元素不是集合的成员，或 key 不存在，返回 0 。
   ```python
   print r.sadd("71",1,2,3,4,5,6)   #输出的结果是6
   print r.sismember("71",1)        #输出的结果是True
   print r.sismember("71",2)        #输出的结果是True
   print r.sismember("71",7)        #输出的结果是False
   print r.sismember("71",8)        #输出的结果是False
   ```

8. <font color="red">Smembers</font> 命令返回集合中的所有的成员。<br> 不存在的集合 key 被视为空集合。

   ```python
   print r.sadd("81",1,2,3,4,5,6)   #输出的结果是6
   print r.smembers(81)             #输出的结果是set(['1', '3', '2', '5', '4', '6'])
   print r.smembers(82)             #输出的结果是set([])
   ```

9. <font color="red">Smove</font> 命令将指定成员 member 元素从 source 集合移动到 destination 集合。<br>
   SMOVE 是原子性操作。<br>如果 source 集合不存在或不包含指定的 member 元素，则 SMOVE 命令不执行任何操作，仅返回 False 。否则， member 元素从 source 集合中被移除，并添加到 destination 集合中去。<br>
   当 destination 集合已经包含 member 元素时， SMOVE 命令只是简单地将 source 集合中的 member 元素删除。<br>
   当 source 或 destination 不是集合类型时，返回一个错误。<br>
   如果成员元素被成功移除，返回 True。 <br>如果成员元素不是 source 集合的成员，并且没有任何操作对 destination 集合执行，那么返回 False<br>
   
   ```python
   print r.sadd("91",1,2,)     #输出的结果是2
   print r.sadd("92",3,4,)     #输出的结果是2
   print r.smove(91,92,1)      #把91中的1移动到92中去，输出的结果是True
   print r.smembers("91")      #输出的结果是set(['2'])
   print r.smembers("92")      #输出的结果是set(['1', '3', '4'])
   print r.smove(91,92,5)      #91不存在5，输出的结果是False
   print r.smembers("91")      #输出的结果是set(['2'])
   print r.smembers("92")      #输出的结果是set(['1', '3', '4'])
   ```
   
10. <font color="red">Spop </font>命令用于移除并返回集合中的一个随机元素。

    ```python
    print r.sadd("10",1,2,3,4,5,6)  #输出的结果是6
    print r.spop("10")              #输出的结果是3
    print r.smembers("10")          #输出的结果是set(['1', '2', '5', '4', '6'])
    print r.spop("10")              #输出的结果是1
    print r.smembers("10")          #输出的结果是set(['2', '5', '4', '6'])
    ```

11. <font color="red">Srandmember</font> 命令用于返回集合中的一个随机元素。<br>
    从 Redis 2.6 版本开始， Srandmember 命令接受可选的 count 参数：<br>
    如果 count 为正数，且小于集合基数，那么命令返回一个包含 count 个元素的数组，数组中的元素各不相同。<br>如果 count 大于等于集合基数，那么返回整个集合。<br>
    如果 count 为负数，那么命令返回一个数组，数组中的元素可能会重复出现多次，而数组的长度为 count 的绝对值。<br>
    该操作和 SPOP 相似，但 SPOP 将随机元素从集合中移除并返回，而 Srandmember 则仅仅返回随机元素，而不对集合进行任何改动。

    ```python
    print r.sadd("11",1,2,3,4,5,6)  #输出的结果是6
    print r.srandmember(11)         #输出的结果是4
    print r.smembers(11)            #输出的结果是set(['1', '3', '2', '5', '4', '6'])
    print r.srandmember(11,3)         #输出的结果是['6', '3', '1']
    print r.smembers(11)            #输出的结果是set(['1', '3', '2', '5', '4', '6'])
    ```

12. <font color="red">Srem</font> 命令用于移除集合中的一个或多个成员元素，不存在的成员元素会被忽略。<br>
    当 key 不是集合类型，返回一个错误。被成功移除的元素的数量，不包括被忽略的元素。
    ```python
    print r.sadd("12",1,2,3,4,5,6,7)    #输出的结果是7
    print r.srem("12",1)                #输出的结果是1
    print r.smembers("12")              #输出的结果是set(['3', '2', '5', '4', '7', '6'])
    print r.srem("12",8)                #输出的结果是0
    print r.smembers("12")              #输出的结果是set(['3', '2', '5', '4', '7', '6'])
    ```

13. <font color="red">Sunion</font> 命令返回给定集合的并集。不存在的集合 key 被视为空集。

    ```python
    print r.sadd("131",1,2,3,4,5,6,7)    #输出的结果是7
    print r.sadd("132",0,1,2,7,8,9)      #输出的结果是6
    print r.sunion(131,132)             #输出的结果是set(['1', '0', '3', '2', '5', '4', '7', '6', '9', '8'])
    print r.sunion(131,134)             #输出的结果是set(['1', '3', '2', '5', '4', '7', '6'])
    ```

14. <font color="red">Sunionstore</font> 命令将给定集合的并集存储在指定的集合 destination 中。

    ```python
    print r.sadd("141",1,2,3,4,5,6,7)    #输出的结果是7
    print r.sadd("142",0,1,2,3,4)        #输出的结果是5
    print r.sunionstore(143,141,142)     #输出的结果是8
    print r.smembers(143)                #输出的结果是set(['1', '0', '3', '2', '5', '4', '7', '6'])
    ```

15. <font color="red">Sscan</font> 命令用于迭代集合键中的元素。

    ```python
    print r.sadd("151",1,2,3,4,5,6,7)           #输出的结果是7
    print r.sscan(151,cursor=2,match=1,count=1) #输出的结果是 (0L, ['1'])
    ```

<p>附录：</p>
<p></p>
<table class="table table-bordered table-striped" style="height: 373px; width: 1048px;">
<tr><th class="eye-protector-processed">命令</th><th class="eye-protector-processed">描述</th></tr>
<tr>
<td><a href="http://www.redis.net.cn/order/3594.html">redis sadd 命令</a></td>
<td>向集合添加一个或多个成员</td>
</tr>
<tr>
<td class="eye-protector-processed"><a href="http://www.redis.net.cn/order/3595.html">redis scard 命令</a></td>
<td class="eye-protector-processed">获取集合的成员数</td>
</tr>
<tr>
<td><a href="http://www.redis.net.cn/order/3596.html">redis sdiff 命令</a></td>
<td>返回给定所有集合的差集</td>
</tr>
<tr>
<td class="eye-protector-processed"><a href="http://www.redis.net.cn/order/3597.html">redis sdiffstore 命令</a></td>
<td class="eye-protector-processed">返回给定所有集合的差集并存储在 destination 中</td>
</tr>
<tr>
<td><a href="http://www.redis.net.cn/order/3598.html">redis sinter 命令</a></td>
<td>返回给定所有集合的交集</td>
</tr>
<tr>
<td class="eye-protector-processed"><a href="http://www.redis.net.cn/order/3599.html">redis sinterstore 命令</a></td>
<td class="eye-protector-processed">返回给定所有集合的交集并存储在 destination 中</td>
</tr>
<tr>
<td><a href="http://www.redis.net.cn/order/3600.html">redis sismember 命令</a></td>
<td>判断 member 元素是否是集合 key 的成员</td>
</tr>
<tr>
<td class="eye-protector-processed"><a href="http://www.redis.net.cn/order/3601.html">redis smembers 命令</a></td>
<td class="eye-protector-processed">返回集合中的所有成员</td>
</tr>
<tr>
<td><a href="http://www.redis.net.cn/order/3602.html">redis smove 命令</a></td>
<td>将 member 元素从 source 集合移动到 destination 集合</td>
</tr>
<tr>
<td class="eye-protector-processed"><a href="http://www.redis.net.cn/order/3603.html">redis spop 命令</a></td>
<td class="eye-protector-processed">移除并返回集合中的一个随机元素</td>
</tr>
<tr>
<td><a href="http://www.redis.net.cn/order/3604.html">redis srandmember 命令</a></td>
<td>返回集合中一个或多个随机数</td>
</tr>
<tr>
<td class="eye-protector-processed"><a href="http://www.redis.net.cn/order/3605.html">redis srem 命令</a></td>
<td class="eye-protector-processed">移除集合中一个或多个成员</td>
</tr>
<tr>
<td><a href="http://www.redis.net.cn/order/3606.html">redis sunion 命令</a></td>
<td>返回所有给定集合的并集</td>
</tr>
<tr>
<td class="eye-protector-processed"><a href="http://www.redis.net.cn/order/3607.html">redis sunionstore 命令</a></td>
<td class="eye-protector-processed">所有给定集合的并集存储在 destination 集合中</td>
</tr>
<tr>
<td><a href="http://www.redis.net.cn/order/3608.html">redis sscan 命令</a></td>
<td>迭代集合中的元素</td>
</tr>
</table>
