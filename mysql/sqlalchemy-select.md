### 1.2.4 sqlalchemy之select 《其一》

- <font color=red><b>get</b></font>  根据主键ID获取对象，若主键不存在返回None

  ```python
  User.query.get(2)
  ```
  
- <font color=red><b>all</b></font>  查询所有，返回列表 

  ```python
  User.query.all()
  ```

- <font color=red><b>first()</b></font>  查询第一个，返回对象

  ```python
  User.query.first()
  ```

- <font color=red><b>count()</b></font>  计数

   对每一项分别计数的时候，我们需要在`func`模块里通过`func.count()`来直接指定`count()`函数，下面我们可以单独计算一下每个名称的数量 

  ```python
   from sqlalchemy import func 
  
   session.query(func.count(User.name), User.name).group_by(User.name).all() 
  ```

  ```python
  SELECT count(*) FROM table
  
  session.query(func.count('*')).select_from(User).scalar() 
  ```

  ```python
  根据User的主键来计算，那么select_from()可以去掉
  
  session.query(func.count(User.id)).scalar() 
  ```

- <font color=red><b>起别名与查询部分字段 </b></font>

   字段起别名 lable() 

  ```python
  query(Users.id, Users.name.label('cname')).all()
  ```

  数据表起别名

  ```python
  from sqlalchemy.orm import aliased
  
  user_alias = aliased(User, name='user_alias')
  for row in session.query(user_alias, user_alias.name).all(): 
      print(row.user_alias) 
  ```

- <font color=red><b>filter_by</b></font>   进行过虑 

  ```python
  User.query.filter_by(username='python').first()
  User.query.filter_by(username='python, sex=1).first()  # and关系
  ```

- <font color=red><b>filter</b></font>   进行过虑 

  ```python
  User.query.filter(User.username=='python').first()
  ```
  
- <font color=red><b>或 or_、与and_、非not_</b></font>  

  ```python
  from sqlalchemy import or_、or、not_
  User.query.filter(or_(User.sex==1, User.username.endswith('jie'))).all()
  User.query.filter(and_(User.username != 'python',User.email.startswith('131'))).all()
  session.query(Users).filter(Users.id > 1, Users.username == 'python').all()
  User.query.filter(not_(User.sex == 1).all()
  ```
  
- <font color=red><b>offset</b></font>   偏移，起始位置 

  ```python
  User.query.offset(2).all()
  ```

- <font color=red><b>limit  </b></font>获取限制数据

  ```python
  User.query.limit(3).all()
  ```

- <font color=red><b>order_by   </b></font>   排序 

  ```python
  User.query.order_by(User.id).all()  # 正序
  User.query.order_by(User.id.desc()).all()  # 倒序
  ```

- <font color=red><b>distinct   </b></font>   去除查询

  ```python
  from sqlalchemy import distinct
  
  users = session.query(distinct(User.name).label('name')).all()
  ```

- <font color=red><b>between   </b></font>   之间查询

  ```
  session.query(Users).filter(Users.id.between(1, 3), Users.username == 'python').all()
  ```

- <font color=red><b>in 与  not  in   </b></font>  

  ```python
  session.query(Users).filter(Users.id.in_([1,3,4])).all()
  session.query(Users).filter(~Users.id.in_([1,3,4])).all()
  ```

  ```python
  session.query(Users).filter(User.name.in_(['ed', 'wendy', 'jack']))
  
  # works with query objects too:
  session.query(Users).filter(User.name.in_(
          session.query(User.name).filter(User.name.like('%ed%'))
  ))
  ```

- <font color=red><b>is  null 和 is not null</b></font>

  ```python
  session.query(Users).filter(User.name == None)
  
  # alternatively, if pep8/linters are a concern
  session.query(Users).filter(User.name.is_(None))
  ```

  ```python
  session.query(Users).filter(User.name != None)
  
  # alternatively, if pep8/linters are a concern
  session.query(Users).filter(User.name.isnot(None))
  ```

- <font color=red><b>and 和 or </b></font>

  ```python
  from sqlalchemy import and_, or_
  
  session.query(User).filter(and_(User.name == 'ed', User.fullname == 'Ed Jones')).all()
  session.query(User).filter(User.name == 'ed', User.fullname == 'Ed Jones')
  session.query(User).filter(User.name == 'ed').filter(User.fullname == 'Ed Jones')
  
  session.query(Users).filter(or_(Users.id < 2, Users.name == 'eric')).all()
  
  session.query(Users).filter(
      or_(Users.id < 2,and_(Users.name == 'eric', Users.id > 3),Users.extra != "")
      ).all()
  ```

- <font color=red><b>first() </b></font> 对查询结果进行了一个限制-返回列表的第一个值 

- <font color=red><b>all()</b></font>all()  返回一个列表 

- <font color=red><b>one</b></font> 创建Query查询，匹配不到，返回一个错误，或者匹配到多个值也会返回错误

  ```python
  user = session.query(User).filter(User.id=='5').one()
  ```
  
- <font color=red><b>one_or_none()</b></font>   查询不到直接返回None，匹配到多个值也会返回错误

   ```python
   user = session.query(User).filter(User.id=='5').one_or_none()
   ```

- <font color=red><b>exists</b></font>查询(不存在则为~exists())

   ```python
   from sqlalchemy.sql import exists
   
   session.query(User.name).filter(~exists().where(User.role_id == Role.id))
   ```

- <font color=red><b> any </b></font>除了exists，any也可以表示EXIST

   ```python
   session.query(Role).filter(Role.users.any())
   ```

- <font color=red><b> random </b></font>

   ```python
   from sqlalchemy.sql.functions import random
   
   user = session.query(User).order_by(random()).first()
   ```

- <font color=red><b>通配符 </b></font>

  ```python
  ret = session.query(Users).filter(Users.name.like('e%')).all()
  ret = session.query(Users).filter(~Users.name.like('e%')).all()   # ~ 是 not
  
  ret = session.query(User).filter(User.name.like('%ed%'))
  ```

- <font color=red><b>match </b></font>

  ```
  session.query(User).filter(User.name.match('wendy'))
  ```

   `match()` 和数据库底层有关 使用`MATCH` 或者 `CONTAINS` 来实现

- <font color=red><b>切片 </b></font>

  ```python
  result = session.query(Users)[1:2]
  ```

- <font color=red><b>group by  </b></font> 分组

  ```python
  from sqlalchemy.sql import func
  users = session.query(func.count(User.name), User.age).group_by(User.age)
  
  ret = session.query(
      Users.depart_id, func.count(Users.id),
  ).group_by(
      Users.depart_id
  ).having(
      func.count(Users.id) >= 2
  ).all()
  ```

- <font color=red><b>union 和 union all</b></font> 

  ```python
  """
  组合:
  select id,name from users
  UNION    #去重组合  不去重union all
  select id,name from users;
  """
  # q1 = session.query(Users.name).filter(Users.id > 2)
  # q2 = session.query(Favor.caption).filter(Favor.nid < 2)
  # ret = q1.union(q2).all()
  
  # q1 = session.query(Users.name).filter(Users.id > 2)
  # q2 = session.query(Favor.caption).filter(Favor.nid < 2)
  # ret = q1.union_all(q2).all()
  ```
  
- <font color=red><b>其它 </b></font> 

  -  `scalar()`援引自`one()`函数，查询成功之后会返回这一行的第一列参数 
  
    ```python
    query = session.query(User.id).filter(User.name == 'ed').order_by(User.id)
    query.scalar()
  ```
  
  

