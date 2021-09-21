#### 1.3.6 peewee之select

#### 一、get

- get  方法检索与给定查询匹配的单个实例 

  ```python
  p1 = User.get(username='张三')
  p2 = User.get(username.Name == '李四')
  ```

   当获取的结果不存在时，报 `Model.DoesNotExist` 异常。

  如果有多条记录满足条件，则返回第一条 

- get_or_none 

   如果当获取的结果不存在时，不想报错，可以使用 `Model.get_or_none()` 方法，会返回 `None`，参数和 `get` 方法一致。 

- get_by_id 对于主键查找，还可以使用快捷方法`Model.get_by_id()`。

  ```
  User.get_by_id(1)
  ```

- get_or_create  首先尝试检索匹配的行。如果失败，将创建一个新行。 

  ```python
  p, created = User.get_or_create(username='赵六', defaults={'username': "赵六", 'score': 75, 'gender':1})
  print(p, created)  # 存在返回id false  存在添加后返回id true
  
  person, created = Person.get_or_create(first_name=first_name,last_name=last_name,defaults={'dob': dob, 'favorite_color': 'green'})
  ```

#### 二、select

- 获取总条数方法

  ```python
  User.select().count()
  ```

- 查询部分字段

  ```python
  user3= User.select(User.user_name)
  ```

- where 条件查询

  ```python
  user4 = User.select().where(User.id == 12)
  ```

- 随机查询(需要先引入fn)

  ```python
  from peewee import fn
  
  user5 = User.select().order_by(fn.Random()).limit(2)
  ```

- 排序查询

  ```python
  正序
  user6 = User.select().order_by(User.dept_id.asc())
  倒序加分页
  user6 = User.select().order_by(User.age.desc()).paginate(3, 5)   
  参数1 从第几个数据开始 参数2 要取多少条数据
  ```
- 复合条件
<table>
<tr align="left">
<td>逻辑符</td>
<td>含义</td>
<td>样例</td>
</tr>
<tr align="left">
<td>&</td>
<td>and</td>
<td>Person.select().where((Person.Name == '张三') & (Person.Age == 30))</td>
</tr>
<tr align="left">
<td>|</td>
<td>or</td>
<td>Person.select().where((Person.Name == '张三')|(Person.Age==30))</td>
</tr>
<tr align="left">
<td>~</td>
<td>not</td>
<td>Person.select().where(~Person.Name == '张三')</td>
</tr>
</table>
  ```python
  query1 = Person.select().where((Person.name == "fff0") | (Person.name == "sss1"))
  query2 = Person.select().where((Person.name == "fff") & (Person.is_relative == True))
  ```
  特别注意：有多个条件时，每个条件\*\*必须**用 () 括起来。 
  当条件全为 \*and\* 时，也可以用逗号分隔，\*get\* 和 \*select\* 中都可以： 
  ```python
  Person.get(Person.Name == '张三', Person.Age == 30) 
  ```
- 比较运算符
<table border="1" align="center" bordercolor="#4A766E" cellpadding="0" cellspacing="0">
<tr>
<th>运算符</th>
<th>含义 </th>
<th>运算符</th>
<th>含义</th>
</tr>
<tr>
<td>==</td>
<td>等于 </td>
<td>!=</td>
<td>不等于</td>
<tr>
<td>< </td>
<td>小于 </td>
<td><=</td>
<td>小于等于 </td>
<tr>
<td>> </td>
<td>大于 </td>
<td>>=</td>
<td>大于等于 </td>
</tr>
<tr>
<td><< </td>
<td>x in y，其中 y 是列表或查询</td>
<td>>> </td>
<td> x is y, 其中 y 可以是 None </td>
</tr>
<tr>
<td>%</td>
<td>x like y</td>
<td> ** </td>
<td>x like y</td>
</tr>
</table>

  ```python
  st8 = User.select().where(User.score >= 80) 
  st8 = StudentsInfo.select().where(StudentsInfo.english_sore>StudentsInfo.chinese_sore)
  
  st8 = User.select().where(User.username % '%子%') 
  st8 = User.select().where(User.username % 'zhang*')  区分大小写
st8 = User.select().where(User.username ** 'zhang*')  不区分大小写
  ```

- contains

  ```python
  query = User.select().where(User.username.crontains('zhang'))
  ```

- in not_in

  ```python
  query = User.select().where(User.id.in_([1, 5]))
  
  st9 = User.select(User.username).where(User.id > 880)
  sc = UserAuth.select().where(UserAuth.Auth.not_in(st9))
  ```

- **None / Null / Empty** 

  ```python
  Message.select().where(Message.somecolumn.is_null(False))
  ```

- 查询扩展字段

  ```python
  query = User.get_or_none(id = 1)
  query.penpal = 'langwang'
  query.girl_friend= 'xiaoma'
  res = model_to_dict(query , extra_attrs=["penpal", "girl_friend"])
  ```
  ```python
  {'id': 13, 'gender': '0','score': 45, 'username': 'sunsan', 'girl_friend': 'xiaoma', 'penpal': 'langwang'}
  ```

-  字典展示(还可以tuples(),namedtuples(),objects()) 

  ```
  user = User.select().dicts()
  user = User.select().tuples()
  user = User.select().namedtuples()
  ```

