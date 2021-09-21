
#### 1.3.1 peewee-model

#### 一、字段类型

<table border="1" align="center" bordercolor="#4A766E" cellpadding="0" cellspacing="0">    <tr >        <th class="head" width="100">字段类型</th>        <th class="head" width="100">Sqlite</th>        <th class="head" width="100">Postgresql</th>        <th class="head" width="100">MySQL</th>    </tr>    <tr>        <td><code >AutoField 自增</code></td>        <td><code>integer</code></td>        <td><code>serial</code></td>        <td><code>integer</code></td>    </tr>    <tr >        <td><code >BigAutoField 自增</code></td>        <td><code>integer</code></td>        <td><code>bigserial</code></td>        <td><code>bigint</code></td>    </tr>    <tr>        <td><code >IntegerField 整数</code></td>        <td><code>integer</code></td>        <td><code>integer</code></td>        <td><code>integer</code></td>    </tr>    <tr >        <td><code>BigIntegerField</code></td>        <td><code>integer</code></td>        <td><code>bigint</code></td>        <td><code>bigint</code></td>    </tr>    <tr>        <td><code>SmallIntegerField</code></td>        <td><code>integer</code></td>        <td><code>smallint</code></td>        <td><code>smallint</code></td>    </tr>    <tr >        <td><code >IdentityField</code></td>        <td><code>not supported</code></td>        <td><code>int identity</code></td>        <td><code>not supported</code></td>    </tr>    <tr>        <td><code >FloatField 浮点型</code></td>        <td><code>real</code></td>        <td><code>real</code></td>        <td><code>real</code></td>    </tr>    <tr >        <td><code >DoubleField 双精度</code></td>        <td><code>real</code></td>        <td><code>double precision</code></td>        <td><code>double precision</code></td>    </tr>    <tr>        <td><code >DecimalField 小数</code></td>        <td><code>decimal</code></td>        <td><code>numeric</code></td>        <td><code>numeric</code></td>    </tr>    <tr >        <td><code >CharField 字符</code></td>        <td><code>varchar</code></td>        <td><code>varchar</code></td>        <td><code>varchar</code></td>    </tr>    <tr>        <td><code >FixedCharField</code></td>        <td><code>char</code></td>        <td><code>char</code></td>        <td><code>char</code></td>    </tr>    <tr >        <td><code >TextField 文本</code></td>        <td><code>text</code></td>        <td><code>text</code></td>        <td><code>text</code></td>    </tr>    <tr>        <td><code >BlobField</code></td>        <td><code>blob</code></td>        <td><code>bytea</code></td>        <td><code>blob</code></td>    </tr>    <tr >        <td><code >BitField</code></td>        <td><code>integer</code></td>        <td><code>bigint</code></td>        <td><code>bigint</code></td>    </tr>    <tr>        <td><code >BigBitField</code></td>        <td><code>blob</code></td>        <td><code>bytea</code></td>        <td><code>blob</code></td>    </tr>    <tr >        <td><code >UUIDField uid</code></td>        <td><code>text</code></td>        <td><code>uuid</code></td>        <td><code>varchar(40)</code></td>    </tr>    <tr>        <td><code >BinaryUUIDField 二进制uuid</code></td>        <td><code>blob</code></td>        <td><code>bytea</code></td>        <td><code>varbinary(16)</code></td>    </tr>    <tr >        <td><code >DateTimeField 日期时间</code></td>        <td><code>datetime</code></td>        <td><code>timestamp</code></td>        <td><code>datetime</code></td>    </tr>    <tr>        <td><code >DateField 日期</code></td>        <td><code>date</code></td>        <td><code>date</code></td>        <td><code>date</code></td>    </tr>    <tr >        <td><code >TimeField 时间</code></td>        <td><code>time</code></td>        <td><code>time</code></td>        <td><code>time</code></td>    </tr>    <tr>        <td><code >TimestampField 时间戳</code></td>        <td><code>integer</code></td>        <td><code>integer</code></td>        <td><code>integer</code></td>    </tr>    <tr >        <td><code >IPField</code></td>        <td><code>integer</code></td>        <td><code>bigint</code></td>        <td><code>bigint</code></td>    </tr>    <tr>        <td><code >BooleanField 布尔</code></td>        <td><code>integer</code></td>        <td><code>boolean</code></td>        <td><code>bool</code></td>    </tr>    <tr >        <td><code >BareField</code></td>        <td><code>untyped</code></td>        <td><code>not supported</code></td>        <td><code>not supported</code></td>    </tr>    <tr>        <td><code>ForeignKeyField 外键</code></td>        <td><code>integer</code></td>        <td><code>integer</code></td>        <td><code>integer</code></td>    </tr></table>
#### 二、字段初始化参数(字段类型都接受的参数及其默认值)
- `null = False` –允许空值
- `index = False` –在此列上创建索引
- `unique = False`–在此列上创建唯一索引。另请参阅[添加复合索引](http://docs.peewee-orm.com/en/latest/peewee/models.html#model-indexes)。
- `column_name = None` –在数据库中明确指定列名称。
- `default = None` –任何值或可调用以用作未初始化模型的默认值
- `primary_key = False` –表的主键
- `constraints = None` -一个或多个约束，例如 `[Check('price > 0')]`
- `sequence = None` –序列名称（如果后端支持）
- `collation = None` –用于排序字段/索引的排序规则
- `unindexed = False`–指示虚拟表上的字段应未索引（**仅SQLite**）
- `choices = None`–可选的可迭代，包含2个元组`value`，`display`
- `help_text = None` –表示该字段任何有用文本的字符串
- `verbose_name = None` –表示该字段“用户友好”名称的字符串
- `index_type = None`–指定自定义索引类型，例如，对于Postgres，您可以指定一个`'BRIN'`或`'GIN'`索引。

#### 三、一些字段带有特殊参数
<table border="1" align="center" bordercolor="#4A766E" cellpadding="0" cellspacing="0"><tr><td><a href="http://docs.peewee-orm.com/en/latest/peewee/api.html#CharField"><code        class="xref py py-class docutils literal notranslate">CharField</code></a></td>     <td><code>max_length</code></td> </tr>    <tr class="row-odd"><td><a href="http://docs.peewee-orm.com/en/latest/peewee/api.html#FixedCharField"><code>FixedCharField</code></a></td><td><code>max_length</code></td></tr><tr><td><a href="http://docs.peewee-orm.com/en/latest/peewee/api.html#DateTimeField"><code>DateTimeField</code></a></td><td><code>formats</code></td></tr><tr class="row-odd"><td><a href="http://docs.peewee-orm.com/en/latest/peewee/api.html#DateField"><code>DateField</code></a></td><td><code>formats</code></td></tr><tr><td><a href="http://docs.peewee-orm.com/en/latest/peewee/api.html#TimeField"><code>TimeField</code></a></td><td><code>formats</code></td></tr>    <tr class="row-odd"><td><a href="http://docs.peewee-orm.com/en/latest/peewee/api.html#TimestampField"><code>TimestampField</code></a></td><td><code class="docutils literal notranslate">resolution</code>, <code>utc</code></td></tr><tr><td><a href="http://docs.peewee-orm.com/en/latest/peewee/api.html#DecimalField"><code>DecimalField</code></a></td><td><code class="docutils literal notranslate">max_digits</code>, <code>decimal_places</code>,<code>auto_round</code>, <code>rounding</code></td></tr><tr class="row-odd"><td><a href="http://docs.peewee-orm.com/en/latest/peewee/api.html#ForeignKeyField"><code>ForeignKeyField</code></a></td><td><code>model</code>, <code>field</code>, <code>backref</code>,<code>on_delete</code>, <code>on_update</code>, <code>deferrable</code><code>lazy_load</code></td></tr><tr><td><a href="http://docs.peewee-orm.com/en/latest/peewee/api.html#BareField"><code>BareField</code></a></td><td><code>adapt</code></td></tr></table>
#### 四、根据模型生成数据表

1. 方法

   ```cpp
   database.create_tables([User, UserAuth])  有其他的model往列表加即可
   database.close()
   12
   ```

2. 方法

   ```cpp
   myModels = [User, UserAuth]
   for myModel in myModels:
       myModel.create_table() 
   ```

#### 五、根据数据表生成模型

如果不需要自己去创建表，已有现成的表可用，可以使用下面的命令生成模型

```
python -m pwiz -e mysql -H 127.0.0.1 -p 3306 -u root -P dbname >model.py
python -m pwiz -e mysql -H {主机地址} -p 3306  -u root -- password   {数据库名称} > {生成的代码文件 例model.py}

```
- 解释：

| 选项 | 解释                    | 举例                         |
| ---- | ----------------------- | ---------------------------- |
| -h   | help 显示帮助           | -h                           |
| -e   | engine 数据库引擎       | -e mysql                     |
| -H   | host 数据库地址         | -H localhost                 |
| -p   | port 数据库端口         | -p 3306                      |
| -u   | user 数据库用户名       | -u root                      |
| -P   | password 数据库密码     | -P                           |
| -s   | schema 模式             | -s public                    |
| -t   | tables 指定生成来自表格 | -t tweet,users,relationships |
| -v   | VIEWs 指定生成来自视图  | -v                           |
| -i   | info 添加原信息         | -i                           |
| -o   | order 保留表格列顺序    | -o                           |

  ```shell
  -e数据库类型 比如mysql
  -H数据库ip
  -p端口
  -u数据库用户名
  -P密码
  dbname为db名
  db.py自动生成的py文件名
  ```
  
- 其它

  ```python
  from peewee import *
  from bcrypt import hashpw, gensalt
  from MxForm.models import BaseModel

  class PasswordHash(bytes):
      def check_password(self, password):
          password = password.encode('utf-8')
          return hashpw(password, self) == self

  class PasswordField(BlobField):
      def __init__(self, iterations=12, *args, **kwargs):
          if None in (hashpw, gensalt):
              raise ValueError('Missing library required for PasswordField: bcrypt')
          self.bcrypt_iterations = iterations
          self.raw_password = None
          super(PasswordField, self).__init__(*args, **kwargs)

  def db_value(self, value):
      """Convert the python value for storage in the database."""
      if isinstance(value, PasswordHash):
          return bytes(value)

      if isinstance(value, str):
          value = value.encode('utf-8')
      salt = gensalt(self.bcrypt_iterations)
      return value if value is None else hashpw(value, salt)

  def python_value(self, value):
      """Convert the database value to a pythonic value."""
      if isinstance(value, str):
          value = value.encode('utf-8')

      return PasswordHash(value)
  ```
  ```python
  GENDERS = (
      ("female", "女"),
      ("male", "男")
  )

  class User(BaseModel):
      mobile = CharField(max_length=11, verbose_name="手机号码", index=True, unique=True)
      password = PasswordField(verbose_name="密码") #1. 密文，2.不可反解
      nick_name = CharField(max_length=20, null=True, verbose_name="昵称")
      head_url = CharField(max_length=200, null=True, verbose_name="头像")
      address = CharField(max_length=200, null=True, verbose_name="地址")
      desc = TextField(null=True, verbose_name="个人简介")
      gender = CharField(max_length=200, choices=GENDERS, null=True, verbose_name="地址")
  ```
















