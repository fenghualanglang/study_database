### 1.2.5 sqlalchemy之select<二>

#### 一、高级查询

- ##### 复合查询

  ```python
  User.query.filter(User.name.startswith('13')).order_by(User.id.desc()).offset(2).limit(5).all()
  ```

  ```python
  query = User.query.filter(User.name.startswith('13'))
  query = query.order_by(User.id.desc())
  query = query.offset(2).limit(5)
  ret = query.all()
  ```

- ##### 优化查询

  ```python
  user = User.query.filter_by(id=1).first()  # 查询所有字段
  select user_id, mobile......
  
  select * from   # 程序不要使用
  select user_id, mobile,.... # 查询指定字段
  
  from sqlalchemy.orm import load_only
  User.query.options(load_only(User.name, User.mobile)).filter_by(id=1).first() # 查询特定字段
  ```

- ##### 聚合查询

  ```python
  from sqlalchemy import func
  
  db.session.query(Relation.user_id, func.count(Relation.target_user_id)).filter(Relation.relation == Relation.RELATION.FOLLOW).group_by(Relation.user_id).all()
  ```

#### 二、多表关系

 -  User模型 

   ```python
   from sqlalchemy import Column, Integer, String
   from sqlalchemy import ForeignKey
   from sqlalchemy.orm import relationship
   from Models import Base
   
   class User(Base):
    __tablename__ = 'users'
    id = Column('id', Integer, primary_key=True, autoincrement=True)
    name = Column('name', String(50))
    age = Column('age', Integer)

    # 添加角色id外键(关联到Role表的id属性)
    role_id = Column('role_id', Integer, ForeignKey('roles.id'))
    # 添加同表外键
    second_role_id = Column('second_role_id', Integer, ForeignKey('roles.id'))

    # 添加关系属性，关联到role_id外键上
    role = relationship('Role', foreign_keys='User.role_id', backref='User_role_id')
    # 添加关系属性，关联到second_role_id外键上
    second_role = relationship('Role', foreign_keys='User.second_role_id', backref='User_second_role_id')
   ```

-  Role模型 

  ```python
  from sqlalchemy import Column, Integer, String
  from sqlalchemy.orm import relationship
  from Models import Base

  class Role(Base):
    __tablename__ = 'roles'
    id = Column('id', Integer, primary_key=True, autoincrement=True)
    name = Column('name', String(50))

    # 添加关系属性，关联到User.role_id属性上
    users = relationship("User", foreign_keys='User.role_id', backref="Role_users")
    # 添加关系属性，关联到User.second_role_id属性上
    second_users = relationship("User", foreign_keys='User.second_role_id', backref="Role_second_users")
  ```

   设置外键的时候`ForeignKey('roles.id')`这里面使用的是表名和表列
  
  在设置关联属性的时候`relationship('Role', foreign_keys='User.role_id', backref='User_role_id')`，这里的`foreign_keys`使用的时候类名和属性名 
  
  ```python
  u = User(name='tobi', age=200)
  r1 = Role(name='admin')
  r2 = Role(name='user')

  u.role = r1
  u.second_role = r2

  session.add(u)
  session.commit()

  # 查询（对于外键关联的关系属性可以直接访问，在需要用到的时候session会到数据库查询）

  roles = session.query(Role).all()
  for role in roles:
      print 'role:{0} users'
      for user in role.users:
          print '{0}'.format(user.name)
      print 'role:{0} second_users'
      for user in role.second_users:
          print '{0}'.format(user.name)

  ```
  
   上面表示的是一对多（多对一）的关系，还有一对一，多对多，如果要表示一对一的关系，在定义relationship的时候设置`uselist`为False（默认为True），如在Role中 
  
  ```python
  class Role(Base):
      ...
      user = relationship("User", uselist=False, foreign_keys='User.role_id', backref="Role_user")
  ```



#### 三、关联查询

