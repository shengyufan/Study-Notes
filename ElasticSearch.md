[toc]

# 概述

ElasticSearch 是一个开源的分布式搜索和数据分析引擎，能达到**近实时**搜索，专门用于处理大规模文本数据和实现高性能的全文检索

## Elastic Stack

-   ElasticSearch：常应用于全文检索、日志分析和商业智能决策
-   Logstach：数据处理管道，负责数据收集、处理和传输
-   Beats：边缘数据采集器，负责从各种来源采集数据并发送到 Logstash 或 Elasticsearch
-   Kibana：可视化和管理工具，提供数据展示和交互式查询

## 重要概念

全文检索：一种从大量文本数据中**快速**检索出包含指定词汇或短语的信息的技术

倒排索引：在一个文档集合中，每个文档都可视为一个词语的集合，倒排索引则是将词语映射到包含这个词语的文档的数据结构

# 索引

使用场景

-   将采集的不同业务类型的数据存储到不同的索引
-   按日期切分存储日志索引

## 基本操作

### 创建索引

```json
PUT /index_name
{
    "settings": {
		"number_of_shards": 1,
		"number of replicas": 1
    },
	"mappings": {
		"properties": {
			"name": {
				"type": "text"
            },
			"age": {
                "type": "integer"
            },
            "enrolled_date": {
                "type":"date"
            }
        }
    }
}
```

必要的参数：

-   索引名称（index_name）：索引名称必须是小写字母，可以包含数字和下划线

-   索引设置（settings）

    1.   分片数量（numberofshards）：一个索引的分片数决定了索引的并行度和数据分布，如 `"number_of_shards": 1`

    2.   副本数量（numberofreplicas）：副本提高数据的可用性和容错能力，如 `"number_of_replicas": 1`

    3.   映射（mappings）：字段属性定义索引中文档的字段及其类型，常用字段类型包括 text、keyword、integer、float、date 等

         ```json
         "properties": {
             "field1": {
                 "type": "text"
             },
             "field2": {
                 "type": "keyword"
             }
         }
         ```

### 删除索引

`DELETE /index_name`

### 查询索引

`GET /index_name`

### 修改索引

修改相关设置

```json
PUT /index_name/_settings
{
    "index": {
        "number_of_replicas": 2
    }
}
```

添加新字段

```json
PUT /index_name/_mapping
{
    "properties": {
        "grade": {
            "type": "integer"
        }
    }
}
```

## 别名

问题场景

-   面对 PB 级别的增量数据，对外提供服务的是基于日期切分的 n 个不同索引，每次检索都要指定数十个甚至数百个索引，非常麻烦
-   线上提供服务的某个索引设计不合理，比如某字段分词定义不准确。如何保证对外提供服务不停止，即在不更改业务代码的前提下更换索引 

索引别名可以指向一个或多个索引，并且可以在任何需要索引名称的 API中使用

别名提供了极大的灵活性，它允许用户执行以下操作：

-   ﻿﻿在正在运行的集群上的一个索引和另一个索引之间进行透明切换
-   ﻿对多个索引进行分组组合
-   ﻿﻿在索引中的文档子集上创建“视图”，结合业务场景，缩小了检索范围，自然会提升检索效率

若索引和别名指向相同，则在相同检索条件下的检索效率是一致的，因为索引别名只是物理索引的软链接的名称而已

注意：

1.   对相同索引别名的物理索引建议有一致的映射，以提升检索效率
2.   推荐充分发挥索引别名在检索方面的优势，但在写入和更新时还得使用物理索引

### 添加别名

1.   创建时添加

```json
PUT myindex
{
    "aliases": {
    	"myindex_alias": {}
    },
    "settings": {
        "refresh_interval": "30s",
        "number_of_shards": 1,
        "number_of_replicas": 0
	}
}
```

2.   为已有索引增加别名

