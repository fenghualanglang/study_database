
#### 1.3.4 peewee之delete

- 删除调用delete()方法，使用execute()执行，  返回值为成功删除的数据条数 

  ```python
  User.delete().where(User.id == 10).execute()
  User.delete().where(~fn.EXISTS(subq))
  ```

-  要删除单个模型实例，可以使用 快捷方式。`Model.delete_instance() delete_instance()`

  ```python
  user = User.get(User.id == 11)
  user.delete_instance()  
  ```

  1. 返回删除条数
  2. 如果不存在则报UserDoesNotExist: instance matching query does not exist






















