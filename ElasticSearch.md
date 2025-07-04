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

## 关联关系

多表关联指一对多或多对多的数据关系，ElasticSearch 一般采用四种方法处理关联关系

-   嵌套对象（Nested Object）
-   Join 父子文档
-   宽表冗余存储
-   业务端关联

| 类型     | Nested 嵌套类型                                    | Join 父子文档类型                               | 宽表冗余存储               | 业务端关联                               |
| -------- | -------------------------------------------------- | ----------------------------------------------- | -------------------------- | ---------------------------------------- |
| 优点     | 文档存储在一起，读取性能高                         | 父子文档可以独立更新，互不影响                  | 以空间换时间               | 数据量小时，用户体验好                   |
| 缺点     | 更新嵌套的子文档时，需要更新整个文档，查询相对较慢 | Join 关系的维护也耗费内存。读取性能 Nested 还差 | 字段冗余造成存储空间的浪费 | 数据量多，两次查询耗时较长，影响用户体验 |
| 适用场景 | 对少量、子文档偶尔更新、查询频繁                   | 子文档更新频繁                                  | 一对多或者多对多           | 数据量少                                 |

### 嵌套对象

适用于一对少量、子文档偶尔更新、查询频繁的场景。如果需要索引对象数组并保持数组中每个对象的独立性，则应使用 Nested 数据类型而不是 Object 数据类型

Nested 类型的优点是 Nested 文档可以将父子关系的两部分数据关联起来（例如博客与评论），可以基于 Nested 类型做任何查询

Nested 类型的缺点则是查询相对较慢，更新子文档时需要更新整篇文档

### Join 类型

用于在同一索引的文档中创建父子关系。Join 类型适用于子文档数据量明显多于父文档的数据量的场景，该场景存在一对多量的关系，子文档更新频繁。举例来说，一个产品和供应商之间就是一对多的关联关系。当使用父子文档时，使用 has_child 或者 has_parent 做父子关联查询

Join 类型的优点是父子文档可独立更新

Join 类型的缺点则是维护 Join 关系需要占据部分内存，查询较 Nested 类型更耗资源

### 宽表冗余

适用于一对多或者多对多的关联关系

宽表的优点是速度快

宽表的缺点则是索引更新或删除数据时，应用程序不得不处理宽表的冗余数据；并且由于冗余存储，某些搜索和聚合操作的结果可能不准确

### 业务端关联

即在应用接口层面处理关联关系。一般建议在存储层面使用两个独立索引存储，在实际业务层面这将分为两次请求来完成。适用于数据量少的多表关联业务场景

数据量少时，用户体验好；而数据量多时，两次查询耗时肯定会比较长，反而影响用户体验

## 文档建模总结

1.   根据不同情形采用不同处理方式
2.   避免过多字段
3.   避免正则，通配符，前缀查询
4.   避免空值引起的聚合不准
5.   为索引的 Mapping 加 Meta 信息

# 高级查询语法

## match_all 匹配所有文档

match_all 查询是一个特殊的查询类型，它用于匹配索引中的所有文档，而不考虑任何特定的查询条件

```json
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    }
}
```

可以在 `match_all` 查询中添加额外的参数来控制搜索结果的显示，例如设置返回的文档数量（size）、开始返回的文档位置（from）、排序规则（sort）以及选择返回哪些字段（_source）

返回索引中前 10 个文档，按照文档评分进行排序

```json
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    },
    "size": 10,
    "sort": [
        {"_score": {"order": "desc"}}
    ]
}
```

`_source` 示例

```json
# 不查看源数据，仅查看元字段
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    },
    "_source": false
}

# 返回指定字段
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    },
    "_source": ["field1", "field2"]
}

# 返回符合 pattern 的字段，如以 obj. 开头的字段
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    },
    "_source": "obj.*"
}
```

## 精确匹配

精确匹配指搜索内容不经过文本分析，直接用于文本匹配，类似于数据的 SQL 查询，搜索对象大多是索引的非 text 类型字段

精确匹配主要应用于**结构化**数据，如 ID、状态和标签等。其包含

-   term 单字段精确匹配查询

