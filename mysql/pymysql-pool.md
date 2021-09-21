#### 1.3.3 MySQL之 DBUtils.PooledDB 

使用dbutils的PooledDB连接池，操作数据库。

这样就不需要每次执行sql后都关闭数据库连接，频繁的创建连接，消耗时间

如果是使用一个连接一直不关闭，多线程下，插入超长字符串到数据库，运行一段时间后很容易出现OperationalError: (2006, ‘MySQL server has gone away’)这个错误。

## 示例一

- MyDbUtils：

  ```python
  #-*- coding: UTF-8 -*-
  import pymysql
  from DBUtils.PooledDB import PooledDB
  import DB_config as Config
  import MySQLdb
  '''
  @功能：PT数据库连接池
  '''
  class PTConnectionPool(object):
      __pool = None;
      # def __init__(self):
      #     self.conn = self.__getConn();
      #     self.cursor = self.conn.cursor();
      def __enter__(self):
          self.conn = self.__getConn();
          self.cursor = self.conn.cursor();
          print u"PT数据库创建con和cursor";
          return self;
  
      def __getConn(self):
          if self.__pool is None:
              self.__pool = PooledDB(
                creator=MySQLdb, 
                mincached=Config.DB_MIN_CACHED , 
                maxcached=Config.DB_MAX_CACHED,
                maxshared=Config.DB_MAX_SHARED, 
                maxconnections=Config.DB_MAX_CONNECYIONS,
                blocking=Config.DB_BLOCKING, 
                maxusage=Config.DB_MAX_USAGE,
                setsession=Config.DB_SET_SESSION,
                host=Config.DB_TEST_HOST , 
                port=Config.DB_TEST_PORT ,
                user=Config.DB_TEST_USER ,
                passwd=Config.DB_TEST_PASSWORD ,
                db=Config.DB_TEST_DBNAME , 
                use_unicode=False, 
                charset=Config.DB_CHARSET
                );

        return self.__pool.connection()
  
      """
      @summary: 释放连接池资源
      """
      def __exit__(self, type, value, trace):
          self.cursor.close()
          self.conn.close()
          print u"PT连接池释放con和cursor";
      
      #重连接池中取出一个连接
      def getconn(self):
          conn = self.__getConn();
          cursor = conn.cursor();
          return cursor,conn
      
      #关闭连接归还给连接池
      # def close(self):
      #     self.cursor.close()
      #     self.conn.close()
      #     print u"PT连接池释放con和cursor";
      def getPTConnection():
          return PTConnectionPool()
  ```
  
- 配置文件：DB_config.py 

  

  ```python
  #-*- coding: UTF-8 -*-
  
  #TEST数据库信息
  DB_TEST_HOST="192.168.88.6";
  DB_TEST_PORT=3306;
  DB_TEST_DBNAME="asterisk";
  DB_TEST_USER="root";
  DB_TEST_PASSWORD="kalamodo";
  
  #数据库连接编码
  DB_CHARSET="utf8";
  #mincached : 启动时开启的闲置连接数量(缺省值 0 开始时不创建连接)
  DB_MIN_CACHED=10;
  #maxcached : 连接池中允许的闲置的最多连接数量(缺省值 0 代表不闲置连接池大小)
  DB_MAX_CACHED=10;
  #maxshared : 共享连接数允许的最大数量(缺省值 0 代表所有连接都是专用的)如果达到了最大数量,被请求为共享的连接将会被共享使用
  DB_MAX_SHARED=20;
  #maxconnecyions : 创建连接池的最大数量(缺省值 0 代表不限制)
  DB_MAX_CONNECYIONS=100;
  #blocking : 设置在连接池达到最大数量时的行为(缺省值 0 或 False 代表返回一个错误<toMany......>; 其他代表阻塞直到连接数减少,连接被分配)
  DB_BLOCKING=True;
  #maxusage : 单个连接的最大允许复用次数(缺省值 0 或 False 代表不限制的复用).当达到最大数时,连接会自动重新连接(关闭和重新打开)
  DB_MAX_USAGE=0;
  #setsession : 一个可选的SQL命令列表用于准备每个会话，如["set datestyle to german", ...]
  DB_SET_SESSION=None;
  ```

