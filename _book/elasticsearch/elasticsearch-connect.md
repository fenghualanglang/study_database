- 创建index索引

  ```python
  创建索引my-index
  es.indices.create(index='my-index')
  
  索引my-index,如果已经存在，就返回个400，
  es.indices.create(index='my-index',ignore=[400])
  ```

- 插入数据<font color='red'>     es.get(index='index_name', doc_type='tpye_name', id='id_value') </font>

  ```python
  es.index(
      index="my-index",
      doc_type="test-type",
      id=1,
      body={"unsername": "python", "sex":1, "":18, "timestamp":datetime.now()}
  )
  ```

-  get查询获取数据 

  ```python
  es.get(index="my-index", doc_type="test-type", id=1)
  ```

- search 查询获取数据 

  ```python
  es.search(index="my-index", doc_type="test-type")
  ```

-  search 查询所有数据 <font color='red'>match_all</font>

  ```python
  es.search(index="my-index", doc_type="test-type")
  ```

  ```python
  body = {"query": {"match_all": {}}}
  es.search(index="my-index", doc_type="test-type", body=body)
  ```

- search 条件查询数据 <font color='red'>term</font>

  <div class="click_reveal">
      <span>搜索name="python" 的数据</span>
      <div>
          <pre><code>搜索name="python" 的数据
  body = {
      "query": {
          "term": {
              "username":"python"
          }
      }
  }
  </code></pre></div></div>

- search 条件查询数据 <font color='red'>terms</font>

  <div class="click_reveal">
      <span>搜索username="python"或username="java" 的数据</span>
      <div>
          <pre><code>搜索username="python"或username="java" 的数据
  body = {
      "query": {
          "terms": {
              "username": ["python", "java"]
          }
      }
  }
  </code></pre></div></div>

- search 条件查询数据 <font color='red'> match </font>

  <font color='red'> match  </font>： 关键字匹配的数据(匹配库中单个field) ;  分词后结果 python 与 python1 为两个不同的词

  <div class="click_reveal">
      <span>查询username 包含 python 的数据</span>
      <div>
          <pre><code>查询username 包含 python 的数据
  body = {
      "query": {
          "match": {
              "username": "python"
          }
      }
  }
  </code></pre></div></div>

- search 条件查询数据 <font color='red'> multi_match </font>

  <font color='red'>multi_match </font>关键字匹配的数据(匹配库中多个field)  ; 

  <div class="click_reveal">
      <span>查询username 和 addr 包含"python"关键字的数据 </span>
      <div>
          <pre><code><font color='red'>查询 username 和 addr 包含"python"关键字的数据</font>
  body = {
      "query": {
          "multi_match": {
              "query": "python",
              "fields": ["username", "addr"]
          }
      }
  }
  </code></pre></div></div>

- search 条件查询 <font color='red'> ids</font>

  <div class="click_reveal">
      <span>查询 id为1 或 2d 的数据</span>
      <div>
          <pre><code>查询 id为1 或 2d 的数据
  body = {
      "query": {
          "ids": {
              "type": "test-type",
              "values": [1, 2]
          }
      }
  }
  </code></pre></div></div>

-  search 复合查询  <font color='red'>bool</font>
  bool有3类查询关系   must(都满足)    should(其中一个满足)  must_not(都不满足) 

  <div class="click_reveal">
      <span>查询 username="python" and "age"=18 的数据</span>
      <div>
          <pre><code>查询 username="python" and "age"=18 的数据
  body = {
      "query": {
          "bool": {
              "must": [
                  {
                      "term": {"username": "python"}
                  },
                  {
                      "term": {"age": 18}
                  }
              ]
          }
      }
  }
  </code></pre></div></div>

- search 范围查询 <font color='red'>range</font>

  <div class="click_reveal">
      <span>查询age大于18且age小于30 的数据</span>
      <div>
          <pre><code>查询age大于18且age小于30 的数据
  body = {
      "query": {
          "range": {
              "age": {
                  "gte": 18,
                  "lte": 30
              }
          }
      }
  }
  </code></pre></div></div>

-  search 排序   <font color='red'>sort</font>

  ```python
  根据某个字段升序排序    "order": "asc"   asc升序，desc降序
  ```

  <div class="click_reveal">
      <span>根据age字段升序排序</span>
      <div>
          <pre><code>根据age字段升序排序
  body = {
      "query": {
          "match_all": {}
      },
      "sort": {
          "age": {"order": "asc"}
      }
  }
  </code></pre></div></div>

-  count 执行查询并获取该查询的匹配数 

  ```python
  es.count(index="my-index", doc_type="test-type")
  ```

  ```python
  es.count(index="my-index", doc_type="test-type", body=body)
  ```