-   ﻿terms 多字段精确匹配
-   ﻿range 范围查询
-   exists 是否存在查询
-   ﻿﻿ids 根据一组 id 查询

### term 单字段

主要应用于单字段精确匹配的场景，term 检索针对非 text 类型，用于 text 类型并不会报错，但结果可能不符合预期

```json
GET /<index_name>/_search
{
    "query": {
        "term": {
            "<field.keyword>": {
                "value": "<exact_value>"
            }
        }
    }
}
```

处理多值字段时，term 查询是包含，即查找数组中是否包含匹配项

term 查询对输入不做分词，即会将输入作为一个整体，在倒排索引中查找准确的词项。因此不需要使用相关度算分公式为每个包含该词项的文档进行相关度算分，可以通过 constant_score 将查询转换成一个 filtering，避免算分，并利用缓存，提高性能

-   ﻿将 query 转成 filter，忽略 TF-IDF 计算，避免相关性算分的开销
-   ﻿﻿filter 可以有效利用缓存

```json
GET /<index_name>/_search
{
    "query": {
        "constant_score" : {
            "filter": {
                "term": {
                    "<field>.keyword": {
                        "value": "<exact_value>"
                    }
                }
            }
        }
    }
}
```

### terms 多字段

应用于多值精准匹配场景，它允许用户在单个查询中指定多个词条来进行精确匹配。这种查询方式适合从文档中查找包含多个特定值的字段，例如筛选出具有多个特定标签或状态的项目。而 terms 检索是针对未分析的字段进行精确匹配的，因此它在处理关键词、数字、日期等结构化数据时表现良好

```json
GET /<index_name>/_search
{
    "query": {
        "terms": {
            "<field_name>": [
                "value1",
                "value2",
                "value3"
            ]
        }
    }
}
```

### range 范围查询

针对指定字段值在给定范围内的文档的检索类型。这种查询适合对数字、日期或其他可排序数据类型的字段进行范围筛选。range 检索支持多种比较操作符，如大于（gt）、大于等于（gte）、小于（lt）和小于等于（lte）等，可以实现灵活的区间查询

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

### exists 存在查询

用于筛选具有特定字段值的文档。这种查询类型适用于检查文档中是否存在某个字段，或者该字段是否包含非空值。通过使用 exists 检索，你可以有效地过滤掉缺少关键信息的文档，从而专注于包含所需数据的结果。应用场景包括但不限于数据完整性检查、查询特定属性的文档以及对可选字段进行筛选等

```json
GET /<index_name>/_search
{
    "query": {
       "exists": {
           "field": "missing_field"
       }
    }
}
```

### ids 组查询

用于返回具有指定ID列表的文档，允许我们基于给定的ID组快速召回相关数据，从而实现高效的文档检索

```json
GET /<index_name>/_search
{
    "query": {
       "ids": {
           "values": ["id1", "id2"]
       }
    }
}
```

## 模糊匹配

模糊匹配应用于**结构化**数据，包含

-   ﻿prefix 前缀匹配
-   ﻿﻿wildcard 通配符匹配
-   ﻿fuzzy 支持编辑距离的模糊查询
-   regexp 正则匹配查询
-   term_set 用于解决多值字段中的文档匹配问题

### prefix 前缀匹配

prefix 会对分词后的 term 进行前缀搜索

-   ﻿它不会对要搜索的字符串分词，传入的前缀就是想要查找的前缀
-   ﻿默认状态下，前缀查询不做相关性分数计算，它只是将所有匹配的文档返回，然后赋予所有相关分数值为1

其原理为遍历所有倒排索引，并比较每个词项是否以所搜索的前缀开头

```json
GET /<index_name>/_search
{
    "query": {
       "prefix": {
           "<field_name>": {
               "value": "<prefix>"
           }
       }
    }
}
```

### wildcard 通配符匹配

一种支持通配符匹配的查询类型，它允许在检索时使用通配符表达式来匹配文档的字段值。通配符包括两种。

-   ﻿星号（*）：表示零或多个字符，可用于匹配任意长度的字符串。
-   ﻿问号（?）：表示一个字符，用于匹配任意单个字符。

