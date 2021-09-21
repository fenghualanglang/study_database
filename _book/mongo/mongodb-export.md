### MongoDB 数据的批量备份、还原、导入与导出

 **mongodb数据备份和还原主要分为二种，一种是针对于库的mongodump和mongorestore，一种是针对库中表的mongoexport和mongoimport。** 

### 一、mongodump备份数据库

#### 1.常用命令格

```bash
mongodump -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件存在路径 
```

> 如果没有用户谁，可以去掉-u和-p。
> 如果导出本机的数据库，可以去掉-h。
> 如果是默认端口，可以去掉–port。
> 如果想导出所有数据库，可以去掉-d。

#### 2. 导出所有数据库

```bash
[root@localhost mongodb]$ mongodump -h 127.0.0.1 -o /usr/local/Cellar/mongodb 
connected to: 127.0.0.1  
Tue Dec  3 06:15:55.448 all dbs  
Tue Dec  3 06:15:55.449 DATABASE: test to /usr/local/Cellar/mongodb/test  
Tue Dec  3 06:15:55.449 test.system.indexes to /home/zhangy/mongodb/test/system.indexes.bson  
Tue Dec  3 06:15:55.450   1 objects  
Tue Dec  3 06:15:55.450   test.posts to /usr/local/Cellar/mongodb/test/posts.bson  
Tue Dec  3 06:15:55.480   0 objects  
```

#### 3. 导出指定数据库

```bash
[root@localhost mongodb]$ mongodump -h 192.168.1.108 -d tank -o /home/zhangy/mongodb/  
connected to: 192.168.1.108  
Tue Dec  3 06:11:41.618 DATABASE: tank to  /usr/local/Cellar/mongodb/tank  
Tue Dec  3 06:11:41.623     tank.system.indexes to /usr/local/Cellar/mongodb/tank/system.indexes.bson  
Tue Dec  3 06:11:41.623          2 objects  
Tue Dec  3 06:11:41.623     tank.contact to  /usr/local/Cellar/mongodb/tank/contact.bson 
Tue Dec  3 06:11:41.669          2 objects  
Tue Dec  3 06:11:41.670     Metadata for tank.contact to  /usr/local/Cellar/mongodb/tank/contact.metadata.json  
Tue Dec  3 06:11:41.670     tank.users to  /usr/local/Cellar/mongodb/tank/users.bson  
Tue Dec  3 06:11:41.685          2 objects  
Tue Dec  3 06:11:41.685     Metadata for tank.users to  /usr/local/Cellar/mongodb/tank/users.metadata.json  
```

### 二、mongorestore还原数据库

#### 1. 常用命令格式

```bash
mongorestore -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 --drop 文件存在路径
```

-- drop的意思是，先删除所有的记录，然后恢复。

#### 2. 恢复所有数据库到mongodb中

```bash
[root@localhost mongodb]$ mongorestore /usr/local/Cellar/mongodb/   # 这里的路径是所有库的备份路径  
```

#### 3. 还原指定的数据库

```bash
[root@localhost mongodb]$ mongorestore -d tank /usr/local/Cellar/mongodb/tank/ # tank这个数据库的备份路径  
[root@localhost mongodb]$ mongorestore -d tank_new  /usr/local/Cellar/mongodb/tank/ # 将tank还有tank_new数据库中
```

这二个命令，可以实现数据库的备份与还原，文件格式是json和bson的。无法指写到表备份或者还原。

### 三、mongoexport导出表，或者表中部分字段

| 命令简拼 |         |          |
| ---- | ---------- | -------- |
| d    | db         | 数据库 |
| c    | collection | 集合 |
| f    | fields     | 字段     |
| type | type       | 文件格式 |
|o|out|导出路径及文件名|
| q            | 查询条件 | -q '{ "uid" : "100" }' 导出uid为100的数据     |
| ------------ | -------- | --------------------------------------------- |
| upsert       | 插入更新 | 插入或者更新现有数据(还原整表导出的非csv文件) |
| drop         | 删除     | 先删除所有的记录，然后恢复                    |
| upsertFields | 插入更新 | 插入或者更新现有数据(还原部分字段的导出文件)  |
##### 1. 常用命令格式

```bash
mongoexport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 -f 字段 -q 条件导出 --csv -o 文件名 
```

##### **2. 导出整张表**

```bash
[root@localhost mongodb]# mongoexport -d tank -c users -o /home/zhangy/mongodb/tank/users.dat 
connected to: 127.0.0.1 
exported 4 records 
```

##### **3. 导出表中部分字段**

```bash
[root@localhost mongodb]# mongoexport -d tank -c users --csv -f uid,name,sex -o tank/users.csv 
```

```bash
mongoexport --db db_name --collection collection_name --type=csv --fields title,link,publish,read --out ~/Desktop/posts.csv
```

**4. 根据条件敢出数据**

```bash
[root@localhost mongodb]# mongoexport -d tank -c users -q '{uid:{$gt:1}}' -o tank/users.json 
```

#### **四、mongoimport导入表，或者表中部分字段**

##### 1.1. 还原整表导出的非csv文件

```bash
mongoimport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 --upsert --drop 文件名 
```

##### 1.2. 还原部分字段的导出文件

```bash
mongoimport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 --upsertFields 字段 --drop 文件名
```

##### 1.3. 还原导出的csv文件

```bash
mongoimport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 --type 类型 --headerline --upsert --drop 文件名  
```

##### 1.4 上面三种情况，还可以有其他排列组合的

##### 1.5 还原导出的表数据

```bash
[root@localhost mongodb]$ mongoimport -d tank -c users --upsert tank/users.dat  
connected to: 127.0.0.1  
Tue Dec  3 08:26:52.852 imported 4 objects
```

##### 1.6 部分字段的表数据导入

```bash
[root@localhost mongodb]$ mongoimport -d tank -c users  --upsertFields uid,name,sex  tank/users.dat  
connected to: 127.0.0.1  
Tue Dec  3 08:31:15.179 imported 4 objects  
```

##### 1.7 还原csv文件

```bash
[root@localhost mongodb]$ mongoimport -d tank -c users --type csv --headerline --file tank/users.csv  
connected to: 127.0.0.1  
Tue Dec  3 08:37:21.961 imported 4 objects  
```

 **转载:海底苍鹰地址:http://blog.51yip.com/nosql/1573.html** 