-  获取最值  aggs 

  <div class="click_reveal">
      <span>搜索所有数据，并获取age最大的值</span>
      <div>
          <pre><code>搜索所有数据，并获取age最大的值
  body = {
      "query": {
          "match_all": {}
      },
      "aggs": {                             # 聚合查询
          "max_age": {                      # 最大值的key
              "max": {"field": "age"}       # 最大  查询"age"的最大值
          }
      }
  }
  </code></pre></div></div>

- 获取和

  <div class="click_reveal">
      <span>搜索所有数据，并获取age和</span>
      <div>
          <pre><code>搜索所有数据，并获取age和
  body = {
      "query": {
          "match_all": {}
      },
      "aggs": {                             # 聚合查询
          "sum_age": {                      # 和的key
              "sum": {"field": "age"}       # 和  获取所有age的和
          }
      }
  }
  </code></pre></div></div>

- 获取平均值

  <div class="click_reveal">
      <span>搜索所有数据，并获取age平均值</span>
      <div>
          <pre><code>搜索所有数据，并获取age平均值
  body = {
      "query": {
          "match_all": {}
      },
      "aggs": {                             # 聚合查询
          "avg_age": {                      # 平均的key
              "sum": {"field": "age"}       # 平均值  获取所有age的平均值
          }
      }
  }
  </code></pre></div></div>

-  filter_path  响应过滤 

  ```ppython
  只需要获取_id数据,多个条件用逗号隔开
  es.search(index="my-index", doc_type="test-type", filter_path=["hits.hits._id"])
  ```

  ```python
  获取所有数据
  es.search(index="my-index", doc_type="test-type", filter_path=["hits.hits._*"])
  ```

-  通配符查询 

  <div class="click_reveal">
      <span>查询name以id为后缀的所有数据</span>
      <div>
          <pre><code>查询name以id为后缀的所有数据
  body = {
      "query": {
          "wildcard": {
              "name": "*id"
          }
      }
  }
  </code></pre></div></div>

-  前缀查询 

  <div class="click_reveal">
      <span>查询前缀为"赵"的所有数据</span>
      <div>
          <pre><code>查询前缀为"赵"的所有数据
  body = {
      "query": {
          "prefix": {
              "username": "p"
          }
      }
  }
  </code></pre></div></div>

-  切片式查询 

  <div class="click_reveal">
      <span>从第2条数据开始，获取4条数据</span>
      <div>
          <pre><code>从第2条数据开始，获取4条数据
  body = {
      "query": {
          "match_all": {}
      },
      "from": 2,  从第二条数据开始
      "size": 4   获取4条数据
  }
  </code></pre></div></div>

  

- 删除数据  <font color='red'> delete：删除指定index、type、id的文档 </font>

  ```python
  es.delete(index="my-index", doc_type="test-type", id=1)
  es.delete(index="my-index", doc_type="test-type", id=1, ignore=[404])  不存在则404 可忽略
  ```

-  条件删除 

  ```python
  delete_by_query：删除满足条件的所有数据，查询条件必须符合DLS格式
  
  query = {'query': {'match': {'sex': 'famale'}}}  # 删除性别为女性的所有文档
  query = {'query': {'range': {'age': {'lt': 11}}}}# 删除年龄小于11的所有文档
  
  es.delete_by_query(index='indexName', body=query, doc_type='typeName')
  ```

- 条件更新 

  ```python
  update_by_query：更新满足条件的所有数据，写法同上删除和查询
  ```

- 批量写入、删除、更新 bulk

- 如果想存在即更新, 不存在则更新，加一个 doc_as_upsert 参数就好了

  ```
  datas = {
  	"_index": f"online_complex_test", 
  	"_op_type": "update", 
  	"_type": f"complex_city", 
  	"_id": id, 
  	'doc': doc, 
  	"doc_as_upsert": True
  }
  ```

- ​	基础操作

  ```python
  import pymongo, json
  from elasticsearch import Elasticsearch
  from elasticsearch import helpers
  import math
  
  # mapping 定义你的es字段
  doc_mapping = {
      'properties': {
          "other_id": {"type": "long"},
          "city": {"type": "keyword"},
          "id": {"type": "keyword"},
      }
  }
  
  # 创建ES连接
  es_conn_test = Elasticsearch(["127.0.0.1:6666"], maxsize=25)
  
  # es的基础配置,  index_name, alias(别名可以创建多个), doc_type(表名)
  index_name = f'complex_test'
  online_alias = f'online_complex_test'
  type_name = f'complex_city'
  
  es_conn_test.indices.create(index=index_name, ignore=400)
  es_conn_test.indices.put_mapping(index=index_name, doc_type=type_name, body=doc_mapping)
  es_conn_test.indices.put_alias(index_name, online_alias)  # 别名
  
  # 获取mapping
  res = es_conn_test.indices.get_mapping(index=index_name)
  print(json.dumps(res))
  
  # es查询
  res = es_conn_test.search(index=index_name, doc_type=type_name, body={})
  print(json.dumps(res))
  
  # 删除索引
  res = es_conn_test.indices.delete(index=index_name)
  print(res)
  ```