wildcard 检索适用于对部分已知内容的文本字段进行模糊检索。例如，在文件名或产品型号等具有一定规律的字段中，使用通配符检索可以方便地找到满足特定模式的文档

注意，通配符查询可能会导致较高的计算负担，因此在实际应用中应谨慎使用，尤其是在涉及大量文档的情况下

```json
GET /<index_name>/_search
{
    "query": {
       "wildcard": {
           "<field_name>": {
               "value": "<pattern>"
           }
       }
    }
}
```

### regexp 正则匹配

用于在字段中执行正则表达式匹配。一种基于正则表达式的检索方法。虽然该检索方式的功能强大，但建议在非必要情况下避免使用，以保持查询性能的高效和稳定

这个查询可以用来搜索满足特定模式的文本，并且比wildcard 查询更加灵活和强大

```json
GET /<index_name>/_search
{
    "query": {
       "regexp": {
           "<field_name>": {
               "value": "<pattern>"
           }
       }
    }
}
```

### fuzzy 编辑距离匹配

编辑距离是指从一个单词转换到另一个单词需要编辑单字符的次数。如中文集团到中威集团编辑距离就是 1，只需要修改一个字符；如果 fuzziness 值在这里设置成 2，会把编辑距离为 2 的东东集团也查出来

在用户输入内容存在拼写错误或上下文不一致时，仍然返回与搜索词相似的文档。通过使用编辑距离算法来度量输入词与文档中词条的相似程度，模糊查询在保证搜索结果相关性的同时，有效地提高了搜索容错能力

```json
GET /<index_name>/_search
{
    "query": {
       "fuzzy": {
           "<field_name>": {
               "value": "<term>",
               # AUTO 为 [0, 1, 2]，即表示编辑距离不超过2
               "fuzziness": "AUTO",
           	   # 搜索词的前缀长度，表示在此长度内不会应用模糊匹配
               "prefix_length": 1
           }
       }
    }
}
```

### term_set 数组匹配

用于解决多值字段中的文档匹配问题，检索至少匹配一定数量给定词项的文档，其中匹配的数量可以是固定值，也可以是基于另一个字段的动态值，在处理具有多个属性、分类或标签的复杂数据时非常有用

从应用场景来说，terms set 检索在处理多值字段和特定匹配条件时具有很大的优势。它适用于标签系统、搜索引擎、电子商务系统、文档管理系统和技能匹配等场景

```json
GET /<index_name>/_search
{
    "query": {
       "term_set": {
           "<field_name>": {
               "term": ["term1", "term2"],
               # 自定义脚本，用于动态计算匹配数量
               "minimum_should_match_script": {
                   "source": "<script>"
               }
               # 也可以静态指定需要匹配的数量
               # "minimum_should_match": "<value>"
    		   # 也可以静态指定需要匹配的字段
               # "minimum_should_match_field": "<name>"
           }
       }
    }
}
```

## 全文检索

全文检索查询旨在基于相关性搜索和匹配文本数据。这些查询会对输入的文本进行分析，将其拆分为词项（单个单词），并执行诸如分词、词干处理和标准化等操作

此类检索主要应用于**非结构化**文本数据，如文章和评论等。其包含

-   match 分词查询
-   multi_match 多字段查询
-   match_phrase 短语查询
-   query_string 与或非表达式查询
-   simple_query_string

### match 分词查询

使用分析器将查询字符串分解成单独的词条，并在倒排索引中搜索这些词条。 match 查询适用于文本字段，并且可以通过多种参数来调整搜索行为。

对于 match 查询，其底层逻辑的概述：

1.   分词：首先，输入的查询文本会被分词器进行分词。分词器会将文本拆分成一个个词项（terms），如单词、短语或特定字符。分词器通常根据特定的语言规则和配置进行操作
2.   ﻿﻿匹配计算：一旦查询被分词，ES 将根据查询的类型和参数计算文档与查询的匹配度。对于 match 查询，ES 将比较查询的词项与倒排索引中的词项，并计算文档的相关性得分。相关性得分衡量了文档与查询的匹配程度
3.   ﻿﻿结果返回：根据相关性得分，ES 将返回最匹配的文档作为搜索结果。搜索结果通常按照相关性得分进行排序，以便最相关的文档排在前面