-  封装的mysqlhelp.py 

  ```python
  import MySQLdb
  from MyDbUtils import getPTConnection
  
  class MysqlHelp(object):
  
  mysql=None
  def __init__(self):
      # self.connect()
      self.db=getPTConnection()
  def __new__(cls, *args, **kwargs):
      if not hasattr(cls, 'inst'):
          cls.inst = super(MysqlHelp, cls).__new__(cls, *args, **kwargs)
      return cls.inst
  #查询所有
  def selectall(self,sql='',param=()):
      #判断是否连接,并设置重连机制
      # self.connected()
      try:
          cursor,conn=self.execute(sql,param)
          res=cursor.fetchall()
          self.close(cursor, conn)
          return res
      except Exception,e:
          print 'selectall except   ', e.args
          self.close(cursor, conn)
          return None
  
  #查询一条
  def selectone(self,sql='',param=()):
      # self.connected()
      try:
          # cur = self.db.cursor()
          cursor, conn=self.execute(sql,param)
          res = cursor.fetchone()
          self.close(cursor, conn)
          return res
      except Exception, e:
          print 'selectone except   ', e.args
          self.close(cursor, conn)
          return None
  
  #增加
  def insert(self,sql='',param=()):
      # self.connected()
      try:
          # self.db.getconn().execute(sql, param)
          cursor, conn=self.execute(sql,param)
          print '============'
          # _id=self.db.conn.insert_id()
          _id=cursor.lastrowid
          print '_id   ',_id
          conn.commit()
          self.close(cursor, conn)
          #防止表中没有id返回0
          if _id==0:
              return True
          return _id
      except Exception, e:
          print 'insert except   ', e.args
          conn.rollback()
          self.close(cursor, conn)
          # self.conn.rollback()
          return 0
  
  #增加多行
  def insertmany(self,sql='',param=()):
      # self.connected()
      cursor,conn=self.db.getconn()
      try:
          cursor.executemany(sql, param)
          # self.execute(sql,param)
          conn.commit()
          self.close(cursor, conn)
          return True
      except Exception, e:
          print 'insert many except   ', e.args
          conn.rollback()
          self.close(cursor, conn)
          # self.conn.rollback()
          return False
  
  #删除
  def delete(self,sql='',param=()):
      # self.connected()
      try:
          # cur = self.conn.cursor()
          # self.db.getconn().execute(sql, param)
          cursor,conn=self.execute(sql,param)
          # self.db.conn.commit()
          self.close(cursor, conn)
          return True
      except Exception, e:
          print 'delete except   ', e.args
          conn.rollback()
          self.close(cursor, conn)
          # self.conn.rollback()
          return False
  
  #更新
  def update(self,sql='',param=()):
      # self.connected()
      try:
          #cur = self.conn.cursor()
          # self.db.getconn().execute(sql, param)
          cursor,conn=self.execute(sql,param)
          # self.db.conn.commit()
          self.close(cursor, conn)
          return True
      except Exception, e:
          print 'update except   ',e.args
          conn.rollback()
          self.close(cursor, conn)
          # self.conn.rollback()
          return False
  
  @classmethod
  def getInstance(self):
      if MysqlHelp.mysql==None:
          MysqlHelp.mysql=MysqlHelp()
      return MysqlHelp.mysql
  
  #执行命令
  def execute(self,sql='',param=(),autoclose=False):
      cursor, conn = self.db.getconn()
      try:
          if param:
              cursor.execute(sql, param)
          else:
              cursor.execute(sql)
          conn.commit()
          if autoclose:
              self.close(cursor, conn)
      except Exception as e:
          pass
      return cursor, conn
  
  #执行多条命令
  '[{"sql":"xxx","param":"xx"}....]'
  def executemany(self,list=[]):
      cursor,conn=self.db.getconn()
      try:
          for order in list:
              sql=order['sql']
              param=order['param']
              if param:
                  cursor.execute(sql,param)
              else:
                  cursor.execute(sql)
          conn.commit()
          self.close(cursor, conn)
          return True
      except Exception as e:
          print 'execute failed========',e.args
          conn.rollback()
          self.close(cursor, conn)
          return False
  def connect(self):
      self.conn = MySQLdb.connect(user='root', db='asterisk', passwd='kalamodo', host='192.168.88.6')
  
  def close(self,cursor,conn):
      cursor.close()
      conn.close()
      print u"PT连接池释放con和cursor";
  ```

## 示例二

