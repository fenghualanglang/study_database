```shell
[root@localhost ~]# wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
[root@localhost ~]# yum -y install mysql57-community-release-el7-10.noarch.rpm
[root@localhost ~]# yum -y install mysql-community-server

[root@localhost ~]# systemctl start  mysqld.service     //启动MySQL
[root@localhost ~]# systemctl status mysqld.service     //运行状态
[root@localhost ~]# grep "password" /var/log/mysqld.log // 初始密码

```

##### 更改配置文件
vim /etc/my.cnf

```
[mysqld]  
character_set_server=utf8         // 添加
collation-server=utf8_general_ci  // 添加
[client]
default-character-set=utf8        // 添加
```

##### 启动mysql

```shell
sudo service mysqld start      //启动mysql
sudo service mysqld status     //查看mysql状态
sudo systemctl enable mysqld   //配置开机启动
```

##### 删除mysql

```shell
sudo yum remove mysql-community-*
rm -rf /var/lib/mysql
rm /etc/my.cnf
```

