#### 1.3.3 peewee之insert

peewee连接数据库根据场景目前总结常用的集中情况情况，可根据使用的场景自行选择连接方式。

- <font color=red><b>model</b></font>

  ```python
  
  from peewee import *
  
  database = MySQLDatabase('leqiaosu', **{'charset': 'utf8', 'sql_mode': 'PIPES_AS_CONCAT', 'use_unicode': True, 'host': 'localhost', 'port': 3306, 'user': 'root', 'password': '1qaz2wsx'})
  
  class UnknownField(object):
      def __init__(self, *_, **__): pass
  
  class BaseModel(Model):
      class Meta:
          database = database
  
  class User(BaseModel):
      username = CharField(verbose_name='姓名', max_length=10, null=False, index=True)
      email = CharField(verbose_name='邮件', max_length=50, null=True, unique=True)
      gender = IntegerField(verbose_name='姓别', null=False, default=1)
      score = IntegerField(constraints=[SQL("DEFAULT 0")])
      birthday = DateField(verbose_name='生日', null=True, default=None)
      is_admin = BooleanField(verbose_name='是否是管理员', default=True)
      gmt_created = DateTimeField(constraints=[SQL("DEFAULT CURRENT_TIMESTAMP")])
      gmt_modified = DateTimeField(constraints=[SQL("DEFAULT CURRENT_TIMESTAMP")])
      
      class Meta:
  		table_name = 'user'  # 这里可以自定义表名
  ```

<b>一、<font color=red>执行原生SQL</font></b>

-  cursor()方法 

  ```python
  user = User()
  cursor = user.cursor()
  cursor.execute('SELECT `id`, `uname`, `gender` FROM `user` WHERE `id`=1')
  for (id, uname, gender) in cursor:
  	print('%d:%s:%s' % (id, username, gender))
  ```

-  executemany() 

  ```python
  user = User()
  cursor = user.cursor()
  data_source = [
  	("zhangsan", 65, 2),
  	("lisi", 71, 1)
  ]
  cursor.executemany('insert into `user`(`username`, `score`, `gender`) values (%s, %s, %s) ', data_source) # 返回插入条数
  user.commit() 
  cursor.close()
  ```

- 执行原生SQL

  ```python
  temp = User.raw('select * from user').dicts()
  query = User.raw('SELECT * FROM my_table WHERE data = %s', user_data)
  query = User.select().where(SQL('Some SQL expression %s' % user_data))
  ```

<b>二、<font color=red>单条增加</font></b>

- create  返回值插入id
  ```python
  User.create(username='python', score=99, gender=1)
  ```
-  insert 返回值插入id

  ```python
   User.insert(username='lisi', score=97, gender=1).execute()
   等同于insert into User (user_name, score, gender) values ('lisi', 97, 1)
  ```
- 构造一个key: value 的字典，返回值插入id
  ```python
  data = {'username': "zhangsan", "score": 98, "gender": 1}
  User.create(**data)
  ```
-  save  调用save()方法之后，数据会被插入到数据库中，返回此操作影响的数据数量 

  ```
  user = User(username='wanger', score=95, gender=2)
  user .save()
  ```

   **如果要更新数据，直接修改实例属性值即可：** 

  ```python
  user.score = 99
  user.save()
  ```

<b>三、<font color=red>多条增加</font></b>

 - **for循环和`Model.create()`**（最慢） 

   ```python
   data_source = [   
       {'username': "zhaoda", "score": 59, "gender": 1},   
       {'username': "qianer", "score": 67, "gender": 2},    
       {'username': "sunsan", "score": 458, "gender": 0}
   ]
   
   for data_dict in data_source:
       User.create(**data_dict)
   ```

-  **for循环和`Model.create()`，并放入`事务`中**（较快） 

  ```python
  data_source = [   
      {'username': "zhouqi", "score": 59, "gender": 1},   
      {'username': "wuba", "score": 67, "gender": 2},    
      {'username': "fengjiu", "score": 89, "gender": 0}
  ]
  with database.atomic():
      for data_dict in data_source:
          User.create(**data_dict)
  ```

-   **使用原生的`insert_many()`，并放入`事务`中**（最快）  

  ```python
  insert_many(rows, fields=None)
  
  rows：元组或字典列表，要插入的数据
  fields(list)：需要插入的字段名（可传可不传，如传列表rows 中的字段在字典中必须存在
  ```

  ```python
  data_source = [   
      {'username': "chen", "score": 59, "gender": 1},   
      {'username': "chu", "score": 87, "gender": 2},
      {'username': "wei", "score": 39, "gender": 0}
  ]
  # User.insert_many(data_source).execute()
  with database.atomic():
      User.insert_many(data_source).execute()
  ```

   **如果数据量太大或许你需要分开处理，比如一次处理100条：** 

  ```python
  data_source = [
  	{'username': "chen", "score": 59, "gender": 1},
  	{'username': "chu", "score": 87, "gender": 2},
  	{'username': "wei", "score": 39, "gender": 0}
  ]
  
  with database.atomic():
  	for idx in range(0,len(data_source),100):
          User.insert_many(data_source[idx:idx+100]).execute()
  ```
  
  **Peewee分块辅助函数 `chunked()`，使用它可以有效地将通用迭代块分块为一系列批量迭代的迭代：**
  
  ```python
  from peewee import chunked
  
  with db.atomic():
      for batch in chunked(data, 100):  # 一次插入 100 行.
          User.insert_many(batch).execute()
  ```

-  **`insert_from` 使用 SELECT 查询作为源 INSERT 数据** 

  ```python
  insert_from(query, fields) 
      
  query：SELECT查询用作数据源
  fields：要将数据插入的字段，此参数必须要的 **
  ```
  我们将 user表按原结构复制一个 user2表出来，以做演示 
  ```python
  data = User.select(User.username, User.score, User.birthday)
  User2.insert_from(data, ['username', 'score', 'birthday']).execute()
  ```
  ```python
  insert_from 是指从一个表查数据快速插入到另一个表 query = TweetArchive.insert_from(
        Tweet.select(Tweet.user, Tweet.message),fields=[Tweet.user, Tweet.message]).execute()
  ```
  API 应用于 INSERT INTO … SELECT FROM … 形式的查询
  因为是 INSERT INTO … SELECT FROM … 形式的，所以数据源的列跟要插入的列必须保持一致 
