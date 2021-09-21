### 2.2.1 String字符串基本操作

字符串类型是 Redis 最基础的数据结构，首先键都是字符串类型，而且 其他几种数据结构都是在字符串类型基础上构建的，
我们常使用的 set key value 命令就是字符串。

常用在缓存、计数、共享Session、限速等。

<table border="1" align="center" bordercolor="#00BFFF" cellpadding="0" cellspacing="0">
    	<caption><strong>常用操作命令如下</strong></caption>
    <tr>
        <th>序号</th>
        <th>命令</th>
        <th>描述</th>
    </tr>
    <tr>
        <td>1</td>
        <td>set key value</td>
        <td>设置指定 key 的值</td>
    </tr>
    <tr>
        <td>2</td>
        <td>get key</td>
        <td>获取指定 key 的值</td>
    </tr>
    <tr>
        <td>3</td>
        <td>mset key value key2 value2</td>
        <td>同时设置一个或多个 key-value 对。</td>
    </tr>
    <tr>
        <td>4</td>
        <td>mget key1 key2</td>
        <td>获取所有(一个或多个)给定 key 的值</td>
    </tr>
    <tr>
        <td>5</td>
        <td>setnx key value</td>
        <td>只有在 key 不存在时设置 key 的值</td>
    </tr>
    <tr>
        <td>6</td>
        <td>setex key seconds value</td>
        <td>将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)</td>
    </tr>
    <tr>
        <td>7</td>
        <td>strlen key</td>
        <td>返回 key 所储存的字符串值的长度</td>
    </tr>
    <tr>
        <td>8</td>
        <td>incr key</td>
        <td>将 key 中储存的数字值增一</td>
    </tr>
    <tr>
        <td>9</td>
        <td>decr key</td>
        <td>将 key 中储存的数字值减一</td>
    </tr>
    <tr>
        <td>10</td>
        <td>getrange key start end</td>
        <td>返回 key 中字符串值的子字符</td>
    </tr>
    <tr>
        <td>11</td>
        <td>getset key value</td>
        <td>将给定 key 的值设为 value ，并返回 key 的旧值(old value)</td>
    </tr>
</table>

<p><strong>1.1 常用操作命令</strong></p>
- SET 命令用于设置给定 key 的值。如果 key 已经存储其他值， SET 就覆写旧值，且无视类型。

    设置键值 `set key value`

    ```c++
    set username zhangsan
    ```

- GET 根据键获取值，如果不存在此键则返回nil   `get key`

    ```c++
    get username
    ```


- MSET 设置多个键值 `mset key1 value1 key2 value2 ...`

    ```c++
    mset zhangsan 18 lisi 19 wangwu 20
    ```

    追加 `append key value`
    
    ```c++
    append maliu 21
    ```
    
    和set 本质无区别，由单个设为多个而已
    
- MGET 根据多个键获取多个值  `mget key1 key2 ...`

    ```c++
    mget zhangsan lisi wangwu
    ```

- SETEX 设置键值及过期时间，以秒为单位 `setex key seconds value`

   ```c++
   setex token 3 JZJOViZOdHcczGDEXMeu8lZe
   ```

   注 `setex zhangsan 18 18 lisi 19 19 wangwu 20 20` 没有此用法

- INCR 将key中储存的数字增加一  `incr key `

   ```c++
   set read  2000
   incr read    # 此时输出2001
   ```
   注: 阅读量点赞量评论量

- DECR 将key中储存的数字减一  `decr key `

   ```c++
   set read  2000
   decr read    # 此时输出1999
   ```
    注: 一些资源的消耗
   
- SETNX 只有在 key 不存在时设置 key 的值   `setnx key value `

    ```c++
    setnx name zhangsan
    ```
    注: 已有key值情况下，不会覆盖更新，也不会创建新的

- STRLEN  返回 key 所储存的字符串值的长度 `strlen key  ` 

   ```c++
   strlen name # 输出为8   zhangsan
   ```

```python
基本的命令：get、set     语法：get key，set key value   value如果有空格需要双引号以示区分
整数递增：incr          语法：incr key    默认值为0，所以首先执行命令得到 1 ，不是整型提示错误
增加指定的整数：incrby   语法：incrby key increment
整数递减：decr          语法：decr key   默认值为0，所以首先执行命令得到 -1，不是整型提示错误
减少指定的整数：decrby   语法：decrby key increment
增加指定浮点数：incrbyfloat 语法：incrbyfloat key increment 
向尾部追加值：append 语法：append key value redis客户端并不是输出追加后的字符串，而是输出字符串总长度
获取字符串长度：strlen语法：strlen key  如果键不存在返回0，注意如果有中文时，redis是使用utf-8编码中文
获取多个键值：mget 语法：mget key key2 例如：mget key1 key2 
设置多个键值：mset 语法：mset key value key2 value2  例如：mset key1 1 key2 "hello redis"
二进制指定位置值：getbit  语法：getbit key offset   
      例如：getbit key1 2 ，key1为hello 返回 1，返回的值只有0或1，当key不存在或超出实际长度时为0
设置二进制位置值：setbit  语法：setbit key offset value ，返回该位置的旧值
二进制是1的个数：bitcount 语法：bitcount key [start end] ，start 、end为开始和结束字节
位运算：bitop   语法：bitop operation destkey key key2，operation支持and、or、xor、not
偏移：bitpos    语法：bitpos key bit [start] [end] 
```

