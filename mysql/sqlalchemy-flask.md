### 1.4.6 sqlalchemy之insert



一、 安装Flask-SQLAlchemy 

```shell
pip install flask-sqlalchemy
```

二、数据库连接设置

- config.py

  ```python
  from flask import Flask
  
  app = Flask(__name__)
  
  class Config(object):
      SQLALCHEMY_DATABASE_URI = 'mysql://root:mysql@127.0.0.1:3306/leqiaosu'
      SQLALCHEMY_TRACK_MODIFICATIONS = False # 在Flask中是否追踪数据修改
      SQLALCHEMY_ECHO = True  # 显示生成的SQL语句，可用于调试
  
  app.config.from_object(Config)
  ```
- 其他配置

| 名字                      | 备注                                                         |
| :------------------------ | :----------------------------------------------------------- |
| SQLALCHEMY_DATABASE_URI   | 用于连接的数据库 URI 。例如:sqlite:////tmp/test.dbmysql://username:password@server/db |
| SQLALCHEMY_BINDS          | 一个映射 binds 到连接 URI 的字典。更多 binds 的信息见[*用 Binds 操作多个数据库*](http://docs.jinkan.org/docs/flask-sqlalchemy/binds.html#binds)。 |
| SQLALCHEMY_ECHO           | 如果设置为Ture， SQLAlchemy 会记录所有 发给 stderr 的语句，这对调试有用。(打印sql语句) |
| SQLALCHEMY_RECORD_QUERIES | 可以用于显式地禁用或启用查询记录。查询记录 在调试或测试模式自动启用。更多信息见get_debug_queries()。 |
| SQLALCHEMY_NATIVE_UNICODE | 可以用于显式禁用原生 unicode 支持。当使用 不合适的指定无编码的数据库默认值时，这对于 一些数据库适配器是必须的（比如 Ubuntu 上 某些版本的 PostgreSQL ）。 |
| SQLALCHEMY_POOL_SIZE      | 数据库连接池的大小。默认是引擎默认值（通常 是 5 ）           |
| SQLALCHEMY_POOL_TIMEOUT   | 设定连接池的连接超时时间。默认是 10 。                       |
| SQLALCHEMY_POOL_RECYCLE   | 多少秒后自动回收连接。这对 MySQL 是必要的， 它默认移除闲置多于 8 小时的连接。注意如果 使用了 MySQL ， Flask-SQLALchemy 自动设定 这个值为 2 小时。 |
三、链接数据库

- 方式一：

  ```python
    db = SQLAlchemy(app)
  ```

- 方式二：

  ```python
    db = SQLAlchemy()
    db.init_app(app)
  ```

