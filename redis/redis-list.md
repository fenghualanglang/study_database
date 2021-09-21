
### 2.2.4 list列表基本操作
常见的存储结构,它是一个双向链表,即可以作为栈结构也可以作为队列结构.
列表的元素类型为string, 按照插⼊顺序排序，可以在列表头部尾部添加元素

<table border="1" align="left" bordercolor="#00BFFF" cellpadding="0" cellspacing="0">
    	<caption>
    		<strong>常用操作命令如下</strong>
    	</caption>
<tr align="left">
		<td>1</td>
		<td>llen key</td>
		<td>获取列表长度</td>
</tr>
<tr align="left">
		<td>2</td>
		<td>lpush key value1 value2</td>
		<td>将一个或多个值插入到列表头部</td>
</tr>
<tr align="left">
		<td>3</td>
		<td>lpop key</td>
		<td>移出并获取列表的第一个元素</td>
</tr>
<tr align="left">
		<td>4</td>
		<td>rpush key value1 value2</td>
		<td>据将一个或多个值插入到列表尾部</td>
	</tr>
<tr align="left">
		<td>5</td>
		<td>rpop key</td>
		<td>移除并获取列表最后一个元素</td>
	</tr>
<tr align="left">
		<td>6</td>
		<td>linsert key before 现有元素 新元素</td>
		<td>在指定元素的前插⼊新元素</td>
	</tr>
<tr align="left">
		<td>7</td>
		<td>linsert key after 现有元素 新元素</td>
		<td>在指定元素后插⼊新元素</td>
	</tr>
<tr align="left">
		<td>8</td>
		<td>lrange key start stop</td>
		<td>
			获取列表指定范围内的元素 tart、stop为元素的下标索引<br>
			索引从左侧开始，第⼀个元素为0索引可以是负数<br>
			表示从尾部开始计数，如-1表示最后⼀个元素<br>
			列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。
	</td>
	</tr>
<tr align="left">
		<td>9</td>
		<td>lset key index value</td>
		<td>设置指定索引位置的元素值</td>
	</tr>
<tr align="left">
		<td>10</td>
		<td>lrem key count value</td>
		<td>
			将列表中前count次出现的值为value的元素移除<br>
			count>0:从头往尾移除<br>
			count < 0: 从尾往头移除<br>
			count = 0: 移除所有
		</td>
	</tr>
	<tr align="left">
		<td>11</td>
		<td>rpushx key value</td>
		<td>为已存在的列表添加值</td>
	</tr>
</table>

### 使用场景
```python
1. 取出最新n个消息(不涉及复杂排序)
	比如最新1000个访客,就可以使用list,同时使用LTRIM key 0 1000,只保存最新的1000个
2. 作为队列
	将Redis用作日志收集器, 多个端点将日志信息push到Redis,然后一个worker统一将所有日志写到磁盘rpop
3. 取最新N个数据的操作
	记录前N个最新登陆的用户Id列表，超出的范围可以从数据库中获得。
	把当前登录人添加到链表里 ret = r.lpush("login:last_login_times", uid)
	保持链表只有N位  ret = redis.ltrim("login:last_login_times", 0, N-1)
	获得前N个最新登陆的用户Id列表 last_login_list = r.lrange("login:last_login_times", 0, N-1)	 
    比如sina微博：
     在Redis中我们的最新微博ID使用了常驻缓存，这是一直更新的。
     但是我们做了限制不能超过5000个ID，因此我们的获取ID函数会一直询问Redis。
     只有在start/count参数超出了这个范围的时候，才需要去访问数据库。
```

我们的系统不会像传统方式那样“刷新”缓存，Redis实例中的信息永远是一致的。SQL数据库只是在用户需要获取“很远”的数据时才会被触发，而主页或第一个评论页是不会麻烦到硬盘上的数据库了




## 列表类型（list）

```C++
内部使用双向链表实现，所以获取越接近两端的元素速度越快，但通过索引访问时会比较慢
添加左边元素：LPUSH       语法：LPUSH key value [value ...]  ，返回添加后的列表元素的总个数
添加右边元素：RPUSH       语法：RPUSH key value [value ...]  ，返回添加后的列表元素的总个数
移除左边第一个元素：LPOP   语法：LPOP key  ，返回被移除的元素值
移除右边第一个元素：RPOP   语法：RPOP key ，返回被移除的元素值 
列表元素个数：LLEN        语法：LLEN key， 不存在时返回0，redis是直接读取现成的值，并不是统计个数
获取列表片段：LRANGE      语法：LRANGE key start stop，如果start比stop靠后时返回空列表，0 -1 返回整个列表
						   正数时：start 开始索引值，stop结束索引值（索引从0开始）
						   负数时：例如 lrange num -2 -1，-2表示最右边第二个，-1表示最右边第一个，
删除指定值：LREM          语法：LREM key count value，返回被删除的个数
							count>0，从左边开始删除前count个值为value的元素
							count<0，从右边开始删除前|count|个值为value的元素
							count=0，删除所有值为value的元素
索引元素值：LINDEX        语法：LINDEX key index ，返回索引的元素值，-1表示从最右边的第一位
设置元素值：LSET          语法：LSET key index value
保留列表片段：LTRIM       语法：LTRIM key start stop，start、top 参考lrange命令
一个列表转移另一个列表：RPOPLPUSH   语法：RPOPLPUSH source desctination ，从source列表转移到desctination列表，
                                  该命令分两步看，首先source列表RPOP右移除，再desctination列表LPUSHssss
```

