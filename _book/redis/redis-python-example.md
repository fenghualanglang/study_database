
### 2.3.8 python操作redis之应用示例

```python
import redis
from rediscluster import RedisCluster

from config import (
    REDIS_HOST,
    REDIS_PORT,
    REDIS_DB,
    REDIS_PASS,
    REDIS_CLUSTER
)


class RedisDB(object):

    def __init__(self):
        
        self._pool = redis.ConnectionPool(host=REDIS_HOST, port=REDIS_PORT, db=REDIS_DB, password=REDIS_PASS)
        self.conn = redis.Redis(connection_pool=self._pool)

    # ============redis list string=======================
    def set(self, key, value, expire=None):
        self.conn.set(key, value, expire)

    def get(self, key):
        return self.conn.get(key)

    def setex(self, key, expire_time, strval):
        return self.conn.setex(key, expire_time, strval)

    def expire(self, name, time):
        return self.conn.expire(name, time)

    def delete(self, name):
        self.conn.delete(name)

    # ============redis list hash==============================
    def hdel(self, name, key):
        return self.conn.hdel(name, key)

    def hset(self, name, key, value):
        self.conn.hset(name, key, value)

    def hget(self, key, fields):
        return self.conn.hget(key, fields)

    def hmset(self, key, fields):
        self.conn.hmset(key, fields)

    def hgetall(self, name):
        data = self.conn.hgetall(name)
        data = dict((item[0].decode('utf-8'), item[1].decode('utf-8')) for item in data.items()) if data else None
        return data

    def hvals(self, name):
        return self.conn.hvals(name)

    def hkeys(self, key):
        return self.conn.hkeys(key)

    def exists(self, key):
        return self.conn.exists(key)

    def hexists(self, name, key):
        return self.conn.hexists(name, key)

    # ============redis list queue======================
    def qsize(self, name):
        return self.conn.llen(name)  # 返回队列里面list内元素的数量

    def put(self, name, values):
        self.conn.rpush(name, values)  # 添加新元素到队列最右方

    def get_wait(self, keys, timeout=None):
        # 返回队列第一个元素，如果为空则等待至有元素被加入队列（超时时间阈值为timeout，如果为None则一直等待）
        item = self.conn.blpop(keys, timeout=timeout)
        # if item:
        #     item = item[1]  # 返回值为一个tuple
        return item

    def get_nowait(self, name):
        # 直接返回队列第一个元素，如果队列为空返回的是None
        item = self.conn.lpop(name)
        return item

    # ============redis set =======================
    def sismember(self, name, value):
        item = self.conn.sismember(name, value)
        return item


my_redis = RedisDB()
```