- ##### 使用ForeignKey

  ```python
  class User(db.Model):
      ...
      profile = db.relationship('UserProfile', uselist=False)
      followings = db.relationship('Relation')
  
  class UserProfile(db.Model):
      id = db.Column('user_id', db.Integer, db.ForeignKey('user_basic.user_id'), primary_key=True,  doc='用户ID')
      ...
  
  class Relation(db.Model):
      user_id = db.Column(db.Integer, db.ForeignKey('user_basic.user_id'), doc='用户ID')
      ...
  
  # 测试   
  user = User.query.get(1)
  user.profile.gender
  user.followings
  ```

- ##### 使用primaryjoin

  ```python
  class User(db.Model):
      ...
  
      profile = db.relationship('UserProfile', primaryjoin='User.id==foreign(UserProfile.id)', uselist=False)
      followings = db.relationship('Relation', primaryjoin='User.id==foreign(Relation.user_id)')
  
  # 测试
  user = User.query.get(1)
  user.profile.gender
  user.followings
  ```

- ##### 指定字段关联查询

  ```python
  class Relation(db.Model):
      ...
      target_user = db.relationship('User', primaryjoin='Relation.target_user_id==foreign(User.id)', uselist=False)
  
  from sqlalchemy.orm import load_only, contains_eager
  
  Relation.query.join(Relation.target_user).options(load_only(Relation.target_user_id), contains_eager(Relation.target_user).load_only(User.name)).all()
  
  关联查询a表字段，b表字段；用先join 然后再contains_eager选择相应字段
  ```

#### 四、多表查询

由于关系数据库的多个表还可以用外键实现一对多、多对多等关联，相应地，ORM框架也可以提供两个对象之间的一对多、多对多等功能。

例如，如果一个User拥有多个Book，就可以定义一对多关系如下：

```python
class User(Base):
    __tablename__ = 'user'
    id = Column(String(20), primary_key=True)
    name = Column(String(20))
    
    # 一对多:
    books = relationship('Book')
    
class Book(Base):
    __tablename__ = 'book'
    id = Column(String(20), primary_key=True)
    name = Column(String(20))
    
    # “多”的一方的book表是通过外键关联到user表的:
    user_id = Column(String(20), ForeignKey('user.id'))
```

 当我们查询一个User对象时，该对象的books属性将返回一个包含若干个Book对象的list。

 多表查询通常使用`join`内连接进行表连接，第一个参数为表名，第二个参数为条件，例如 

```python
users = db.session.query(User).join(Role, Role.id == User.role_id)

for u in users:
    print u.name
```

还可以直接查询多个表，如下

```python
result = db.session.query(User, Role).filter(User.role_id = Role.id)
# 这里选择的是两个表，使用元组获取数据
for u, r in result:
      print u.name
```

#### 五、事务

```python
environ = {'wsgi.version':(1,0), 'wsgi.input': '', 'REQUEST_METHOD': 'GET', 'PATH_INFO': '/', 'SERVER_NAME': 'itcast server', 'wsgi.url_scheme': 'http', 'SERVER_PORT': '80'}

with app.request_context(environ):
    try:
        user = User(mobile='18911111111', name='itheima')
        db.session.add(user)
        db.session.flush() # 将db.session记录的sql传到数据库中执行
        profile = UserProfile(id=user.id)
        db.session.add(profile)
        db.session.commit()
    except:
        db.session.rollback()
```

#### 六、其他

| backref        | 在关系的另一模型中添加反向引用                               |
| -------------- | ------------------------------------------------------------ |
| primary join   | 明确指定两个模型之间使用的联结条件                           |
| uselist        | 如果为False，不使用列表，而使用标量值                        |
| order_by       | 指定关系中记录的排序方式                                     |
| secondary      | 指定多对多关系中关系表的名字                                 |
| secondary join | 在SQLAlchemy中无法自行决定时，指定多对多关系中的二级联结条件 |

```python
# 一对多关系, 同步到数据库外键的关系
user_id = db.Column(db.Integer, db.ForeignKey('user.id'))  # 外检不允许为空
user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)  # 外检不允许为空
```
```python
# 增加一个字段，代码层面的关系
articles = db.relationship('Article', backref='user', lazy='dynamic')
comments = db.relationship('Comment', backref='user')
comments = db.relationship('Comment', backref='news')
```


