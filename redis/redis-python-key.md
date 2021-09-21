### 2.3.2 python操作redis之key键
```python
# -*- coding: utf-8 -*-
import redis
#这个redis 连接不能用，请根据自己的需要修改
r =redis.Redis(host="106.13.168.8",port=6379,password="123456")
```

1. <font color="red">delete del</font>命令用于删除已存在的键。不存在的 key 会被忽略

   ```python
   # set 设置指定 key 的值,如果设置正确返回 True
   print r.set('1', '4028b2883d3f5a8b013d57228d760a93') 

   print r.get('1') # 得到 键为1 的值    4028b2883d3f5a8b013d57228d760a93
   print r.delete('1') # 删除 键为1 的值
   print r.get('1') # 因为上面已经把键为1 的值删除掉，所以返回的是None
   ```

2. <font color="red">exists</font>

   ```python
   # 设定键为2的值是  4028b2883d3f5a8b013d57228d760a93
   r.set('2', '4028b2883d3f5a8b013d57228d760a93')
       
   # 存在就返回True 不存在就返回False
   print r.exists('2')  #返回True
   print r.exists('33')  #返回False
   ```

3. <font color="red">Expire</font> 命令用于设置 key 的过期时间。key 过期后将不再可用。

   ```python
	r.set('2', '4028b2883d3f5a8b013d57228d760a93')
	    
	#成功就返回True 失败就返回False，下面的20表示是20秒
	print r.expire('2',20)
	    
	#如果时间没事失效我们能得到键为2的值，否者是None
	print r.get('2')
	```

4. <font color="red">Expireat</font>命令用于以 UNIX 时间戳(unix timestamp)格式设置 key 的过期时间。key 过期后将不再可用。注意:时间精确到秒，时间戳是10为数字

   ```python
	r.set('2', '4028b2883d3f5a8b013d57228d760a93')
	
	#成功就返回True 失败就返回False，下面的1598033936表示是在2020-08-22 02:18:56 键2 过期
	print r.expireat('2',1598033936)
	print r.get('2')
	```
	
5. <font color="red">PEXPIREAT</font> 命令用于设置 key 的过期时间，已毫秒技。key 过期后将不再可用。注意:时间精确到毫秒，时间戳是13位数字

   ```python
	r.set('2', '4028b2883d3f5a8b013d57228d760a93')
	    
	#成功就返回True 失败就返回False。
	print r.expireat('2',1598033936000)
	print r.get('2')
	```
	
6. <font color="red">Keys</font> 命令用于查找所有符合给定模式 <font color="red">pattern</font> 的 <font color="red">key</font>

   ```python
   print r.set('111', '11')
   print r.set('122', '12')
   print r.set('113', '13')
   print r.keys(pattern='11*')
   
   # 输出的结果是 ['113', '111'] 因为键122不和 11* 匹配
   ```

7. <font color="red">MOVE </font>命令用于将当前数据库的 key 移动到给定的数据库 db 当中,select可以设定当前的数据库，如有需要请看select命令因为我们默认使用的数据库是db0，我们可以使用下面的命令键 2 移动到数据库 1 中去

   ```python
   # 将select 0 key 为 2 转存到 select 1
   r.move(2,1)
   ```
8. <font color="red"> PERSIST</font> 命令用于移除给定 key 的过期时间，使得 key 永不过期

   ```python
   #设定键为 1 的值为11
   print r.set('1', '11')
   
   #设定键 1 过期时间为100秒
   print r.expire(1,100)
   
   # 查看键 1 的过期时间还剩下多少
   print r.ttl('1')
   
   # 目的是13秒后移除键 1 的过期时间
   import time
   time.sleep(3)
   
   # 查看键 1 的过期时间还剩下多少
   print r.ttl('1')
   
   #移除键 1 的过期时间
   r.persist(1)
   
   # 查看键 1 的过期时间还剩下多少  输出的结果是 None
   # 我们可以通过redis desktop manager 查看键 1 的过期时间
   print r.ttl('1')
   ```

9. <font color="red">Pttl</font> 命令以毫秒为单位返回 key 的剩余过期时间。 

   ```python
	# 当 key 不存在时，返回 -2 。 
	# 当 key 存在但没有设置剩余生存时间时，返回 -1 。 
	# 否则，以毫秒为单位，返回 key 的剩余生存时间。
	
	#设定键为 1 的值为11
	print r.set('1', '11')
	
	#设定键 1 过期时间为100秒
	print r.expire(1,100)
	import time
	time.sleep(3)
	
	# 返回的结果是 96994 ，运行的结果不是固定的，大于是97秒钟，
	# 注意是为了展示出返回的结果是毫秒，一秒等于1000毫秒
	print r.pttl('1')
	```
	
