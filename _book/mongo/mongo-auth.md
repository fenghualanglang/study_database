#### 3.1.4 MongoDB 权限分配
- ```shell
mongo 
mongo 172.16.0.13:27017/admin -u admin -p password
mongo -u 用户名 -p 密码 --port 端口号 --host IP 数据库名
  mongo 192.168.0.1:27017/库名  -u 用户名 -p 密码
  
  show dbs      查看库
  use admin     使用某库
  show users     查看用户
  ```
```

- <b>超级管理员</b>  (用来管理用户的用户，可以通过超级管理员创建、删除用户 )

	```shell
	db.createUser({user:"admin",pwd:"admin123456",roles:[{role: "userAdminAnyDatabase", db: "admin"}]}) 
```

- <b>超级管理员root</b>( root角色用于关闭数据库 )

	```shell
	db.createUser({user: "root",pwd: "password", roles: [{role:"root", db: "admin" } ]})
	```

- 创建指定数据库testdb的用户，并且分配权限

  ```shell
  use testdb
  db.createUser({user:'test',pwd:'test123',roles:[{role:'readWrite', db:'testdb'}]})
  db.auth('test', 'test123') 
  ```

- 修改权限、密码

  ```shell
  use testdb
  db.createUser({user:'test',pwd:'test123',roles:[{role:'readWrite', db:'testdb'}]})
  db.changeUserPassword('user','new password'); 
  ```

- 删除账号 (删除用户必须由账号管理员来删)

  ```shell
  use admin
  db.auth("admin","password")
  
  db.system.users.remove({user:"XXXXXX"})   删除单个用户
  db.system.users.remove({})   			  删除所有用户
  ```

- 注

  ```powershell
  [role]
  dbOwner         owner权限  
  readWrite       读写权限
  ```

  

