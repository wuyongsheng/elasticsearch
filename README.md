---

最近在做一个企业信用信息大数据的项目，系统的架构大概是这样的：使用elasticsearch对用户输入的搜索关键字进行检索，并在网页上以列表的方式展示检索到结果，当用户点击列表中的检索结果记录后，会跳转到企业信用详情信息页面，企业信用详情信息是从MongoDB里面读取的。

之前没怎么接触过elasticsearch，于是就想自己搭建一个elasticsearch的环境，学习一下elasticsearch的相关操作。

在网上找了一些elasticsearch的学习资料，发现有个叫做 Elasticsearch: 权威指南 的网站非常好，从环境的安装到实例的讲解都非常详细，适合初学者，我已经把从第一章到聚合部分的内容都看完了，网站地址：

https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html

![elasticsearch权威指南.jpg](https://upload-images.jianshu.io/upload_images/12273007-b2d3983c07813ede.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

###  elasticsearch及kibana的安装和配置

kibana和elasticsearch通常是配套使用的，可以通过kibana在网页上操作elasticsearch。

相关的安装过程，这里就不介绍了，Elasticsearch: 权威指南网站以及百度上相关的资料非常多，安装的过程也不复杂。

需要注意的是，kibana的版本号不能高于elasticsearch的版本号，否则在使用的时候会有问题。我自己安装的elasticsearch和kibana的版本号都是5.6.14.

elasticsearch 安装包 Windows 64位的下载地址：
https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.6.14.msi

kibana 安装包 Windows 64位的下载地址：
https://artifacts.elastic.co/downloads/kibana/kibana-5.6.14-windows-x86.zip

elasticsearch安装并启动后，在浏览器中访问http://localhost:9200/，出现如下图所示内容表明Elasticsearch启动成功。

![elasticsearch安装.jpg](https://upload-images.jianshu.io/upload_images/12273007-5dd9fa3b459fe6e2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

kibana安装完成后，在浏览器中输入 http://localhost:5601/
即可访问kibana，界面如下：

![kibana.jpg](https://upload-images.jianshu.io/upload_images/12273007-d7a47759d5a3fe1b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###  配置索引模式

在使用使用 kibana之前，会提示你设置索引模式(Index Patterns)，点击 kibana 左侧导航栏的 Management 菜单，在 Management 页面，点击 Index Patterns 链接，会进入索引模式设置页面，如下图所示：

![索引模式.jpg](https://upload-images.jianshu.io/upload_images/12273007-0e7671785fb84d0e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在索引模式设置页面，点击页面左上角的“ Create Index Pattern”按钮，即可设置索引模式，在下图的输入框中输入索引名称及“*”（通配符），注意：输入框中的索引名称要是系统中已存在的。接着，点击 “create” 按钮即可创建索引模式。 

![配置索引名称.jpg](https://upload-images.jianshu.io/upload_images/12273007-eb8d0feee1c5f9a3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

索引模式创建成功后，点击左侧导航栏的 “Discover” 菜单，进入 “Discover” 页面后，点击左侧的下拉框，会显示上面我们已经配置好的索引模式，选择任意一个索引模式，页面就会显示该索引模式下的记录，在页面上方的搜索框中输入关键字即可对该索引模式下的记录进行搜索，如下图所示：

![discover页面.jpg](https://upload-images.jianshu.io/upload_images/12273007-572da6c9e269b683.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

索引模式配置好了之后，我们就可以使用 kibana 来操作 elasticsearch 了。

具体的操作如下：点击 kibana 左侧导航栏的 “Dev Tools” 菜单，会进入“Console” 页面，输入查询的 DSL ，并点击右侧的三角图标，即可进行elasticsearch查询操作，同时右边栏会显示查询，如下图：

![DSL.jpg](https://upload-images.jianshu.io/upload_images/12273007-f19b082b49697559.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###  相关概念介绍

- Node 与 Cluster

Elastic 是一个分布式数据库，允许多台服务器协同工作，每台服务器可以运行多个 Elastic 实例。

单个 Elastic 实例称为一个节点（node）。一组节点构成一个集群（cluster）。

- Index

一个索引类似于传统关系数据库中的一个数据库，是一个存储关系型文档的地方。索引(index) 的复数词为 indices 或 indexes。

在console中输入
```
“GET _cat/indices?v” 
```
可查询当前节点的所有 Index，返回结果如下：

```
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   blogs-en 9RqPRx3KS9q2Qq9lda--Sg   5   1          0            0       810b           810b
yellow open   cars     DDoss65BSHSEmzr3hNwOfw   5   1         24            0     45.4kb         45.4kb
yellow open   megacorp n1JhO0IcSVuJKHpi2i1Eog   5   1          3            0     17.1kb         17.1kb
yellow open   .kibana  6xGAaxHSSRa0erZeEhNvkQ   1   1          3            0     10.9kb         10.9kb
yellow open   blogs-fr t6g1t2rPSIeY7EHNv-EPZA   5   1          0            0       810b           810b
yellow open   my_store U50-KyE0R_-9nFvEwYFGQA   5   1          4            0     11.5kb         11.5kb
yellow open   my_index 9QQS512qRq-nPLaNtlLkXg   5   1          2            0      7.2kb          7.2kb
yellow open   movies   97Hhrys5QCi0AAiBCJKl6A   5   1          0            0       810b           810b

```

-   Document


Index 里面单条的记录称为 Document（文档）。许多条 Document 构成了一个 Index。

Document 使用 JSON 格式表示，下面是一个例子。

```
{
  "user": "张三",
  "title": "工程师",
  "desc": "数据库管理"
}
```
同一个 Index 里面的 Document，不要求有相同的结构（scheme），但是最好保持相同，这样有利于提高搜索效率。

-  Type


Document 可以分组，比如weather这个 Index 里面，可以按城市分组（北京和上海），也可以按气候分组（晴天和雨天）。这种分组就叫做 Type，它是虚拟的逻辑分组，用来过滤 Document。

不同的 Type 应该有相似的结构（schema），举例来说，id字段不能在这个组是字符串，在另一个组是数值。这是与关系型数据库的表的一个区别。性质完全不同的数据（比如products和logs）应该存成两个 Index，而不是一个 Index 里面的两个 Type（虽然可以做到）。

在console中输入下面的命令，可以列出每个 Index 所包含的 Type：

```
GET _mapping?pretty=true
```

###  数据操作

-  新建索引同时存放数据

使用如下命令新建索引(wysh_index为索引名称，wysh_type为类型名称，数字1为插入记录的id，在elasticsearch中，索引、类型、id三者的组合可唯一确定一条记录），同时可以将数据存放到索引中
```
PUT /wysh_index/wysh_type/1
{
    "first_name" : "Vincent",
    "last_name" :  "Wu",
    "age" :        25,
    "interests": [ "sports", "music" ]
}
```
服务器返回一个 JSON 对象，里面的 result 字段表示索引创建成功:
```
{
  "_index": "wysh_index",
  "_type": "wysh_type",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}
```
使用下面的 GET 语句，可以查询索引中所用的记录：
```
GET /wysh_index/_search
```
服务器返回一个 JSON 对象，里面的_source字段为查询结果的内容：
```
{
  "took": 16,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max_score": 1,
    "hits": [
      {
        "_index": "wysh_index",
        "_type": "wysh_type",
        "_id": "1",
        "_score": 1,
        "_source": {
          "first_name": "Vincent",
          "last_name": "Wu",
          "age": 25,
          "interests": [
            "sports",
            "music"
          ]
        }
      }
    ]
  }
}
```
- 删除索引

使用下面的语句可以删除索引：

```
DELETE /wysh_index
```
服务器返回一个 JSON 对象，里面的acknowledged字段表示删除成功
```
{
  "acknowledged": true
}
```
此时，我们再用上面的GET语句查询已经被删除的索引时，提示no such index：
```
{
  "error": {
    "root_cause": [
      {
        "type": "index_not_found_exception",
        "reason": "no such index",
        "resource.type": "index_or_alias",
        "resource.id": "wysh_index",
        "index_uuid": "_na_",
        "index": "wysh_index"
      }
    ],
    "type": "index_not_found_exception",
    "reason": "no such index",
    "resource.type": "index_or_alias",
    "resource.id": "wysh_index",
    "index_uuid": "_na_",
    "index": "wysh_index"
  },
  "status": 404
}
```
-  不同类型的查询操作

先向数据库里面再插入两条数据：
```
PUT /wysh_index/wysh_type/2
{
    "first_name" : "YongSheng",
    "last_name" :  "Wu",
    "age" :        36,
    "interests": [ "sports", "reading" ]
}
```
```
PUT /wysh_index/wysh_type/3
{
    "first_name" : "Wei",
    "last_name" :  "Zhang",
    "age" :        36,
    "interests": [ "cooking", "reading" ]
}
```
前面介绍的
```
GET /wysh_index/_search
```
会返回 wysh_index 索引中所有的记录，再次执行时，会返回_id为1、2、3这三条记录。

如果想查询某一id的记录，可以用如下的查询语句：
```
GET /wysh_index/wysh_type/3
```
执行了之后，会返回id为3的记录：
```
{
  "_index": "wysh_index",
  "_type": "wysh_type",
  "_id": "3",
  "_version": 1,
  "found": true,
  "_source": {
    "first_name": "Wei",
    "last_name": "Zhang",
    "age": 36,
    "interests": [
      "cooking",
      "reading"
    ]
  }
}
```
-  使用查询表达式搜索查询（全文搜索）

上面的查询都不带请求体，我们可以使用下面带请求体的查询：
```
GET /wysh_index/wysh_type/_search
{
    "query" : {
        "match" : {
            "last_name" : "Wu"
        }
    }
}
```
上面的查询会返回所有"last_name"中与"Wu"相关的记录，结果如下：
```
{
  "took": 27,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 2,
    "max_score": 0.2876821,
    "hits": [
      {
        "_index": "wysh_index",
        "_type": "wysh_type",
        "_id": "2",
        "_score": 0.2876821,
        "_source": {
          "first_name": "YongSheng",
          "last_name": "Wu",
          "age": 36,
          "interests": [
            "sports",
            "reading"
          ]
        }
      },
      {
        "_index": "wysh_index",
        "_type": "wysh_type",
        "_id": "1",
        "_score": 0.2876821,
        "_source": {
          "first_name": "Vincent",
          "last_name": "Wu",
          "age": 25,
          "interests": [
            "sports",
            "music"
          ]
        }
      }
    ]
  }
}
```
-   逻辑运算

支持逻辑“或”与逻辑“与”查询

下面是通过逻辑“或”进行查询的：
```
GET /wysh_index/wysh_type/_search
{
  "query" : { "match" : { "first_name" : "YongSheng Wei"}}
}
```
"first_name"字段有多个搜索关键字（关键字“YongSheng”和“Wei”， Elastic 认为它们是or关系，查询结果如下：
```
{
  "took": 23,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 2,
    "max_score": 0.2876821,
    "hits": [
      {
        "_index": "wysh_index",
        "_type": "wysh_type",
        "_id": "2",
        "_score": 0.2876821,
        "_source": {
          "first_name": "YongSheng",
          "last_name": "Wu",
          "age": 36,
          "interests": [
            "sports",
            "reading"
          ]
        }
      },
      {
        "_index": "wysh_index",
        "_type": "wysh_type",
        "_id": "3",
        "_score": 0.2876821,
        "_source": {
          "first_name": "Wei",
          "last_name": "Zhang",
          "age": 36,
          "interests": [
            "cooking",
            "reading"
          ]
        }
      }
    ]
  }
}
```

下面是通过逻辑“与”进行查询的：
```
GET /wysh_index/wysh_type/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "first_name": "YongSheng" } },
        { "match": { "last_name": "Wu" } }
      ]
    }
  }
}
```
查询同时满足"first_name"为 "YongSheng" ，"last_name"为"Wu"的记录，查询结果如下：
```
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max_score": 0.5753642,
    "hits": [
      {
        "_index": "wysh_index",
        "_type": "wysh_type",
        "_id": "2",
        "_score": 0.5753642,
        "_source": {
          "first_name": "YongSheng",
          "last_name": "Wu",
          "age": 36,
          "interests": [
            "sports",
            "reading"
          ]
        }
      }
    ]
  }
}
```