```json
GET /<index_name>/_search
{
    "query": {
       "match": {
           "<field_name>": "<query_str>"
       }
    }
}

GET /<index_name>/_search
{
    "query": {
       "match": {
           "<field_name>": {
               "query": "<query_str>",
               "operator": "or",
               "minimum_should_match": <value>
           }
       }
    }
}
```

### multi_match 多字段查询

用于在多个字段上执行相同的搜索操作。它可以接受一个查询字符串，并在指定的字段集合中搜索这个字符串。multi_match 查询提供了灵活的匹配类型和操作符选项，以便根据不同的搜索需求调整搜索行为

```json
GET /<index_name>/_search
{
    "query": {
       "multi_match": {
           "query": "<query_str>",
           "fields": ["<field1>", "<field2>"]
       }
    }
}
```

### match_phrase 短语查询

用于执行短语搜索，它不仅匹配整个短语，而且还考虑了短语中各个词的**顺序和位置**。这种查询类型对于搜索精确短语非常有用，尤其是在用户输入的查询与文档中的文本表达方式需要严格匹配时

```json
GET /<index_name>/_search
{
    "query": {
       "match_phrase": {
           "<field_name>": {
               "query": "<phrase>",
               # 匹配查询词条可间隔的距离
               "slop": <value>
           }
       }
    }
}
```

### query_string 表达式查询

 它允许使用 Lucene 查询语法来构建复杂的搜索查询。这种查询类型支持多种逻辑运算符，包括与（AND）、或（OR）和非（NOT），以及通配符、模糊搜索和正则表达式等功能。query_string 查询可以在单个或多个字段上进行搜索，并且可以处理复杂的查询逻辑。

其应用场景包括高级搜索、数据分析和报表等，适合处理需满足特定需求、要求支持与或非表达式的复杂查询任务，通常用于专业领域或需要高级查询功能的应用中

```json
GET /<index_name>/_search
{
    "query": {
       "query_string": {
           "query": "<query_str>",
           "default_field": "<field_name>"
       }
    }
}
```

### simple_query_string

简化版本的 query_string，只支持部分查询语法，不支持 AND OR NOT，会当作字符串处理。支持部分逻辑：

-   ﻿+ 替代 AND
-   ﻿I 替代 OR
-   ﻿- 替代 NOT

在生产环境中推荐使用 simple_query_string 而不是 query_string 主要是因为simple_query_string 提供了更宽松的语法，能够容忍一定程度的输入错误，而不会导致整个查询失败

```json
GET /<index_name>/_search
{
    "query": {
       "simple_query_string": {
           "query": "<query_str>",
           "fields": [<field1>"", "<field2>"],
           "default_operator": "OR"
       }
    }
}
```

## 布尔查询

布尔查询可以按照布尔逻辑条件组织多条查询语句，只有符合整个布尔条件的文档才会被搜索出来。

在布尔条件中，可以包含两种不同的上下文。

1.  ﻿﻿﻿搜索上下文（query context）：使用搜索上下文时，Elasticsearch 需要计算每个文档与搜索条件的相关度得分，这个得分的计算需使用一套复杂的计算公式，有一定的性能开销，带文本分析的全文检索的查询语句很适合放在搜索上下文中。
2.  ﻿﻿﻿过滤上下文（filter context）：使用过滤上下文时，Elasticsearch 只需要判断搜索条件跟文档数据是否匹配，例如使用 term query 判断一个值是否跟搜索内容一致，使用 range query 判断某数据是否位于某个区间等。过滤上下文的查询不需要进行相关度得分计算，还可以使用缓存加快响应速度，很多术语级查询语句都适合放在过滤上下文中

支持 4 种组合类型

| 类型     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| must     | 可包含多个查询条件，每个条件均满足的文档才能被搜索到，每次查询需要计算相关度得分，属于搜索上下文 |
| should   | 可包含多个查询条件，不存在 must 和 filter 条件时，至少要满足多个查询条件中的一个，文档才能被搜索到，否则需满足的条件数量不受限制，匹配到的查询越多相关度越高，也属于搜索上下文 |
| filter   | 可包含多个过滤条件，每个条件均满足的文档才能被搜索到，每个过滤条件不计算相关度得分，结果在一定条件下会被缓存，属于过滤上下文 |
| must_not | 可包含多个过滤条件，每个条件均不满足的文档才能被搜索到，每个过滤条件不计算相关度得分，结果在一定条件下会被缓存，属于过滤上下文 |