```python
# coding=utf-8
"""
使用DBUtils数据库连接池中的连接，操作数据库
OperationalError: (2006, ‘MySQL server has gone away’)
"""
import json
import pymysql
import datetime
from DBUtils.PooledDB import PooledDB
import pymysql


class MysqlClient(object):
    __pool = None;

    def __init__(self, 
      mincached=10, 
      maxcached=20, 
      maxshared=10, 
      maxconnections=200, 
      blocking=True,
      maxusage=100, 
      setsession=None, 
      reset=True,
      host='127.0.0.1', port=3306, db='test',
      user='root', 
      passwd='123456', 
      charset='utf8mb4'
      ):
        """
        :param mincached:连接池中空闲连接的初始数量
        :param maxcached:连接池中空闲连接的最大数量
        :param maxshared:共享连接的最大数量
        :param maxconnections:创建连接池的最大数量
        :param blocking:超过最大连接数量时候的表现，为True等待连接数量下降，为false直接报错处理
        :param maxusage:单个连接的最大重复使用次数
        :param setsession:optional list of SQL commands that may serve to prepare
            the session, e.g. ["set datestyle to ...", "set time zone ..."]
        :param reset:how connections should be reset when returned to the pool
            (False or None to rollback transcations started with begin(),
            True to always issue a rollback for safety's sake)
        :param host:数据库ip地址
        :param port:数据库端口
        :param db:库名
        :param user:用户名
        :param passwd:密码
        :param charset:字符编码
        """

        if not self.__pool:
            self.__class__.__pool = PooledDB(
              pymysql,
              mincached, 
              maxcached,
              maxshared, 
              maxconnections, 
              blocking,
              maxusage, 
              setsession, 
              reset,
              host=host, 
              port=port, 
              db=db,
              user=user, 
              passwd=passwd,
              charset=charset,
              cursorclass=pymysql.cursors.DictCursor
            )
        self._conn = None
        self._cursor = None
        self.__get_conn()

    def __get_conn(self):
        self._conn = self.__pool.connection();
        self._cursor = self._conn.cursor();

    def close(self):
        try:
            self._cursor.close()
            self._conn.close()
        except Exception as e:
            print e

    def __execute(self, sql, param=()):
        count = self._cursor.execute(sql, param)
        print count
        return count

    @staticmethod
    def __dict_datetime_obj_to_str(result_dict):
        """把字典里面的datatime对象转成字符串，使json转换不出错"""
        if result_dict:
            result_replace = {k: v.__str__() for k, v in result_dict.items() if isinstance(v, datetime.datetime)}
            result_dict.update(result_replace)
        return result_dict

    def select_one(self, sql, param=()):
        """查询单个结果"""
        count = self.__execute(sql, param)
        result = self._cursor.fetchone()
        """:type result:dict"""
        result = self.__dict_datetime_obj_to_str(result)
        return count, result

    def select_many(self, sql, param=()):
        """
        查询多个结果
        :param sql: qsl语句
        :param param: sql参数
        :return: 结果数量和查询结果集
        """
        count = self.__execute(sql, param)
        result = self._cursor.fetchall()
        """:type result:list"""
        [self.__dict_datetime_obj_to_str(row_dict) for row_dict in result]
        return count, result

    def execute(self, sql, param=()):
        count = self.__execute(sql, param)
        return count

    def begin(self):
        """开启事务"""
        self._conn.autocommit(0)

    def end(self, option='commit'):
        """结束事务"""
        if option == 'commit':
            self._conn.autocommit()
        else:
            self._conn.rollback()


if __name__ == "__main__":
    mc = MysqlClient()
    sql1 = 'SELECT * FROM shiji  WHERE  id = 1'
    result1 = mc.select_one(sql1)
    print json.dumps(result1[1], ensure_ascii=False)

    sql2 = 'SELECT * FROM shiji  WHERE  id IN (%s,%s,%s)'
    param = (2, 3, 4)
    print json.dumps(mc.select_many(sql2, param)[1], ensure_ascii=False)
```



参考与转载

https://www.cnblogs.com/huay/p/11562094.html
https://www.cnblogs.com/ydf0509/p/8972265.html
https://blog.csdn.net/daerzei/article/details/83865325
https://blog.csdn.net/u010939285/article/details/71088145
https://blog.csdn.net/weixin_43064185/article/details/89928645
