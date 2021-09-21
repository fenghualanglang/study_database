### 1.4.2 sqlalchemy之connect


#### 一、常规链接
```python
# 导入相关数据包
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import create_engine, Column, Integer, String, equence

engine = create_engine("mysql+pymysql://root:1qaz2wsx@127.0.0.1:3306/testdb?charset=utf8", echo=True)  
# 创建到数据库的连接,echo=True表示用logging输出调试结果
Base = declarative_base()

class User(Base):
    __tablename__ = 'user'
    id = Column(Integer, Sequence('user_id_seq'), primary_key=True)
    name = Column(String(50))
    fullname = Column(String(50))
    password = Column(String(12))
    def __repr__(self):
        return "<User(name='%s', fullname='%s', password='%s')>" % (self.name, self.fullname, self.password)

"""创建引擎中的所有表。这相当于“CREATE TABLE”原始的SQL语句。"""
Base.metadata.create_all(engine)

# 实例化一个对象，并赋值
ed_user = User(name='ed', fullname='Ed Jones', password='edspassword')

# 创建会话链接，并关联引擎
Session = sessionmaker(bind=engine, expire_on_commit=False)

# 创建一个session实例
session = Session()

# 将数据添加到seesion中
```
echo 参数是用来设置SQLAlchemy日志的
create_engine() 的返回值是Engine的一个实例，通过方言来处理数据库和数据库的API

数据库事务的提交,sisson自动过期而不需要关闭 

#### 二、连接池

```python
from sqlalchemy.orm import sessionmaker, relationship
# 函数是最常使用的创建最顶层可用于整个应用Session 的方法,Session管理着所有与数据库之间的会话
from sqlalchemy.ext.declarative import declarative_base  # 要继承这个类
from sqlalchemy import Column, ForeignKey  # 写字段 条件
from sqlalchemy import Integer,String,Text,Date,DateTime, Column
from sqlalchemy import create_engine, Table  #  链接数据库

 # 单独执行 model 文件
Base = declarative_base()

class Users(Base):
    __tablename__ = 'users'  # 要建的 表名
    id = Column(Integer, primary_key=True)  #
    username = Column(String(32), index=True, nullable=False)  # index加 索引nullable 为空
    depart_id = Column(Integer) # Integer  int
    password = Column(String(64), nullable=False)
    email = Column(String(64), nullable=False, index=True)


def create_all():
    engine = create_engine(
        "mysql+pymysql://root:1qaz2wsx@127.0.0.1:3306/testdb?charset=utf8",
        max_overflow=0,  # 超过连接池大小外最多创建的连接
        pool_size=5,  # 连接池大小
        pool_timeout=30,  # 池中没有线程最多等待的时间，否则报错
        pool_recycle=-1  # 多久之后对线程池中的线程进行一次连接的回收（重置）
    )
    Base.metadata.create_all(engine)

def drop_all():
    '''删表:return:'''
    engine = create_engine(
        "mysql+pymysql://root:123456@127.0.0.1:3306/db2?charset=utf8",
        max_overflow=0,  # 超过连接池大小外最多创建的连接
        pool_size=5,  # 连接池大小
        pool_timeout=30,  # 池中没有线程最多等待的时间，否则报错
        pool_recycle=-1  # 多久之后对线程池中的线程进行一次连接的回收（重置）
    )
    Base.metadata.drop_all(engine)

if __name__ == '__main__':
    drop_all()  
    create_all()
```