```json
GET /<index_name>/_search
{
    "query": {
       "bool": {
           "must": [
               {
                   "match": {
                       "<field_name>": "<value>"
                   }
               },
               {
                   "match": {
                       "<field_name>": "<value>"
                   }
               }
           ]
       }
    }
}
```

## 高亮

highlight 关键字：可以让符合条件的文档中的关键词高亮

highlight 相关属性：

-   ﻿﻿pre_tags 前缀标签
-   ﻿﻿post_tags 后缀标签
-   ﻿﻿tags_schema 设置为 styled 可以使用内置高亮样式
-   ﻿﻿require_field_match 多字段高亮需要设置为 false

```json
GET /<index_name>/_search
{
    "query": {
        "term": {
            "<field.keyword>": {
                "value": "<exact_value>"
            }
        }
    },
    "highlight": {
        "pre_tags": ["<pattern>"],
        "post_tags": ["<pattern>"],
        "fields": {
            "*": {}
        }
    }
}
```

## 地理空间查询

ElasticSearch 中地理空间数据通常存储在 geo_point 字段类型中，其包含经纬度坐标，用于表示地球上的一个点

允许用户基于地理位置信息来搜索和过滤数据。在 Elasticsearch 这样的全文搜索引擎中，地理空间位置查询被广泛应用，例如在旅行、房地产、物流和零售等行业，用于提供基于位置的搜索功能

```json
GET /my_index/_search
{
    "query": {
        "bool": {
            "must": {
                "match_all": {}
            },
            "filter": {
                "geo_distance": {
                    "distance": "10km",
                    "distance_type": "arc",
                    "location": {
                        "lat": 39.9,
                        "lon": 116.4
                    }
                }
            }
        }
    }
}
```

## 向量查询

通过 KNN（K-Nearest Neighbors）算法支持向量近邻检索。这一特性使得 ElasticSearch 在机器学习、数据分析和推荐系统等领域的应用变得更加广泛和强大

向量检索的基本思路是，将文档（或数据项）表示为高维向量，并使用这些向量来执行相似性搜索。在 ElasticSearch 中，这些向量被存储在 dense_vector 类型的字段中，然后使用 KNN 算法来找到与给定向量最相似的其他向量

```json
POST /<index_name>/_search
{
    "knn" : {
        "field": "<field_name>",
        "query_vector": [-5, 10, -12],
        "k": 10,
        "num_candidates": 100
    },
	"fields": [ "<field1>", "<field2>"]
}
```

# 相关性

用于描述一个文档与查询语句匹配程度的度量标准

## TF-IDF 算法

ES5 之前使用该算法计算相关性
$$
\text{TF-IDF}(t, d, D) = \text{TF}(t, d) \times \text{IDF}(t, D)
$$
TF 是词频：检索词在文档中出现的频率越高，相关性也越高
$$
\text{TF}(t, d) = \frac{f_{t,d}}{\sum_{k} f_{k,d}}
$$

- $f_{t,d}$：词语（t）在文档（d）中出现的次数  
- $\sum_{k} f_{k,d}$：文档（d） 中所有词语出现的总次数

IDF 是逆向文本频率：每个检索词在索引中出现的频率，频率越高，相关性越低
$$
\text{IDF}(t, D) = \log \left( \frac{N}{1 + |\{d \in D : t \in d\}|} \right)
$$

- N：语料库中总文档数  
- $|\{d \in D : t \in d\}|$：包含词（t）的文档数  
- 分母加 1 是为了避免除以 0

还可以乘上字段长度归一值：检索词出现在一个内容短的 title 要比同样的词出现在一个内容长的 content 字段权重更大

## BM25 算法

