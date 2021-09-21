## mongodb和python交互

#### 一、安装

`pymongo` 提供了mongdb和python交互的所有方法
安装方式: `pip install pymongo`

#### 二、连接及建库-创表-数据的

使用pymongo的第一步首先是连接Client来使用服务，my_db是数据库名称，如果没有会自动创建  

##### 3.1.1 无需权限认证的方式创建连接对象以及集合操作对象
```python
from pymongo import MongoClient
client = MongoClient(host,port)
collection = client[db名][集合名]
# collection = client.my_db 名.集合名 # 与上边用法相同
```
##### 3.1.2 需要权限认证的方式创建连接对象以及集合操作对象
```python
from pymongo import MongoClient

user = 'python' 
password = 'python'
host = '127.0.0.1'
port = 27017
uri = "mongodb://%s:%s@%s" % (user, password, host)
# uri = mongodb://python:python@127.0.0.1
client = MongoClient(uri, port=port)
collection = client.my_db 名.集合名
```

##### 3.1.3查看数据库下所有表名

```python
collection.collection_names() 
```

#### 三、新增数据

##### 3.3.1 insert() 添加一条数据，返回插入数据的_id

```python
ret = collection.insert({"name":"zhangsan","age":33})
```

##### 3.3.2 insert() 添加多条数据，返回ObjectId对象构成的列表

```python
item_list = [{"name":"张{}疯".format(i)} for i in range(10)]
rets = collection.insert(item_list)
```

##### 3.3.3 insert_one() 插入单个数据

```python
collection.insert_one({"key1":"value1","key2","value2"}) 
```

##### 3.3.4  insert_many()插入多个数据

```python
new_document = [{"key1":"value1","key2","value2"}]
result = collection.insert_many(new_document)
```

####  四、删除数据 

##### 3.4.1 delete_one()  删除一条数据

```python
collection.delete_one({"name":"张三疯"})
```

##### 3.4.2 delete_many()  删除全部数据

```python
collection.delete_many({"name":"张三疯"})
collection.delete_many({}) 删除全部
```

#### 五、更新数据

##### 3.5.0 update()更新数据(全文档覆盖或指定键值，更新一条或多条)

- 语法：collection.update({条件}, {'$set':{指定的kv或完整的一条数据}}, multi=False/True, upsert=False/True)
- multi参数：默认为False,表示更新一条; multi=True则更新多条; multi参数必须和$set一起使用
- upsert参数：默认为False; upsert=True则先查询是否存在,存在则更新;不存在就插入
- $set表示指定字段进行更新

##### 3.5.1 更新一条数据；全文档覆盖；存在就更新，不存在就插入

```python
data = {'msg':'这是一条完整的数据1','name':'哈哈'}
collection.update({'haha': 'heihei'}, {'$set':data}, upsert=True)
```

##### 3.5.2 更新多条数据；全文档覆盖；存在就更新，不存在就插入

```python
data = {'msg':'这是一条完整的数据2','name':'哈哈'} # 该完整数据是先查询后获取的
collection.update({}, {'$set':data}, multi=True, upsert=True)
```

##### 3.5.3 更新一条数据；指定键值；存在就更新，不存在就插入

```python
data = {'msg':'指定只更新msg___1'}
collection.update({}, {'$set':data}, upsert=True)
```

##### 3.5.4 更新多条数据；指定键值；存在就更新，不存在就插入

```python
data = {'msg':'指定只更新msg___2'}
collection.update({}, {'$set':data}, multi=True, upsert=True)
```

#### 六、 查询数据 

##### 3.6.1  find_one()查找一条数据

> 接收一个字典形式的条件，返回字典形式的整条数据
> 如果条件为空，则返回第一条

```python
ret = collection.find_one({'name': 'test10001'})
print(ret) # 包含mongodb的ObjectId对象的字典
_ = ret.pop('_id') # 清除mongodb的ObjectId对象的k,v
print(ret) 
```

##### 2.6.2 find()查找全部数据

> 返回所有满足条件的结果，如果条件为空，则返回全部
> 结果是一个Cursor游标对象，是一个可迭代对象，可以类似读文件的指针，但是只能够进行一次读取

```python
rets = collection.find({"name":"test10005"})，
for ret in rets:
    print(ret)
```

##### 3.6.3 获取数据总数

```
print(collection.find().count()) 
```

##### 3.6.4 查询结果排序

```
collection.find().sort("key1") # 默认为升序  
collection.find().sort("key1", pymongo.ASCENDING) # 升序  
collection.find().sort("key1", pymongo.DESCENDING) # 降序  
```

##### pymongo模块其他api

> 查看pymongo官方文档或源代码 http://api.mongodb.com/python/current/