```json
POST /_aliases
{
    "actions": [
    {
        "add": {
            "index": "myindex",
            "alias": "myindex_alias"
        }
    }
    ]
}
```

# 文档操作

## 新增文档

### 新增单个文档

新增文档的操作可以通过 POST 或 PUT 请求完成，具体取决于是否指定了文档的唯一性标识（即 ID）。如果在创建数据时指定了唯一性标识，可以使用 POST 或 PUT 请求；如果没有指定唯一性标识，只能使用 POST 请求。若未指定 ID，POST 请求会自己生成一个文档 ID

```json
PUT /employee/_doc/1
{
    "name": "Alice",
    "gender": 1,
    "age": 20,
    "address": "USA",
    "remark": "developer"
}

POST /employee/_doc
{
    "name": "Bob",
    "gender": 2,
    "age": 25,
    "address": "UK",
    "remark": "manager"
}
```

PUT 与 POST 的差异

-   指定文档 ID：
    -   ﻿PUT 请求在创建或更新文档时必须指定文档的唯一 ID。如果指定的 ID 已经存在，PUT 请求会替换现有文档；如果不存在，则创建一个新文档
    -   ﻿﻿POST 请求在创建新文档时可以指定 ID，也可以不指定。如果不指定 ID，Elasticsearch 会自动生成一个唯一的 ID
-   幂等性：
    -   ﻿PUT 请求是幂等的，这意味着多次执行相同的 PUT 请求，即使是针对同一个文档，最终的结果都是一致的
    -   ﻿﻿POST 请求不是幂等的，多次执行相同的 POST 请求可能会导致创建多个文档
-   更新行为：
    -   PUT 请求在更新文档时会替换整个文档的内容，即使是文档中未更改的部分也会被新内容覆盖
    -   POST 请求在更新文档时可以使用 _update API，这样可以只更新文档中的特定字段，而不是替换整个文档

### 批量新增

批量操作可以减少网络连接所产生的开销，提升性能

-   ﻿支持在一次 API 调用中，对不同的索引进行操作
-   ﻿操作中单条操作失败，并不会影响其他操作
-   ﻿返回结果包括了每一条操作执行的结果

批量新增文档可以通过 _bulk API 来实现。这个 API 允许您将多个索引、更新或删除操作组合成一个单一的请求，从而提高批量操作的效率

每个操作都是一个独立的 JSON 对象，这些对象交替出现，形成一个请求体。每个 index 操作后面跟着的是要索引的文档内容，update 操作包含了更新的文档内容和操作类型，而 delete 操作则直接指明要删除的文档 ID。每个操作对象的开头都必须是 index、update 或 delete，并且每个操作之间用一个空行分隔

```json
POST /index_name/_bulk
{
    "index": 
    { 
        "_index": "<index_name>", 
        "_id": "<optional_document_id>" 
    } 
} 
{ 
    "field1": "value1",
    "field2": "value2" 
}
{ 
    "update": 
    {
        "_index": "<index_name>",
        "_id": "<document_id>"
    }
} 
{ 
    "doc": 
 	{ 
        "field1": "new_value1", 
        "field2": "new_value2"
    }, 
    "_op_type": "update"
}
{
    "delete": 
    {
        "_index": "<index_name>", 
        "_id": "<document_id>" 
    } 
}
{
    "index": 
    {
        "_index": "<index_name>",
        "_id": "<optional_document_id>" 
    } 
} 
{ 
    "field1": "value1", 
    "field2" : "value2"
}
```

Elasticsearch的 _bulk API 支持以下四种操作类型：

-   ﻿Index：用于创建新文档或替换已有文档
-   ﻿﻿Create：如果文档不存在则创建，如果文档已存在则返回错误
-   ﻿﻿Update：用于更新现有文档
-   ﻿﻿Delete：用于删除指定的文档

