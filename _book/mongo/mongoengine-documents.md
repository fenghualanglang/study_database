## 3.3.2 模型架构

### 一、模型架构

```python
from mongoengine import *
import datetime

SIZE = (
    ('S', 'Small'),
    ('M', 'Medium'),
    ('L', 'Large'),
    ('XL', 'Extra Large'),
    ('XXL', 'Extra Extra Large')
)

def _not_empty(val):
    if not val:
        raise ValidationError('value can not be empty')

class Page(Document):
    title = StringField(max_length=200, required=True)
    date_modified = DateTimeField(default=datetime.datetime.utcnow)
    values = ListField(IntField(), default=list)
    values = ListField(IntField(), default=lambda: [1,2,3])
    values = ListField(IntField(), default=[1,2,3])
    size = StringField(max_length=3, choices=SIZE)
    name = StringField(validation=_not_empty)
```

### 二、字段类型

| 字段          | 说明     | 字段 | 说明 |
| ------------- | -------- | ------------- | ----------- |
| BinaryField | 二进制 | ImageField |图像|
| BooleanField |      | IntField ||
| ComplexDateTimeField | 复杂日期时间 | ListField            |列表|
| DateTimeField | 日期时间域 | LongField ||
| DecimalField |      | MapField             ||
| DictField |      | ObjectIdField |对象|
| DynamicField | [动态] | ReferenceField |参考|
| EmailField | 电子邮件 | LazyReferenceField |懒散参考|
| EmbeddedDocumentField | [嵌入文档] | SequenceField |序列|
| EmbeddedDocumentListField | [嵌入文档列表] | SortedListField |排序列表|
| EnumField | 枚举 | StringField |字符串|
| FileField | 文件 | URLField |URL|
| FloatField | 浮点数 | UUIDField ||
| GenericEmbeddedDocumentField | [通用嵌入文档] | PointField ||
| GenericReferenceField | [通用参考字段] | LineStringField |线串|
| GenericLazyReferenceField | 通用惰性引用字段 | PolygonField |多边形|
| GeoPointField | 地理点 | MultiPointField |多点|
| Geo Point Field | 图像 | MultiLineStringField |多行字符串|
|MultiPolygonField||||

### 三、字段参数

- 强制性：required=True  默认值：False

- 约束性：max_length
- 默认值：default
- 唯一性：unique
- 主键：primary_key， 如设置也可以通过pk字段访问
- 可选择迭代：choices    例如列表，元组或集合 
- 其他:unique_with
-  validation 可调用以验证字段的值。可调用对象将值作为参数，如果验证失败，则应引发ValidationError 

- **kwargs（可选的）    您可以提供其他元数据作为任意其他关键字参数。但是，您不能覆盖现有属性。常见选择包括help_text和verbose_name，它们通常由表单和窗口小部件库使用。 
### 四、字段类型
- List fields列表字段： 将另一个字段对象作为其第一个参数，它指定可以在列表中存储哪些类型的元素 
  ```python
	class Page(Document):
  	tags = ListField(StringField(max_length=50))
  ```

- 嵌入式文件Embedded documents

  可以将文档嵌入其他文档中。可以为这些嵌入式文档定义图式，就像为常规文档定义图式一样。要创建嵌入式文档，只需照常定义一个文档，但要继承自EmbeddedDocument而不是 

  ```python
  class Comment(EmbeddedDocument):
      content = StringField()
  ```

   要将文档嵌入另一个文档中，使用EmbeddedDocumentField字段类型，将嵌入式文档类作为第一个参数： 

  ```python
  class Page(Document):
      comments = ListField(EmbeddedDocumentField(Comment))
  
  comment1 = Comment(content='Good work!')
  comment2 = Comment(content='Nice article!')
  page = Page(comments=[comment1, comment2])
  ```

- Dictionary Fields

   通常，可以使用嵌入式文档代替字典-通常建议使用嵌入式文档，因为字典不支持验证或自定义字段类型。但是，有时您可能不知道要存储的内容的结构。在这种情况下，DictField是合适的： 

  ```python
  class SurveyResponse(Document):
      date = DateTimeField()
      user = ReferenceField(User)
      answers = DictField()
  
  survey_response = SurveyResponse(date=datetime.utcnow(), user=request.user)
  response_form = ResponseForm(request.POST)
  survey_response.answers = response_form.cleaned_data()
  survey_response.save()
  ```

   可以存储复杂的数据，其他字典，列表，对其他对象的引用，因此可以存储最灵活的字段类型 