- Order by; Limit; Distinct; Group by; Having

  ```python
  query = Person.select(Person.name).order_by(Person.name).limit(10).distinct() 
  # 几乎和sql一模一样Person.select().order_by(Person.birthday.desc())  # 日期排序
  
  query = Booking.select(
      Booking.facid,
      fn.SUM(Booking.slots)
  ).group_by(
      Booking.facid
  ).order_by(
      Booking.facid
  )

  query = Booking.select(
      Booking.facility, 
      fn.SUM(Booking.slots)
  ).group_by(
      Booking.facility
  ).having(
      fn.SUM(Booking.slots) > 1000
  ).order_by(
      Booking.facility
      )
  Tweet.select().order_by(-Tweet.created_date) 
  # Similarly you can use "+" to indicate ascending order, though ascending
  ```
  
- 聚合函数 

  ```python
  SELECT MAX(birthday) FROM person;
  query = Person.select(fn.MAX(Person.birthday))
  
  SELECT name, is_relative FROM person WHERE birthday = (SELECT MAX(birthday) FROM person);
  MemberAlias = Member.alias()  
  # 如果一个查询中用了两个表，需要这个Alias作为影子
  subq =MemberAlias.select(fn.MAX(MemberAlias.joindate))
  query = (Member.select(Person.is_relative, Person.name, ).where(Person.birthday == subq))
  ```


- on_conflict

  ```python
  class User(Model):
      username = TextField(unique=True)
      last_login = DateTimeField(null=True)
      login_count = IntegerField()
  
  # Insert a new user.
  User.create(username='huey', login_count=0)
  
  # Simulate the user logging in. The login count and timestamp will be
  # either created or updated correctly.
  now = datetime.now()
  rowid = (User
           .insert(username='huey', last_login=now, login_count=1)
           .on_conflict(
               preserve=[User.last_login],  # Use the value we would have inserted.
               update={User.login_count: User.login_count + 1})
           .execute())
  ```

   我们可以根据需要安全地调用upsert查询多次。

  登录计数将自动增加，最后一个登录列将更新，并且不会创建重复的行。 

- join.left_outer

  ```python
  SysDeptDb.select(
  	BaseDatabase.id,
  	BaseDatabase.db_name
  ).join(
      BaseDatabase, JOIN.LEFT_OUTER, on=(BaseDatabase.id == SysDeptDb.db_id)
  ).where(
      SysDeptDb.dept_id == dept, SysDeptDb.status == 1
  ).dicts()
  ```

-  fn.MAX 

  ```python
  from peewee import fn
  User.select(fn.MAX(User.score)).where(User.type == 1)
  ```

- 聚合行数

  ```python
  MemberAlias = Member.alias()  # 如果一个查询中用了两个表，需要这个Alias作为影子
  
  subq = MemberAlias.select(fn.MAX(MemberAlias.joindate))
  query = (Member.select(Person.is_relative, Person.name, ).where(Person.birthday == subq))
  ```

  ```python
  EquClusterAlias = EquCluster.alias()
  equ_alias = EquClusterAlias.select(
      BaseDatabase.db_name
  ).join(
      BaseDatabase, on=((BaseDatabase.cluster_id == EquClusterAlias.id) |(BaseDatabase.cluster_name == EquClusterAlias.cluster_name))
  ).where(
      EquClusterAlias.id == 1,
      EquClusterAlias.environment == 1
  ).dicts()
  ```

####  三、事务

- atomic和rollback 
  ```python
  with db.atomic() as transaction:  # Opens new transaction.
      try:
          save_some_objects()
      except ErrorSavingData:
          # Because this block of code is wrapped with "atomic", a
          # new transaction will begin automatically after the call
          # to rollback().
          transaction.rollback()
          error_saving = True
  ```
- atomic甚至可以多层嵌套
  ```python
  db = SqliteDatabase(':memory:')
  with db.atomic() as txn:
      # This is the outer-most level, so this block corresponds to
      # a transaction.
      User.create(username='charlie')
      with db.atomic() as nested_txn:
          # This block corresponds to a savepoint.
          User.create(username='huey')
          # This will roll back the above create() query.
          nested_txn.rollback()
      User.create(username='mickey')

  # When the block ends, the transaction is committed (assuming no error
  # occurs). At that point there will be two users, "charlie" and "mickey".
  ```
- 手动、半自动
  ```python
  # 纯手动用commit()提交，用rollback回滚

  with db.transaction() as txn:
      User.create(username='mickey')
      txn.commit()  # Changes are saved and a new transaction begins.
      User.create(username='huey')

      # Roll back. "huey" will not be saved, but since "mickey" was already
      # committed, that row will remain in the database.
      txn.rollback()

  # 半自动用savepoint上下文管理
  with db.transaction() as txn:
      with db.savepoint() as sp:
          User.create(username='mickey')

      with db.savepoint() as sp2:
          User.create(username='zaizee')
          sp2.rollback()  # "zaizee" will not be saved, but "mickey" will be.
  ```

#### 四、其它

```python
Person._meta.fields   # 用访问_meta访问定义的meta
Person._meta.primary_key   
Person._meta.database

# 属性有：database， table_name， table_function， indexes， primary_key， constraints， schema， only_save_dirty， options， table_alias， depends_on， without_rowid
```
-  .first() 
-  .exists() 

#### 五、简单示例

```python
query = session.query(User)
print list(query) #列出所有user
print query.get(1) #根据主键显示
print query.filter_by(user_name='dongwm').first() #类似于SQL的where,打印其中的第一个
u = query.filter_by(user_name='dongwm').first()
u.password = 'newpass' #修改其密码字段
session.commit() #提交事务
print query.get(1).password #打印会出现新密码
```

```python
for instance insession.query(User).order_by(User.id): 
根据id字段排序,打印其中的用户名和邮箱地址
  printinstance.user_name, instance.email_address
```

