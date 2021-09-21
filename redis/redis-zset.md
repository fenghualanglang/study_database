### 2.2.6 sorted set有序集合基本操作

redis 有序集合和集合一样也是string类型元素的集合,且不允许重复的成员。<br>
不同的是每个元素都会关联一个double类型的分数。<br>redis正是通过分数来为集合中的成员进行从小到大的排序。<br>
有序集合的成员是唯一的,但分数(score)却可以重复,当分数重复时,按照自然顺序排序。<br>

明确存储对象用hash<br>
指定存续的存储需顺序zset<br>
需要添加循序指定 list <br>


<table border="1" align="left" bordercolor="#00BFFF" cellpadding="0" cellspacing="0">
    	<caption>
    		<strong>sorted set常用操作命令如下</strong>
    	</caption>
    <tr>
        <th>序号</th>
        <th>命令</th>
        <th>描述</th>
    </tr>
    <tr>
        <td>1</td>
        <td>zadd key score1 member1 score2 member2</td>
        <td>向有序集合添加一个或多个成员，或者更新已存在成员的分数</td>
    </tr>
    <tr>
        <td>2</td>
        <td>zrange key start stop</td>
        <td>返回指定范围内的元素<br>
        	start、stop为元素的下标索引<br>
        	索引从左侧开始，第⼀个元素为0，索引可以是负数<br>
        	表示从尾部开始计数，如-1表示最后⼀个元素
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>zcard key</td>
        <td>获取有序集合的成员数</td>
    </tr>
    <tr>
        <td>4</td>
        <td>zscore key member</td>
        <td>返回有序集中，成员的分数值</td>
    </tr>
    <tr>
        <td>5</td>
        <td>zcount key min max</td>
        <td>计算在有序集合中指定区间权重的成员数</td>
    </tr>
    <tr>
        <td>6</td>
        <td>zrangebyscore key min max [withscores] [limit]</td>
        <td>通过分数返回有序集合指定区间内的成员</td>
    </tr>
    <tr>
        <td>7</td>
        <td>zrank key member</td>
        <td>返回有序集合中指定成员的索引</td>
    </tr>
    <tr>
        <td>8</td>
        <td>zrem key member member2</td>
        <td>移除有序集合中的一个或多个成员</td>
    </tr>
    <tr>
        <td>9</td>
        <td>zincrby key increment member</td>
        <td>有序集合中对指定成员的分数加上增量 increment</td>
    </tr>
    <tr>
        <td>10</td>
        <td>zinterstore destination numkeys key key2</td>
        <td>计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中</td>
    </tr>
    <tr>
        <td>11</td>
        <td>zremrangebyscore key min max</td>
        <td>移除有序集合中给定的分数区间的所有成员</td>
    </tr>
    <tr>
        <td>12</td>
        <td>zrevrangebyscore key max min [withscores]</td>
        <td>返回有序集中指定分数区间内的成员，分数从高到低排序</td>
    </tr>
    <tr>
        <td>13</td>
        <td>zunionstore destination numkeys key key2</td>
        <td>计算给定的一个或多个有序集的并集，并存储在新的 key 中</td>
    </tr>
</table>



- 添加 zadd key score1 member1 score2 member2

   ```c++
   zadd user 1 python 2 java 4 html  5 go  6 javascript
   ```

- 获取 zrange key start stop
	
```c++
	zrange user 1 -1
	
	1) "python"
	2) "java"
	3) "html"
	4) "go"
	5) "javascript"
	```

- 权重在min max 元素个数 zcount key min max

  ```c++
  zcount user  4 5
  ```

- 获取有序集合的成员数 zcard key

  ```c++
   zcard user
    5
  ```

- 返回有序集中，成员的分数值 zscore key member

  ```
  zscore user java
  	 2
  ```

- 通过分数返回有序集合指定区间内的成员 zrangebyscore key min max

  ```
  zrangebyscore user  3   6
  
  1) "html"
  2) "go"
  3) "javascript"
  ```

- 返回有序集合中指定成员的索引 zrank key member

  ```c++
  zrank user java
    	2
  
  zrank user 0 -1
  	1) "python"
  	2) "java"
  	3) "html"
  	4) "go"
  	5) "javascript"
  ```

  ```c++
   zrank user 0 -1  withscores
  	1) "python"
  	2) "1"
  	3) "java"
  	4) "2"
  	5) "html"
  	6) "4"
  	7) "go"
  	8) "5"
  	9) "javascript"
  	10) "6"
  ```

- 有序集合中对指定成员的分数加上增量 zincrby key increment member

  ```c++
  zincrby user 3 java
  5  返回是权重
  ```

- 移除有序集合中的一个或多个成员 zrem key member 

  ```c++
  zrem user python
  ```

#### 使用场景:

使用场景：Sorted Set增加了一个权重参数score，使得集合中的元素能够按score进行有序排列，插入时自动排序。
当你需要一个有序的并且不重复的集合列表，那么可以选择sorted set数据结构, 比如twitter的public 微信朋友圈排序,分数用时间来表示, timeline可以以发表时间作为score来存储，这样获取时就是自动按时间排好序的。


经典案例:一个班的同学按分数排序,就可以把分数作为scored,实现排序
比如一个存储全班同学成绩的Sorted Set，其集合value可以是同学的学号，而score就可以是其考试得分，这样在数据插入集合的时候，就已经进行了天然的排序。另外还可以用Sorted Set来做带权重的队列，比如普通消息的score为1，重要消息的score为2，然后工作线程可以选择按score的倒序来获取工作任务。让重要的任务优先执行。

Redis sorted set的内部使用HashMap和跳跃表(SkipList)来保证数据的存储和有序，HashMap里放的是成员到score的映射，而跳跃表里存放的是所有的成员，排序依据是HashMap里存的score,使用跳跃表的结构可以获得比较高的查找效率，并且在实现上比较简单。


## sorted 

sorted特点: 有序， 元素string类型， 唯一性，不重复
每个元素都会关联double类型的score， 表示权重，通过权重将元素冲销到大排序， 元素的score可以重复
```
添加集合元素：zadd      添加集合元素：zadd key [nx|xx] [ch] [incr] score member [score member]
不存在添加，存在更新
获取元素分数：zscore   语法：zscore key member  返回元素成员的score 分数
元素小到大：zrange      语法：zrange key start top [withscores]
加上withscores 返回带元素，即元素，分数， 当分数一样时，按元素排序
元素大到小：zrevrange   语法：zrevrange key start [withscores]
指定分数范围元素：zrangebyscore   语法：zrangebyscore key min max [withscore] [limit offest count]
返回从小到大的在min和max之间的元素, 符号表示不包含，例如：80-100，(80 100，withscore返回带分数
limit offest count 向左偏移offest个元素，并获取前count个元素
指定分数范围元素：zrevrangescore   语法：zrevrangebyscore key max  min [withscore] [limit offest count]
增加分数：zincrby      语法：zincrby key increment member 注意是增加分数，返回增加后的分数
如果成员不存在，则添加一个为0的成员
```