BM25 在考虑词频（TF）和逆文档频率（IDF）的基础上，引入了对**文档长度的惩罚**，避免长文档“占便宜”
$$
\text{score}(D, Q) = \sum_{t \in Q} \text{IDF}(t) \cdot \frac{f(t, D) \cdot (k_1 + 1)}{f(t, D) + k_1 \cdot \left(1 - b + b \cdot \frac{|D|}{\text{avgdl}}\right)}
$$

- $f(t, D)$：词 $t$ 在文档 $D$ 中出现的次数（词频）
- $|D|$：文档 $D$ 的长度（总词数）
- $\text{avgdl}$：语料库中文档的平均长度
- $k_1$、$b$：可调参数（通常 $k_1 = 1.2$, $b = 0.75$）

$$
\text{IDF}(t) = \log\left( \frac{N - n(t) + 0.5}{n(t) + 0.5} + 1 \right)
$$

| 特性         | TF-IDF | BM25                     |
| ------------ | ------ | ------------------------ |
| 考虑文档长度 | 否     | 是                       |
| 词频增长趋势 | 线性   | 饱和型（非线性）         |
| 排序效果     | 一般   | 更好（用于实际搜索引擎） |

## 自定义评分

实现相关性计算时，算法提供了一个可变参数 boost，从而帮助实现自定义评分

自定义评分的核心是通过修改评分来修改文档相关性，在最前面的位置返回用户最期望的结果。其策略包含

-   indices_boost：索引层面修改相关性
-   boosting：修改文档相关性
-   negative_boost：降低相关性
-   function_score： 自定义评分
-   rescore_query：查询后二次打分

### indices_boost

这种方式能在跨多个索引搜索时为每个索引配置不同的级别。所以它适用于**索引**级别调整评分

```json
POST my_index_100*/_search
{
    "query": {
        "term": {
            "subject.keyword": {
                "value": "subject 1"
            }
        }
    },
    "indices_boost": [
        {
            "my_index_100a": 1.5
        },
        {
            "my_index_100b": 1.2
        },
        {
            "my_index_100c": 1
        }
    ]
｝
```

### boosting

boosting 可在查询时修改文档的相关度。boosting 值所在范围不同，含义也不同

-   boosting 默认值为 1
-   若 boosting 值为 0~1，如 0.2，代表降低评分
-   若 boosting 值 ＞1，如 1.5，则代表提升评分

适用于某些特定的查询场景，用户可以自定义修改满足某个查询条件的结果评分

```json
GET /blogs/_search
{
    "query": {
        "bool": {
            "should": [
                {
                    "match": {
                        "title": {
                            "query": "apple,ipad",
                            "boost": 4
                        }
                    }
                },
                {
                    "match": {
                        "content": {
                            "query": "apple,ipad"
                        }
                    }
                }
            ]
        }
    }
}

```

### negative_boost

若对某些返回结果不满意，但又不想将其排除（must_not），则可以考虑采用 negative_boost 的方式。

原理说明如下：

-   ﻿﻿negative_boost 仅对查询中定义为 negative 的部分生效。
-   ﻿﻿计算评分时，不修改 boosting 部分评分，而 negative 部分的评分则乘以 negative_boost 的值。
-   ﻿﻿negative_boost 取值为 0~1.0，如 0.3

```json
GET /news/_search
{
    "query": {
        "boosting": {
            "positive": {
                "match": {
                    "content": "apple"
                }
            },
            "negative": {
                "match": {
                    "content": "pie"
                }
            },
            "negative_boost": 0.2
        }
    }
}
```

### function_score

支持用户自定义一个或多个查询语句及脚本，达到精细化控制评分的目的，以对搜索结果进行高度个性化的排序设置。适用于需进行复杂查询的自定义评分业务场景

```json
POST /products/_search
{
    "query": {
        "function_score": {
            "query": {
                "match_all": {}
            },
            "script_score": {
                "script": {
                    # 原始分数 *（销量 + 浏览人数）
                    "source": "_score * (doc['sales'].value + doc['visitors'].value)"
                }
            }
        }
    }
}
```

### rescore_query

二次评分是指重新计算查询所返回的结果文档中指定文档的得分

ElasticSearch 会截取查询返回的前 N 条结果，并使用预定义的二次评分方法来重新计算其得分。但对全部有序的结果集进行重新排序的话，开销势必很大，使用 rescore_query 可以只对结果集的子集进行处理。该方式适用于对查询语句的结果不满意，需要重新打分的场景

