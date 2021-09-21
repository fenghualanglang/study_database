### 2.2.7 Redis实际应用场景

#### 1. 显示最新的项目列表

下面这个语句常用来显示最新项目，随着数据多了，查询毫无疑问会越来越慢。

```c++
SELECT * FROM foo WHERE ... ORDER BY time DESC LIMIT 10  
```

<p> <font size="2">在Web应用中，"列出最新的回复"之类的查询非常普遍，这通常会带来可扩展性问题。这令人沮丧，因为项目本来就是按这个顺序被创建的，但要输出这个顺序却不得不进行排序操作。<br>比如说，我们的一个Web应用想要列出用户贴出的最新20条评论。在最新的评论边上我们有一个"显示全部"的链接，点击后就可以获得更多的评论。我们假设数据库中的每条评论都有一个唯一的递增的ID字段。我们可以使用分页来制作主页和评论页，使用Redis的模板，每次新评论发表时，我们会将它的ID添加到一个Redis列表：</font></p>

```
LPUSH latest.comments <ID>   
```

我们将列表裁剪为指定长度，因此Redis只需要保存最新的5000条评论：

```
LTRIM latest.comments 0 5000 
```

每次我们需要获取最新评论的项目范围时，我们调用一个函数来完成（使用伪代码）：

```
FUNCTION get_latest_comments(start, num_items):  
   id_list = redis.lrange("latest.comments",start,start+num_items - 1)  
   IF id_list.length < num_items  
       id_list = SQL_DB("SELECT ... ORDER BY time LIMIT ...")  
   END  
   RETURN id_list  
END 
```

<p>这里我们做的很简单。在Redis中我们的最新ID使用了常驻缓存，这是一直更新的。但是我们做了限制不能超过5000个ID，因此我们的获取ID函数会一直询问Redis。只有在start/count参数超出了这个范围的时候，才需要去访问数据库。我们的系统不会像传统方式那样“刷新”缓存，Redis实例中的信息永远是一致的。
    <br>SQL数据库（或是硬盘上的其他类型数据库）只是在用户需要获取“很远”的数据时才会被触发，而主页或第一个评论页是不会麻烦到硬盘上的数据库了。</p>

#### 2.  排行榜应用，取TOP N操作

 这个需求与上面需求的不同之处在于，取最新N个数据的操作以时间为权重，这个是以某个条件为权重<br>比如按顶的次数排序，这时候就需要我们的sorted set出马了，将你要排序的值设置成sorted set的score，将具体的数据设置成相应的value，每次只需要执行一条ZADD命令即可。

热门，排行榜应用：

```
//将登录次数和用户统一存储在一个
sorted set里zadd login:login_times 5 1zadd 
login:login_times 1 2zadd login:login_times 2 3

//当用户登录时，对该用户的登录次数自增
1ret = r.zincrby("login:login_times", 1, uid)

//那么如何获得登录次数最多的用户呢，逆序排列取得排名前N的用户
ret = r.zrevrange("login:login_times", 0, N-1)
```

另一个很普遍的需求是各种数据库的数据并非存储在内存中，因此在按得分排序以及实时更新这些几乎每秒钟都需要更新的功能上数据库的性能不够理想。典型的比如那些在线游戏的排行榜，比如一个Facebook的游戏，根据得分你通常想要：

- 列出前100名高分选手

- 列出某用户当前的全球排名

这些操作对于Redis来说小菜一碟，即使你有几百万个用户，每分钟都会有几百万个新的得分。模式是这样的，每次获得新得分时，我们用这样的代码：

```
ZADD leaderboard  <score>  <username>
```

你可能用userID来取代username，这取决于你是怎么设计的。得到前100名高分用户很简单：

```
ZREVRANGE leaderboard 0 99
```

用户的全球排名也相似，只需要：

```
ZRANK leaderboard <username>
```

#### 3. 删除与过滤

我们可以使用LREM来删除评论。如果删除操作非常少，另一个选择是直接跳过评论条目的入口，报告说该评论已经不存在。 有些时候你想要给不同的列表附加上不同的过滤器。如果过滤器的数量受到限制，你可以简单的为每个不同的过滤器使用不同的Redis列表。毕竟每个列表只有5000条项目，但Redis却能够使用非常少的内存来处理几百万条项目。

#### 4.  按照用户投票和时间排序

