#### 3.1.3 centos 之MongoDB 安装与配置


1. <b>配置程序包管理系统</b>

   创建一个`/etc/yum.repos.d/mongodb-org-4.4.repo`文件，使用yum以下命令安装MongoDB 
    ```shell
    [mongodb-org-4.4]
    name=MongoDB Repository
    baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/
    gpgcheck=1
    enabled=1
    gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
    ```

2. ##### 安装MongoDB软件包

   ```shell
   sudo yum install -y mongodb-org
   ```

   ```shell
/var/lib/mongo       数据目录
   /var/log/mongodb     日志目录
   /etc/yum.conf        配置文件 
   ```

3. ##### 系统初始化服务

   ```shell
   service mongod start  		 启动MongoDB
   chkconfig mongod on          系统重新启动
   service mongod stop     	 停止MongoDB
   service mongod restart       重新启动MongoDB
   
   mongo                        使用MongoDB
   mongo 172.16.0.13:27017/admin -u admin -p password
   
   sudo yum erase $(rpm -qa | grep mongodb-org)  删除软件包
   sudo rm -r /var/log/mongodb                   删除MongoDB数据库
   sudo rm -r /var/lib/mongo                     删除MongoDB日志文件
   ```

   如果在启动时收到类似于以下内容的错误 [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#mongodb-binary-bin.mongod)：

   ```shell
   Failed to start mongod.service: Unit mongod.service not found.
   ```

   首先运行以下命令：

   ```shell
   sudo systemctl daemon-reload
   ```

   然后再次运行上面的启动命令。 

4. ##### 配置文件

    ```javascript
    systemLog:
      destination: file
      logAppend: true
      path: /var/log/mongodb/mongod.log

    # Where and how to store data.
    storage:
      dbPath: /var/lib/mongo      db目录
      journal:
        enabled: true
    #  engine:
    #  wiredTiger:

    # how the process runs
    processManagement:
      fork: true  				后台运行
      pidFilePath: /var/run/mongodb/mongod.pid  # location of pidfile
      timeZoneInfo: /usr/share/zoneinfo

    # network interfaces
    net:
      port: 27017         端口号（企业使用尽量不要用默认端口）
      bindIp:  0.0.0.0 

    #security:
    #operationProfiling:
    #replication:
    #sharding:
    ## Enterprise-Only Options
    #auditLog:
    ```