```json
# 查询 content 字段中包含”实战“的文档，权重为0.7
# 并对文档中 title 为 MySQL 的文档增加评分，权重为1.2
# window_size 为 50，表示取分片结果的前 50 进行重新算
GET /books/_search
{
    "query": {
        "match": {
            "content": "实战"
        }
    },
    "rescore": {
        "query": {
            "rescore_query": {
                "match": {
                    "title": "MySQL"
                }
            },
            "query_weight": 0.7,
            "rescore_query_weight": 1.2
        },
        "window_size": 50
    }
}
```

## 多字段搜索

优化方案

-    最佳字段（Best Fields）：多个字段中返回评分最高的

当字段之间相互竞争，又相互关联。例如，对于博客的 title 和 body 这样的字段，评分来自最匹配字段

-   多数字段（Most Fields）：匹配多个字段，返回各个字段评分之和

处理英文内容时的一种常见的手段是，在主字段（English Analyzer），抽取词干，加入同义词，以匹配更多的文档。相同的文本，加入子字段（Standard Analyzer），以提供更加精确的匹配。其他字段作为匹配文档提高相关度的信号，匹配字段越多则越好

-   混合字段（Cross Fields）：跨字段匹配，待查询内容在多个字段中都显示

对于某些实体，例如人名，地址，图书信息。需要在多个字段中确定信息，单个字段只能作为整体的一部分。希望在任何这些列出的字段中找到尽可能多的词

### 最佳字段

将任何与任一查询匹配的文档作为结果返回，采用字段上最匹配的评分作为最终评分返回

1.   使用 dis_max 查询：取多字段匹配中最高分数作为文档的匹配分数

```json
POST /blogs/_search
{
    "query": {
        "dis_max": {
            "queries": [
                {
                    "match": {
                        "title": "Brown fox"
                    }
                },
                {
                    "match": {
                        "body": "Brown fox"
                    }
                }
            ]
        }
    }
}
```

这种方法还可以通过 tie_breaker 参数进行调整

tie_breaker 是一个介于 0-1 之间的浮点数。0 代表使用最佳匹配；1 代表所有语句同等重要

1.  ﻿﻿﻿获得最佳匹配语句的评分 _score
2.  ﻿﻿﻿将其他匹配语句的评分与 tie_breaker 相乘
3.  ﻿﻿﻿对以上评分求和并规范化

即最终得分 = 最佳匹配字段 + 其他匹配字段 * tie_breaker

2.   使用 best_fields 查询：与 dis_max 查询功能类似，但更简洁

```json
POST /blogs/_search
{
    "query": {
        "multi_match": {
            "type": "best_fields",
            "query": "Brown fox",
            "fields": ["title", "body"],
            "tie_breaker": 0.2
        }
    }
}
```

### 多数字段

获取全部匹配字段的累计得分，等价于 bool should 查询

```json
GET /employee/_search
{
    "query": {
        "multi_match": {
            "query": "elasticsearch beginner 湖北省 开封市",
            "type": "most_fields",
            "fields": [
                "content",
                "address"
            ]
        }
    }
}
```

### 混合字段

搜索内容在多个字段中都显示，类似于 bool + dis_max 组合

```json
GET /address/_search
{
    "query": {
        "multi_match": {
            "query": "湖南常德",
            "type": "cross_fields",
            "operator": "and",
            "fields": ["province", "city"]
        }
    }
}
```

另一种解决方案是添加数据时使用 copy_to 参数，其允许将多个字段的值复制到组字段中，然后可以将其作为单个字段进行查询

```json
PUT /address
{
    "mappings": {
        "properties": {
            "province": {
                "type": "keyword",
                "copy_to": "full_address"
            },
            "city": {
                "type": "text",
                "copy_to": "full_address"
            }
        }
    },
    "settings": {
        "index": {
            "analysis.analyzer.default.type": "ik_max_word"
        }
    }
}
```

与 copy_to 相比，cross_fields 可以在搜索时为单个字段提升权重，并且 copy_to 需要额外的存储空间

# 聚合

