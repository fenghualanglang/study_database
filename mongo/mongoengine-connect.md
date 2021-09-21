# 2.2 connect MongoDB

### 一、链接到MongoDB

- 方式一

  ```python
  from mongoengine import connect
  connect('project1', host='192.168.1.35', port=12345)
  ```
  
- 方式二

  ```python
  from mongoengine import connect
  connect('project1', host='192.168.1.35', port=12345)
  ```
  
- 方式三

  ```python
  from mongoengine import connect
  connect('project1', host='mongodb://localhost/database_name')
  ```
  
- 方式四

  ```python
  from mongoengine import connect
  connect('project1', username='webapp', password='pwd123', authentication_source='admin')
  ```
  
- 方式五 URI字符串中的数据库，用户名和密码会覆盖以下参数connect()

  ```python
  from mongoengine import connect
  connect(
      db='test',
      username='user',
      password='12345',
      host='mongodb://admin:qwerty@localhost/production'
  )
  ```

### 二、Replica Sets MongoEngine连接到副本集

```python
from mongoengine import connect

# Regular connect
connect('dbname', replicaset='rs-name')

# MongoDB URI-style connect
connect(host='mongodb://localhost/dbname?replicaSet=rs-name')
```

通过连接或通过传递read_preference的单个查询支持读取首选项

```python
Bar.objects().read_preference(ReadPreference.PRIMARY)
Bar.objects(read_preference=ReadPreference.PRIMARY)
```

### 三、内容管理

有时您可能想要切换数据库或集合以进行查询。例如，出于性能原因将较旧的数据归档到单独的数据库中，或者编写可动态选择要向其写入文档的集合的函数 

- 在不同数据库中定义的文档，通过在元数据中提供db_alias，可以将各个文档附加到不同的数据库 。这使`DBRef` 对象可以指向数据库和集合。下面是一个示例架构，使用3个不同的数据库来存储数据：
  ```python
  connect(alias='user-db-alias', db='user-db')
  connect(alias='book-db-alias', db='book-db')
  connect(alias='users-books-db-alias', db='users-books-db')

  class User(Document):
      name = StringField()
      meta = {'db_alias': 'user-db-alias'}
  
  class Book(Document):
      name = StringField()
      meta = {'db_alias': 'book-db-alias'}

  class AuthorBooks(Document):
      author = ReferenceField(User)
      book = ReferenceField(Book)
      meta = {'db_alias': 'users-books-db-alias'}
  ```

- 断开现有连接 该功能`disconnect()`可用于断开特定连接。这可用于全局更改连接：

  ```python
  from mongoengine import connect, disconnect
  connect('a_db', alias='db1')
  
  class User(Document):
      name = StringField()
      meta = {'db_alias': 'db1'}
  
  disconnect(alias='db1')
  connect('another_db', alias='db1')
  ```
  
- 切换数据库 该switch_db 上下文管理器允许您更改数据库别名给定类，允许快速和方便地访问跨数据库相同的用户文档：

  ```python
  from mongoengine.context_managers import switch_db
    
  class User(Document):
      name = StringField()
      meta = {'db_alias': 'user-db'}
    
  with switch_db(User, 'archive-user-db') as User:
    User(name='Ross').save()  # Saves the 'archive-user-db'
  ```
  
- 开关集合 该switch_collection()上下文管理器允许您更改集合给定类，允许快速和方便地访问整个集合在同一组的文件：

  ```python
  from mongoengine.context_managers import switch_collection
    
  class Group(Document):
     name = StringField()
  
  Group(name='test').save()  # Saves in the default db
with switch_collection(Group, 'group2000') as Group:
    Group(name='hello Group 2000 collection!').save() 
    # Saves in group2000 collection
  ```