10. <font color="red">TTL</font> 命令以秒为单位返回 key 的剩余过期时间。 

    ```python
    # 当 key 不存在时，返回 -2 。 
    # 当 key 存在但没有设置剩余生存时间时，返回 -1 。 
    # 否则，以毫秒为单位，返回 key 的剩余生存时间。
    
    # 设定键为 1 的值为11
    print r.set('1', '11')
    
    # 设定键 1 过期时间为100秒
    print r.expire(1,100)   
    import time
    time.sleep(3)
    
    print r.ttl('1') #返回的结果是 97
    print r.ttl('123') #因为键 123 不存在  所以返回的结果是None
    ```

11. <font color="red">RANDOMKEY</font> 命令从当前数据库中随机返回一个 key。当数据库不为空时，返回一个 key 。 当数据库为空时，返回 nil 。

    ```python
    print r.randomkey() # 数据库返回的是默认的数据库 key
    ```

12. <font color="red">Rename</font> 命令用于修改 key 的名称 。改名成功时提示 OK ，失败时候返回一个错误。

    ```python
    print r.rename(1,1111) #修改成功返回 True
    #如果key 不存在 修改失败返回 redis.exceptions.ResponseError: no such key
    print r.rename(222,1111)
    ```

13. <font color="red">Renamenx </font>命令用于在新的 key 不存在时修改 key 的名称 。

    ```python
    print r.exists(123123) #返回false
    print r.renamenx(1111,123123) #成功返回True
    #失败返回    redis.exceptions.ResponseError: no such key
    print r.renamenx(1111,123123)
    ```

14. <font color="red">Type </font>命令用于返回 key 所储存的值的类型

    ```python
    # 返回 key 的数据类型，数据类型有：none (key不存在)，string (字符串)，list (列表)，set (集合)，zset (有序集)，hash (哈希表)，
    
    print r.set('1',"111111111")
    print r.type('1') #返回的结果是string
    
    print r.sadd('2','222222222222')
    print r.type('2') #返回的结果是set
    
    print r.lpush('3','33333333')
    print r.type('3') #返回的结果是list
    ```

    

<table border="1" cellspacing="0" cellpadding="0">
    <tr>
        <td valign="top">
            <p>命令</p>
        </td>
        <td valign="top">
            <p>描述</p>
        </td>
    </tr>
    <tr>
        <td valign="top" width="160px">
            <p><a href="http://www.redis.net.cn/order/3528.html">redis del 命令</a></p>
        </td>
        <td valign="top">
            <p>该命令用于在 key 存在是删除 key。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3529.html">redis dump 命令</a></p>
        </td>
        <td valign="top">
            <p>序列化给定 key ，并返回被序列化的值。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3530.html">redis exists 命令</a></p>
        </td>
        <td valign="top">
            <p>检查给定 key 是否存在。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3531.html">redis expire 命令</a></p>
        </td>
        <td valign="top">
            <p>seconds 为给定 key 设置过期时间。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3532.html">redis expireat 命令</a></p>
        </td>
        <td valign="top">
            <p>expireat 的作用和 expire 类似，都用于为 key 设置过期时间。不同在于 expireat 命令接受的时间参数是 unix 时间戳(unix timestamp)。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3533.html">redis pexpireat 命令</a></p>
        </td>
        <td valign="top">
            <p>设置 key 的过期时间亿以毫秒计。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3534.html">redis pexpireat 命令</a></p>
        </td>
        <td valign="top">
            <p>设置 key 过期时间的时间戳(unix timestamp) 以毫秒计</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3535.html">redis keys 命令</a></p>
        </td>
        <td valign="top">
            <p>查找所有符合给定模式( pattern)的 key 。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3536.html">redis move 命令</a></p>
        </td>
        <td valign="top">
            <p>将当前数据库的 key 移动到给定的数据库 db 当中。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3537.html">redis persist 命令</a></p>
        </td>
        <td valign="top">
            <p>移除 key 的过期时间，key 将持久保持。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3538.html">redis pttl 命令</a></p>
        </td>
        <td valign="top">
            <p>以毫秒为单位返回 key 的剩余的过期时间。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3539.html">redis ttl 命令</a></p>
        </td>
        <td valign="top">
            <p>以秒为单位，返回给定 key 的剩余生存时间(ttl, time to live)。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3540.html">redis randomkey 命令</a></p>
        </td>
        <td valign="top">
            <p>从当前数据库中随机返回一个 key 。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3541.html">redis rename 命令</a></p>
        </td>
        <td valign="top">
            <p>修改 key 的名称</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3542.html">redis renamenx 命令</a></p>
        </td>
        <td valign="top">
            <p>仅当 newkey 不存在时，将 key 改名为 newkey 。</p>
        </td>
    </tr>
    <tr>
        <td valign="top">
            <p><a href="http://www.redis.net.cn/order/3543.html">redis type 命令</a></p>
        </td>
        <td valign="top">
            <p>返回 key 所储存的值的类型。</p>
            <p>&nbsp;</p>
        </td>
    </tr>
</table>

转载 [blackball9](https://www.cnblogs.com/blackball9/) 
[原文链接](https://www.cnblogs.com/blackball9/p/11970355.html )

