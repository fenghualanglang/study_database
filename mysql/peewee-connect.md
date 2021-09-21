#### 1.3.2 peewee之connect

peewee连接数据库根据场景目前总结常用的集中情况情况，可根据使用的场景自行选择连接方式。

```python
 from peewee import * 
```

- <font color=red><b>方法一</b></font>

  ```python
  database = MySQLDatabase('database', host="", user="",passwd="" ) 
  database.connect() 
  ```
- <font color=red><b>方法二  使用数据库URL连接</b></font>
  
  ```python
  from playhouse.db_url import connect
  database = connect('mysql://user:passwd@ip:port/my_db')
  ```
- <font color=red><b>方法三 运行时数据库配置，这些值可能是从配置文件或环境中加载的</b></font>
  
  ```python
  from peewee import *
  database = MySQLDatabase(None)
  
  class BaseModel(Model):
      class Meta:
          database = database
  
  database.init( 
    database=self.conf['db_base'], 
    host=self.conf['db_host'], 
    port=self.conf.get('db_port', 3306), 
    user=self.conf['db_user'], 
    passwd=self.conf['db_passwd'], 
    charset='utf8'
   ) 
  ```
- <font color=red><b>方法四 链接池（超时后，连接将被回收, 打开的连接数上限)</b></font>
  
  ```python
from playhouse.pool import PooledMySQLDatabase
  
  db = PooledMySQLDatabase(
      'my_database',
      max_connections=8,
      stale_timeout=300,
      user='user', 
    password='password',
      host='127.0.0.0',
      port=3306
  )
  
  class BaseModel(Model):
      class Meta:
          database = db
  ```
- <font color=red><b>方法五 [主从连接](https://pypi.org/project/pwmd/)</b></font>
  
  ```python
  from peewee import Model, CharField, DateField, BooleanField
from pwmd import MultiMySQLDatabase
  
  DATABASE = {
      'master': 'mysql://root@localhost/test_app',
      'slaves': ['mysql://root@localhost/test_app']
  }
  db = MultiMySQLDatabase(DATABASE)
  db.connect()
  
  class BaseModel(Model):
        class Meta:
            database = db
    
    class Person(BaseModel):
        name = CharField()
        birthday = DateField()
        is_relative = BooleanField()
  ```
- <font color=red><b>方法六 运行时动态定义数据库</b></font>
  
  ```python
  database_proxy = DatabaseProxy()  # Create a proxy for our db.
    
    class BaseModel(Model):
        class Meta:
          database = database_proxy  # Use proxy for our DB.
    
    class User(BaseModel):
      username = CharField()
    
    # Based on configuration, use a different database.
    if app.config['DEBUG']:
        database = SqliteDatabase('local.db')
    elif app.config['TESTING']:
        database = SqliteDatabase(':memory:')
    else:
      database = PostgresqlDatabase('mega_production_db')
    
    # Configure our proxy to use the db we specified in config.
    database_proxy.initialize(database)
  ```
    运行数据库
    ```python
    db = SqliteDatabase('my_app.db', pragmas={'journal_mode': 'wal'})
    # Specify the details at run-time:
    db = SqliteDatabase(None)
    db.init(db_filename, pragmas={'journal_mode': 'wal'})
    # Or use a placeholder:
    db = DatabaseProxy()
    db.initialize(SqliteDatabase('my_app.db', pragmas={'journal_mode': 'wal'}))
  ```
  
- <font color=red><b>方法七</b></font>  [数据库重连，连接池连接不够](https://blog.csdn.net/max229max/article/details/90512313)
  
  ```python
  - # *_ coding:utf-8 _*_
    from playhouse.pool import PooledMySQLDatabase
    from playhouse.shortcuts import ReconnectMixin
    import config
  
    class RetryMySQLDatabase(ReconnectMixin, PooledMySQLDatabase):
        _instance = None
  
        @staticmethod
        def get_db_instance():
            if not RetryMySQLDatabase._instance:
                RetryMySQLDatabase._instance = RetryMySQLDatabase(
                        config.get('db_name', 'aaabb'),
                        max_connections=8,
                        stale_timeout=300,
                        host=config.get('db_host', '127.0.0.1'),
                        user=config.get('db_user', 'root'),
                        password=config.get('db_pwd', '123'),
                        port=config.get('db_port', 3306)
                        )
            return RetryMySQLDatabase._instance
    # 如何使用？
    # 在model文件中
    database = RetryMySQLDatabase.get_db_instance()
    为了不用不停的连接断开peewee提供with 操作
    with database.connection_context():
        Person.select()
        ....
  ```
  
- <font color=red><b>方法八</b></font> [ssh访问数据库的实例](https://blog.csdn.net/windy135/article/details/79404924)



db.is_closed()  # 判断数据库是不是链接
db.connect()   # 数据库链接
db.create_tables([Person,]) #建库