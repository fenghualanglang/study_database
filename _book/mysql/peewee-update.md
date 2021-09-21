
#### 1.3.5 peewee之update

- save  

  ```python
  p = User(username='zhangsan', score=83, gender=1)
  p.save()
  
  p.username = 'abc'
  p.save()
  ```

   在执行第一个 `save()` 方法的时候，主键没值，所以执行 `INSERT`，`save()` 方法执行之后，自增列的值就返回并赋给了模型实例，所以第二次调用 `save()` 执行的是 `UPDATE`。
  模型中如没有指定主键，peewee 会自动增加一个名为 id 的自增列作为主键。 

- update 返回值影响行数

  ```python
  User.update({User.username: 'zhangsan', User.score: 78}).where(User.id == 12).execute()
  ```

  ```python
  User.update({'username': 'zhangsan', 'score':  78}).where(User.id==12).execute()
  ```

  ```python
  User.update(username='zhangsan', score=78).where(User.id == 12).execute()
  ```

  ```python
  User.update(User.age=User.age+1).where(User.username == 12).execute()
  ```

-  原子更新 Atomic  

  ```python
  subquery = Tweet.select(fn.COUNT(Tweet.id)).where(Tweet.user == User.id)
  update = User.update(num_tweets=subquery)
  update.execute()
  ```

-   **upsert如指定键存在就更新，如不存在执行插入** 

  无论以前是否存在过，存在更新，不存在则插入 **`replace`与`on_conflict_replace()`是等效的**

  ```python
  # last_login值将更新
  user_id = User.replace(username='the-user', gmt_created=datetime.now()).execute()
  user_id = User.insert(username='the-user', gmt_created=datetime.now()).on_conflict_replace().execute()
  
  ```

-  **ON DUPLICATE KEY UPDATE** 
  ```python
  class User(Model):
      username = TextField(unique=True)
      last_login = DateTimeField(null=True)
      login_count = IntegerField()
  
  #插入一个新用户
  User.create(username='huey', login_count=0)
  
  # 模拟用户登录. 
  登录计数和时间戳，要么正确创建，要么更新。
  now = datetime.now()
  userid = User.insert(
      username='maji',
      last_login=datetime.now(),
      login_count=1
  ).on_conflict(
      conflict_target=[User.username],
      preserve=[User.last_login],
      update={User.login_count: User.login_count + 1}
  ).execute()
  ```
  ```python
  class KV(Model):
      key = CharField(unique=True)
      value = IntegerField()
  KV.create(key='k1', value=1)
  
  # Demonstrate usage of EXCLUDED.
  # Here we will attempt to insert a new value for a given key. If that
  # key already exists, then we will update its value with the *sum* of its
  # original value and the value we attempted to insert -- provided that
  # the new value is larger than the original value.
  query = (KV.insert(key='k1', value=10)
           .on_conflict(conflict_target=[KV.key],
                        update={KV.value: KV.value + EXCLUDED.value},
                        where=(EXCLUDED.value > KV.value)))
  
  # Executing the above query will result in the following data being
  # present in the "kv" table:
  # (key='k1', value=11)
  query.execute()
  
  # If we attempted to execute the query *again*, then nothing would be
  # updated, as the new value (10) is now less than the value in the
  # original row (11).
  ```