- Reference fields

  引用可以使用来存储到数据库中的其他文档ReferenceField。将另一个文档类作为第一个参数传递给构造函数，然后只需将文档对象分配给该字段： 

  ```python
  class User(Document):
      name = StringField()
  
  class Page(Document):
      content = StringField()
      author = ReferenceField(User)
  
  john = User(name="John Smith")
  john.save()
  
  post = Page(content="Test Page")
  post.author = john
  post.save()
  ```

   该`User`对象将自动转换为幕后参考，并在`Page`检索到对象时取消引用。 

   要添加ReferenceField引用正在定义的文档的，请使用字符串self代替文档类作为ReferenceField构造函数的参数。要引用尚未定义的文档，请使用未定义文档的名称作为构造函数的参数：

  ```python
  class Employee(Document):
      name = StringField()
      boss = ReferenceField('self')
      profile_page = ReferenceField('ProfilePage')
  
  class ProfilePage(Document):
      content = StringField()
  ```

- ListFields一对多

  如果要通过引用列表实现一对多关系，则引用将存储为DBRef，要查询您需要将对象的实例传递给查询： 

  ```python
  class User(Document):
      name = StringField()
  
  class Page(Document):
      content = StringField()
      authors = ListField(ReferenceField(User))
  
  bob = User(name="Bob Jones").save()
  john = User(name="John Smith").save()
  
  Page(content="Test Page", authors=[bob, john]).save()
  Page(content="Another Page", authors=[john]).save()
  
  # 查找Bob编写的所有页面
  Page.objects(authors__in=[bob])
  
  # 找到鲍勃和约翰都写过的所有网页
  Page.objects(authors__all=[bob, john])
  
  # 将Bob从作者处删除一页
  Page.objects(id='...').update_one(pull__authors=bob)
  
  # 给作者加上约翰一页。
  Page.objects(id='...').update_one(push__authors=john)
  ```

- 处理引用文件的删除

   默认情况下，MongoDB不会检查数据的完整性，因此删除其他文档仍保留引用的文档将导致一致性问题。Mongoengine的`ReferenceField`功能增加了一些功能，可防止此类数据库完整性问题，并为每个引用提供了删除规则规范。通过`reverse_delete_rule`在`ReferenceField`定义中提供属性 来指定删除规则，如下所示： 

  ```python
  class ProfilePage(Document):
      ...
      employee = ReferenceField('Employee', reverse_delete_rule=mongoengine.CASCADE)
  ```

  在此示例中的声明意味着，当`Employee`删除一个对象时`ProfilePage`，引用该雇员的对象也将被删除。如果删除了整批员工，则所有链接的个人资料页也会被删除。 

  其值可以采用以下任何常量：

  - `mongoengine.DO_NOTHING`

    这是默认设置，不会执行任何操作。删除速度很快，但是可能导致数据库不一致或引用悬挂。

  - `mongoengine.DENY`

    如果仍然存在要删除的对象的引用，则拒绝删除。

  - `mongoengine.NULLIFY`

    任何仍引用要删除的对象的对象字段都设置为None（使用MongoDB的“ unset”操作），从而有效地消除了关系。

  - `mongoengine.CASCADE`

    首先删除包含引用要删除的对象的字段的任何对象。

  - `mongoengine.PULL`

    从[`ListField`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.fields.ListField)（[`ReferenceField`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.fields.ReferenceField)）的任何对象字段中删除对对象的引用（使用MongoDB的“拉”操作 ）。

- Generic reference fields

   还存在第二种参考字段 [`GenericReferenceField`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.fields.GenericReferenceField)。这允许您引用任何类型的[`Document`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document)，因此不会将 [`Document`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document)子类用作构造函数参数： 

  ```python
  class Link(Document):
      url = StringField()
  
  class Post(Document):
      title = StringField()
  
  class Bookmark(Document):
      bookmark_object = GenericReferenceField()
  
  link = Link(url='http://hmarr.com/mongoengine/')
  link.save()
  
  post = Post(title='Using MongoEngine')
  post.save()
  
  Bookmark(bookmark_object=link).save()
  Bookmark(bookmark_object=post).save()
  ```

  使用[`GenericReferenceField`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.fields.GenericReferenceField)s的效率略低于标准[`ReferenceField`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.fields.ReferenceField)s，因此，如果您仅引用一种文档类型，则首选标准 [`ReferenceField`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.fields.ReferenceField)。 

