

#### 2.2.8 主从复制读写分类

- 本机给那台主机当从机 

  ```c++
  方法一
  slaveof 主机ip 主机端口
  
  slaveof 106.1234.608 6379
  ```

  ```c++
  方法二
  redis-server --slaveof 106.1234.608 6379  --masterauth 1qaz2wsx > redis.log &
  ```

  ```c++
  方法三配置文件redis.conf
  
  slaveof 106.1234.608 6379
  masterauth 1qaz2wsx 
  ```

- 查看主从复制的信息

  ```c++
  info replication 
  ```

- 恢复主机身份，不给任何主机当从机

  ```c++
  slaveof no one
  ```

  

 

