##  3.1.3 Documents instances

 要创建一个新的文档对象，请创建相关文档类的实例，为其字段提供值作为构造函数关键字参数。您可以为文档中的任何字段提供值： 

```python
>>> page = Page(title="Test Page")
>>> page.title
'Test Page'
```

 还可以使用标准对象属性语法将值分配给文档的字段 

```python
>>> page.title = "Example Page"
>>> page.title
'Example Page
```

### 二、保存和删除文件

 MongoEngine跟踪对文档的更改以提供有效的保存。要将文档保存到数据库，请调用[`save()`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document.save)方法。如果文档在数据库中不存在，则会创建该文档。如果已经存在，则所有更改都将自动更新。例如： 

```python
>>> page = Page(title="Test Page")
>>> page.save()  # Performs an insert
>>> page.title = "My Page"
>>> page.save()  # Performs an atomic set on the title field.
```

跟踪文档的更改，并总体上执行`set`操作。

- ```python
  list_field.push(0)    设置结果列表
  del(list_field)       取消整个列表
  ```

With列表更适合使用，`Doc.update(push__list_field=0)`因为这样可以阻止整个列表的更新-停止任何竞争条件。

### 三、级联保存

如果您的文档包含[`ReferenceField`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.fields.ReferenceField)或 [`GenericReferenceField`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.fields.GenericReferenceField)对象，则默认情况下该 [`save()`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document.save)方法将不会保存对那些对象的任何更改。如果您还希望保存所有引用，请注意每个保存是一个单独的查询，然后将`cascade`True传递给save方法将级联所有保存。

### 四、删除文件

要删除文档，请调用[`delete()`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document.delete)方法。请注意，这仅在文档存在于数据库中且具有有效时才起作用`id`。

## 文件编号

数据库中的每个文档都有唯一的ID。可以通过对象`id`上的属性进行访问 [`Document`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.Document)。通常，保存对象时，数据库服务器会自动生成ID，这意味着您仅`id`在保存文档后才能访问该字段：

```python
>>> page = Page(title="Test Page")
>>> page.id
>>> page.save()
>>> page.id
ObjectId('123456789abcdef000000000')
```

或者，您可以通过为`primary_key=True`字段的构造函数提供关键字参数来将自己的字段之一定义为文档的“主键” 。在幕后，MongoEngine将使用此字段作为 `id`; 实际上实际上`id`是别名为您的主键字段，因此您可以根据需要使用以下`id`方式访问主键：

```python
>>> class User(Document):
...     email = StringField(primary_key=True)
...     name = StringField()
...
>>> bob = User(email='bob@example.com', name='Bob')
>>> bob.save()
>>> bob.id == bob.email == 'bob@example.com'
True
```

您还可以使用`pk`字段访问文档的“主键” ，它是以下内容的别名`id`：

```python
>>> page = Page(title="Another Test Page")
>>> page.save()
>>> page.id == page.pk
True
```

注意

如果定义自己的主键字段，则该字段隐式成为必填字段，因此，[`ValidationError`](https://mongoengine-odm.readthedocs.io/apireference.html#mongoengine.ValidationError)如果不提供该字段，则将引发