- 唯一性约束

  MongoEngine允许您通过提供`unique=True`给`Field`的构造函数来指定字段在整个集合中应该是唯一的。如果您尝试保存一个唯一字段具有与数据库中已有文档相同值的文档， `NotUniqueError`则会引发a。您还可以使用来指定多字段唯一性约束`unique_with`，该约束可以是单个字段名称，也可以是字段名称的列表或元组： 

  ```python
  class User(Document):
      username = StringField(unique=True)
      first_name = StringField()
      last_name = StringField(unique_with='first_name')
  ```

- Document collections 文件集合

   **直接**继承自的文档类[`Document`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document) 将在数据库中拥有自己的**集合**。集合的名称默认情况下是类的名称，转换为小写形式（因此在上面的示例中，集合称为page）。如果您需要更改集合的名称（例如，将MongoEngine与现有数据库一起使用），请创建`meta`在文档上调用的类字典属性，并将其设置`collection`为您希望文档类使用的集合的名称： 

  ```python
  class Page(Document):
      title = StringField(max_length=200, required=True)
      meta = {'collection': 'cmsPage'}
  ```

- Capped collections 上限集合

   一个[`Document`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document)可使用**加盖收集**指定 `max_documents`并`max_size`在`meta`字典中。 `max_documents`是允许在集合中存储的最大文档数，并且`max_size`是集合的最大大小（以字节为单位）。`max_size`由MongoDB内部和mongoengine向上舍入到256的下一个整数倍。也请使用256的倍数以避免混淆。如果`max_size`未指定， `max_documents`则`max_size`默认为10485760字节（10MB）。下面的示例显示一个`Log`文档，该文档将被限制为1000个条目和2MB的磁盘空间： 

  ```python
  class Log(Document):
      ip_address = StringField()
      meta = {'max_documents': 1000, 'max_size': 2000000}
  ```

- ordering 

   您可以[`QuerySet`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.queryset.QuerySet)使用的`ordering`属性 为您指定默认顺序 `meta`。排序会在[`QuerySet`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.queryset.QuerySet)创建时应用 ，并且可以通过后续调用来覆盖[`order_by()`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.queryset.QuerySet.order_by)。 

  ```python
  from datetime import datetime
  
  class BlogPost(Document):
      #location = PointField(auto_index=False)
      title = StringField()
      published_date = DateTimeField()
  
      meta = {
          'ordering': ['-published_date']
      }
  
  blog_post_1 = BlogPost(title="Blog Post #1")
  blog_post_1.published_date = datetime(2010, 1, 5, 0, 0 ,0)
  
  blog_post_2 = BlogPost(title="Blog Post #2")
  blog_post_2.published_date = datetime(2010, 1, 6, 0, 0 ,0)
  
  blog_post_3 = BlogPost(title="Blog Post #3")
  blog_post_3.published_date = datetime(2010, 1, 7, 0, 0 ,0)
  
  blog_post_1.save()
  blog_post_2.save()
  blog_post_3.save()
  
  # get the "first" BlogPost using default ordering
  # from BlogPost.meta.ordering
  latest_post = BlogPost.objects.first()
  assert latest_post.title == "Blog Post #3"
  
  # override default ordering, order BlogPosts by "published_date"
  first_post = BlogPost.objects.order_by("+published_date").first()
  assert first_post.title == "Blog Post #1
  ```

- 文件继承

  要创建[`Document`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document)您定义的专用类型，可以将其子类化，并添加您可能需要的任何其他字段或方法。由于此新类不是的直接子类 [`Document`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document)，因此不会存储在其自己的集合中。它将使用与其超类相同的集合。这样可以更方便，有效地检索相关文档–您需要做的所有事情都`allow_inheritance`在`meta`文档数据中设置为True 。

  ```python
  # Stored in a collection named 'page'
  class Page(Document):
      title = StringField(max_length=200, required=True)
      meta = {'allow_inheritance': True}
  
  # Also stored in the collection named 'page'
  class DatedPage(Page):
      date = DateTimeField()
  ```

  ```python
  Page(title='a funky title').save()
  DatedPage(title='another title', date=datetime.utcnow()).save()
  
  print(Page.objects().count())         # 2
  print(DatedPage.objects().count())    # 1
  
  # print documents in their native form
  # we remove 'id' to avoid polluting the output with unnecessary detail
  qs = Page.objects.exclude('id').as_pymongo()
  print(list(qs))
  ```