排行榜的一种常见变体模式就像Reddit或Hacker News用的那样，新闻按照类似下面的公式根据得分来排序：score = points / time^alpha 因此用户的投票会相应的把新闻挖出来，但时间会按照一定的指数将新闻埋下去。下面是我们的模式，当然算法由你决定。<br>模式是这样的，开始时先观察那些可能是最新的项目，例如首页上的1000条新闻都是候选者，因此我们先忽视掉其他的，这实现起来很简单。每次新的新闻贴上来后，我们将ID添加到列表中，使用LPUSH + LTRIM，确保只取出最新的1000条项目。有一项后台任务获取这个列表，并且持续的计算这1000条新闻中每条新闻的最终得分。计算结果由ZADD命令按照新的顺序填充生成列表，老新闻则被清除。这里的关键思路是排序工作是由后台任务来完成的。

#### 5. 处理过期项目

另一种常用的项目排序是按照时间排序。我们使用unix时间作为得分即可。 模式如下：

- 每次有新项目添加到我们的非Redis数据库时，我们把它加入到排序集合中。这时我们用的是时间属性，current_time和time_to_live。

- 另一项后台任务使用ZRANGE…SCORES查询排序集合，取出最新的10个项目。如果发现unix时间已经过期，则在数据库中删除条目。

#### 6.  计数

Redis是一个很好的计数器，这要感谢INCRBY和其他相似命令。我相信你曾许多次想要给数据库加上新的计数器，用来获取统计或显示新信息，但是最后却由于写入敏感而不得不放弃它们。好了，现在使用Redis就不需要再担心了。有了原子递增（atomic increment），你可以放心的加上各种计数，用GETSET重置，或者是让它们过期。例如这样操作：
```
INCR user:<id> EXPIRE
```

你可以计算出最近用户在页面间停顿不超过60秒的页面浏览量，当计数达到比如20时，就可以显示出某些条幅提示，或是其它你想显示的东西。

#### 7. 特定时间内的特定项目

另一项对于其他数据库很难，但Redis做起来却轻而易举的事就是统计在某段特点时间里有多少特定用户访问了某个特定资源。比如我想要知道某些特定的注册用户或IP地址，他们到底有多少访问了某篇文章。每次我获得一次新的页面浏览时我只需要这样做

```
SADD page:day1:<page_id> <user_id> 
```

当然你可能想用unix时间替换day1，比如time()-(time()%3600*24)等等。 想知道特定用户的数量吗？只需要使用
```
SCARD page:day1:<page_id>
```
### 8. 查找某个值所在的区间(区间无重合) 
Sorted Set-->例如有下面两个范围，10－20和30－40
```
A_start 10, A_end 20
B_start 30, B_end 40
```
我们将这两个范围的起始位置存在Redis的Sorted Sets数据结构中，基本范围起始值作为score，范围名加start和end为其value值：
```
redis 127.0.0.1:6379> zadd ranges 10 A_start
(integer) 1redis 127.0.0.1:6379> zadd ranges 20 A_end
(integer) 1redis 127.0.0.1:6379> zadd ranges 30 B_start
(integer) 1redis 127.0.0.1:6379> zadd ranges 40 B_end
(integer) 1
```
这样数据在插入Sorted Sets后，相当于是将这些起始位置按顺序排列好了。现在我需要查找15这个值在哪一个范围中，只需要进行如下的zrangbyscore查找：
```
redis 127.0.0.1:6379> zrangebyscore ranges (15 +inf LIMIT 0 1
1) "A_end"
```

这个命令的意思是在Sorted Sets中查找大于15的第一个值。（+inf在Redis中表示正无穷大，15前面的括号表示>15而非>=15）查找的结果是A_end，由于所有值是按顺序排列的，所以可以判定15是在A_start到A_end区间上，也就是说15是在A这个范围里。至此大功告成。


#### 9.  交集，并集，差集：(Set)

```
//book表存储book名称
set book:1:name    ”The Ruby Programming Language”
set book:2:name     ”Ruby on rail”
set book:3:name     ”Programming Erlang”

//tag表使用集合来存储数据，因为集合擅长求交集、并集
sadd tag:ruby 1sadd tag:ruby 2sadd tag:web 2sadd tag:erlang 3

//即属于ruby又属于web的书？
inter_list = redis.sinter("tag.web", "tag:ruby") 

//即属于ruby，但不属于web的书？
inter_list = redis.sdiff("tag.ruby", "tag:web") 

//属于ruby和属于web的书的合集？
inter_list = redis.sunion("tag.ruby", "tag:web")
```