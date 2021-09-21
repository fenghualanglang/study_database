### 2.2.5 set集合基本操作
集合的概念就是一堆string类型且不重复值的无序的组合 说明：对于集合没有修改操作
Redis 中 集合是通过哈希表实现的，所以没有hash碰撞时添加，删除，查找的复杂度都是O(1),有碰撞时为O(n)

<table>
<tr align="left">
		<td>1</td>
		<td>sadd key member1 member2</td>
		<td>向集合添加一个或多个成员</td>
	</tr>
<tr align="left">
		<td>2</td>
		<td>smembers key</td>
		<td>返回集合中的所有成员</td>
	</tr>
<tr align="left">
		<td>3</td>
		<td>srem key member1 member1</td>
		<td>移除集合中一个或多个成员</td>
	</tr>
<tr align="left">
		<td>4</td>
		<td>sismember key member</td>
		<td>判断 member 元素是否是集合 key 的成员</td>
	</tr>
	<tr align="left">
		<td>5</td>
		<td>scard key</td>
		<td>获取集合的成员数</td>
	</tr>
	<tr align="left">
		<td>6</td>
		<td>sdiff key1 key2</td>
		<td>返回给定所有集合的差集</td>
	</tr>
	<tr align="left">
		<td>7</td>
		<td>sinter key1 key2</td>
		<td>返回给定所有集合的交集</td>
	</tr>
	<tr align="left">
		<td>8</td>
		<td>sunion key1 key2</td>
		<td>返回所有给定集合的并集</td>
	</tr>
</table>



## 使用场景: 交集，并集，差集
Set是集合，是String类型的无序集合，set是通过hashtable实现的，概念和数学中个的集合基本类似，可以交集，并集，差集等等，set中的元素是没有顺序的。
<br>
实现方式： set 的内部实现是一个 value永远为null的HashMap，实际就是通过计算hash的方式来快速排重的，这也是set能提供判断一个成员是否在集合内的原因。

一些会不重复的数据,进行交集,并集,差集等灵活操作抖音的关注人,粉丝,可分别放在一个set中<br>
在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。<br>
Redis还为集合提供求交集、并集、差集等操作，可以非常方便的实现如共同关注、共同喜好、二度好友等功能<br>
对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存集到一个新的集合中。<br>


```C++
//book表存储book名称

set book:1:name    ”The Ruby Programming Language”
set book:2:name     ”Ruby on rail”
set book:3:name     ”Programming Erlang”

//tag表使用集合来存储数据，因为集合擅长求交集、并集
sadd tag:ruby 1
sadd tag:ruby 2
sadd tag:web 2
sadd tag:erlang 3

//即属于ruby又属于web的书？
inter_list = redis.sinter("tag.web", "tag:ruby") 
//即属于ruby，但不属于web的书？
inter_list = redis.sdiff("tag.ruby", "tag:web") 
//属于ruby和属于web的书的合集？
inter_list = redis.sunion("tag.ruby", "tag:web")
```

获取某段时间所有数据去重值

这个使用Redis的set数据结构最合适了，只需要不断地将数据往set中扔就行了，set意为集合，所以会自动排重。

### 集合类型（set）
集合类型值具有唯一性，常用操作是向集合添加、删除、判断某个值是否存在，集合内部是使用值为空的散列表实现的。

```c++
添加元素：SADD  语法：SADD key member [member ...] 
	向一个集合添加一个或多个元素，因为集合的唯一性，所以添加相同值时会被忽略。返回成功添加元素的数量。
删除元素：SREM  语法：SREM key member [member ...] 删除集合中一个或多个元素，返回成功删除的个数。
获取全部元素：SMEMBERS  语法：SMEMBERS key ，返回集合全部元素
值是否存在：SISMEMBER  语法：SISMEMBER key member ，如果存在返回1，不存在返回0
差运算：SDIFF  语法：SDIFF key [key ...] 
	例如：集合A和集合B，差集表示A-B，在A里有的元素B里没有，返回差集合；多个集合(A-B)-C
交运算：SINTER  语法：SINTER key [key ...]，返回交集集合，每个集合都有的元素
并运算：SUNION　语法：SUNION key [key ...]，返回并集集合，所有集合的元素
集合元素个数：SCARD  语法：SCARD key ，返回集合元素个数
集合运算后存储结果 语法：SDIFFSTROE destination key key2 ，差运算并存储到destination新集合中
	SINTERSTROE destination key key2，交运算并存储到destination新集合中
	SUNIONSTROE destination key [key ...]，并运算并存储到destination新集合中
随机获取元素：SRANDMEMGER 语法：SRANDMEMBER key [count]
	根据count不同有不同结果，count大于元素总数时返回全部元素
	count>0 ，返回集合中count不重复的元素
	count<0，返回集合中count的绝对值个元素，但元素可能会重复
弹出元素：SPOP  语法：SPOP key [count] ，因为集合是无序的，所以spop会随机弹出一个元素
```