这些操作可以在单个_bulk APl 调用中对不同的索引进行，而且即使在批量操作中单条操作失败，也不会影响其他操作的执行。返回结果通常包括了每条操作的执行结果，以便客户端能够处理成功或失败的情况

## 查询文档

根据文档的 ID 查询单个文的标准语法是使用 GET 请求配合文档所在的索引名和文档 ID

```json
GET /<index_name>/_doc/<document_id>
```

使用 Multi GET API 可以根据 ID 查询多个文档。该 API 允许您在单个请求中指定多个文档的 ID，并返回这些文档的信息

```json
GET /<index_name>/_mget
{
    "ids": ["id1", "id2" , "id3"]
}
```

此外还可以根据搜索关键字进行查询，可使用 Query DSL（Domain Specific Language，一种基于 JSON 的语句）构建复杂的搜索查询

匹配所有文档

```json
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    }
}
```

文本字段匹配

```json
GET /<index_name>/_search
{
    "query": {
        "match": {
            "<field_name>": "<query_string>"
        }
    }
}
```

精确匹配（不分词）

```json
GET /<index_name>/_search
{
    "query": {
        "term": {
            "<field_name>": {
                "value": "<exact_value>"
            }
        }
    }
}
```

范围查询

```json
GET /<index_name>/_search
{
    "query": {
        "range": {
            "<field_name>": {
                "gte": <lower_bound>,
                "lte": <upper_bound>
            }
        }
    }
}
```

## 删除文档

### 删除单个文档

```json
DELETE /<index_name>/_doc/<document_id>
```

### 批量删除

删除多个文档可通过两种方法实现

1.   使用 _bulk API：每个删除请求是一个独立的 JSON 对象

     ```json
     POST _bulk
     {
         "delete": {
             "_index": "<index_name>",
             "id": "<id>"
         }
     }
     {
         "delete": {
             "_index": "<index_name>",
             "id": "<id>"
         }
     }
     
     POST /<index_name>/_bulk
     {
         "delete": {
             "id": "<id>"
         }
     }
     {
         "delete": {
             "id": "<id>"
         }
     }
     ```

2.   使用 _delete_by_query API：根据查询条件删除文档

     ```json
     POST /<index_name>/_delete_by_query
     {
         "query": {
             "<query>"
         }
     }
     ```

## 更新文档

### 更新单个文档

更新操作通过 _update API 执行，该接口允许部分更新现有文档的字段

```json
POST /<index_name/_update/<document_id>
{
    "doc": {
        "<field>": "<value>"
    }
}
```

### 批量更新

更新多个文档可通过两种方法实现

1.   使用 _bulk API：每个 update 块代表一个更新操作，其中 index 和 id 指定要更新的文档，doc 部分包含更新后的文档内容，upsert 部分定义文档不存在时应该插入的内容

     ```json
     POST _bulk
     {
         "update": {
             "_index": "<index_name>",
             "_id": "<document_id>"
         }
     }
     {
         "doc": {
             "field1": "value1",
             "field2": "value2"
         },
         "upsert": {
             "field1": "val1",
             "field2": "val2"
         }
     }
     ```

2.   使用 _update_by_query API：允许根据查询条件更新多个文档，但这个操作是原子性的

     ```json
     POST /<index_name>/_update_by_query
     {
         "query": {
             "<query>"
         },
         "script": {
             "source": "ctx._source.field = 'val'" ,
             "lang": "painless"
         }
     }
     ```

`_seq_no` 和 `_primary_term` 取代了旧版本的 `_version` 字段，用于控制文档的版本。`_seq_no` 代表文档在特定分片中的序列号，而 `_primary_term` 代表文档所在主分片的任期编号。这两个字段共同构成了文档的唯一版本标识符，用于实现乐观锁机制，确保在**高并发**环境下文档的一致性和正确更新。当在高并发环境下使用乐观锁机制修改文档时，要带上当前文档的 `_seq_no` 和`_primary_term` 进行更新
