### 1.4.3 sqlalchemy之insert

peewee连接数据库根据场景目前总结常用的集中情况情况，可根据使用的场景自行选择连接方式。

### 一、<font color=red><b>insert</b></font>

- 执行原生sql， 如果是增删改，需要commit

  ```python
  session.execute('select * from User')
  ```

- add 

  ```python
  obj = Users(username='zhangsan', depart_id=10, email='123@qq.com')
  obj = Users(**{'username':'zhangsan', 'depart_id':10, 'password':'123', 'email':'123@qq.com'})
  session.add(obj)
  session.commit()
  ```

-  add_all 

  ```python
  session.add_all([        
      Users(username='zhangsan', depart_id=10, email='123@qq.com'),
      Users(username='lisi', depart_id=10, email='123@qq.com')
  ])
  session.commit()
  ```

二、<font color=red><b>delete</b></font>

-  方式一 

  ```python
  user = User.query.order_by(User.id.desc()).first()
  user = session.query(Article).get(10)
  session.delete(user)
  session.commit()
  ```

- 方式二

  ```python
  User.query.filter(User.mobile='18512345678').delete()
  session.commit()
  ```

- 方法三

  ```python
  a = session.query(Article).get(10)
  session.delete(a)
  session.commit()
  ```

三、<font color=red><b>update</b></font>

- 方式一 

  ```python
  user = User.query.get(1)
  user.name = 'Python'
  db.session.add(user)
  db.session.commit()
  ```

  使用merge方法，如果存在则修改，如果不存在则插入（只判断主键，不判断unique列）*

  ```python
  user.name = 'admin'
  session.merge(user)
  ```

-  方式二 

  ```python
   User.query.filter_by(id=1).update({'username':'python'})
   session.commit()
  ```

- 方式三

  ```python
  session.query(Users).filter(Users.id ==4).update('name':Users.username+"DSB"},synchronize_session=False)
  ```

