### 2.3.7 python操作redis之HyperLogLog
```python
#coding:utf8
import redis

# python 操作redis之——HyperLogLog
r =redis.Redis(host="106.13.168.8",port=6379,password="123456")
```



1. <font color="red">Pfadd</font> 命令将所有元素参数添加到 HyperLogLog 数据结构中。

  ```python
  print r.pfadd("1","1","2")  #输出结果是1
  print r.pfadd("1","3","4")  #输出结果是1
  print r.pfadd("1","4","5")  #输出结果是1
  ```


2. <font color="red">Pfcount</font> 命令返回给定 HyperLogLog 的基数估算值。

  ```python
  print r.pfcount("1")        #输出结果是5, 因为 4 重复了两次
  print r.pfcount("2")        #输出结果是0
  ```

  


