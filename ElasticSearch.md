[toc]
<div STYLE="page-break-after:always;"></div>

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

**索引**：在使用传统的关系型数据库时，如果对数据有存取和更新操作，需要建立一个数据库。相应地，在 ES 中则需要建立索引。用户的数据新增、搜索和更新等操作的对象全部对应索引。但是，ES 中的索引和 Lucene 中的索引不是一一对应的。ES 中的一个索引对应一个或多个 Lucene 索引，这是由其分布式的设计方案决定的

**文档**：在使用传统的关系型数据库时，需要把数据封装成数据库中的一条记录，而在 ES 中对应的则是文档。ES的文档中可以有一个或多个字段，每个字段可以是各种类型。用户对数据操作的最细粒度对象就是文档。ES 文档操作使用了版本的概念，即文档的初始版本为 1，每次的写操作会把文档的版本加 1，每次使用文档时，ES 返回给用户的是最新版本的文档。另外，为了减轻集群负载和提升效率，ES提供了文档的批量索引、更新和删除功能

**字段**：一个文档可以包含一个或多个字段，每个字段都有一个类型与其对应。除了常用的数据类型（如字符串型、文本型和数值型）外，ES 还提供了多种数据类型，如数组类型、经纬度类型和 IP 地址类型等。ES 对不同类型的字段可以支持不同的搜索功能。例如，当使用文本类型的数据时，可以按照某种分词方式对数据进行搜索，并且可以设定搜索后的打分因子来影响最终的排序。再如，使用经纬度的数据时，ES 可以搜索某个地点附近的文档，也可以查询地理围栏内的文档。在排序函数的使用上，ES也可以基于某个地点按照衰减函数进行排序

**映射**：建立索引时需要定义文档的数据结构，这种结构叫作映射。在映射中，文档的字段类型一旦设定后就不能更改。因为字段类型在定义后，ES 已经针对定义的类型建立了特定的索引结构，这种结构不能更改。借助映射可以给文档新增字段。另外，ES 还提供了自动映射功能，即在添加数据时，如果该字段没有定义类型，ES 会根据用户提供的该字段的真实数据来猜测可能的类型，从而自动进行字段类型的定义

**集群和节点**：在分布式系统中，为了完成海量数据的存储、计算并提升系统的高可用性，需要多台计算机集成在一起协作，这种形式被称为集群。这些集群中的每台计算机叫作节点。ES 集群的节点个数不受限制，用户可以根据需求增加计算机对搜索服务进行扩展

**分片**：在分布式系统中，为了能存储和计算海量的数据，会先对数据进行切分，然后再将它们存储到多台计算机中。这样不仅能分担集群的存储和计算压力，而且在该架构基础上进一步优化，还可以提升系统中数据的高可用性。在 ES 中，一个分片对应的就是一个 Lucene 索引，每个分片可以设置多个副分片，这样当主分片所在的计算机因为发生故障而离线时，副分片会充当主分片继续服务。索引的分片个数只能设置一次，之后不能更改。在默认情况下，ES 的每个索引设置为 5 个分片

**副分片**：为了提升系统索引数据的高可用性并减轻集群搜索的负载，可以启用分片的副本，该副本叫作副分片，而原有分片叫作主分片。在一个索引中，主分片的副分片个数是没有限制的，用户可以按需设定。在默认情况下，ES 不会为索引的分片开启副分片，用户需要手动设置。副分片的个数设定后，也可以进行更改。一个分片的主分片和副分片分别存储在不同的计算机上。在极端情况下，当只有一个节点时，如果索引的副分片个数设置大于 1，则系统只分配主分片，而不会分配副分片

**DSL**：ES 使用 DSL（Domain Specific Language，领域特定语言），来定义查询。与编程语言不同，DSL是在特定领域解决特定任务的语言，它可以有多种表达形式，如我们常见的 HTML、CSS、SQL 等都属于 DSL。ES 中的 DSL 采用 JSON 进行表达，相应地，ES 也将响应客户端请求的返回数据封装成了 JSON 形式。这样不仅可以简单明了地表达请求/响应内容，而且还屏蔽了各种编程语言之间数据通信的差异

## 与关系型数据库的对比

1.   **索引方式**：传统关系型数据采用 B-Tree 或 B+-Tree 结构，而 ES 使用倒排索引。两种不同的数据索引方式决定了这两种产品在某些场景中性能和速度的差异
2.   **事务支持**：事务是关系型数据库的核心组成模块，而 ES 时不支持事务的。ES 更新文档时，先读取文档再进行修改，然后再为文档重新建立索引。如果同一个文档同时有多个并发请求，则极有可能会丢失某个更新操作。为了解决这个问题，ES 使用了**乐观锁**，即假定冲突是不会发生的，不阻塞当前数据的更新操作，每次更新会增加当前文档的版本号，最新的数据由文档的最新版本来决定，这种机制就决定了 ES 没有事务管理
3.   **SQL 和 DSL**：SQL 是关系型数据库使用的语言，主要是因为 SQL 查询的逻辑比较简单和直接，一般是大小、相等之类的比较运算，以及逻辑与、或、非的关系运算。ES 不仅包含上述运算，而且支持文本搜索、地理位置搜索等复杂数据的搜索，因此 ES 使用 DSL 查询进行请求通信。虽然ES的高版本也开始支持 SQL 查询，但若需要完成比较复杂的数据搜索需求，使用 DSL 查询会更加方便快捷
4.   **扩展方式**：关系型数据库的扩展需要借助第三方组件完成分库分表（按照某个 ID 取模将数据打散后分散到不同的数据节点中，借此来分摊集群的压力）的支持，而 ES 本身就是支持分片的，只要初期对分片个数进行了合理设置，后期不需要对扩展过分担心
5.   **查询速度**：关系型数据库的查询速度会随着数据量的增长越来越慢（即使通过索引进行优化），而 ES 可以支持全字段建立索引，查询速度并不会变慢
6.   **实时性**：关系型数据库存储和查询数据基本上是实时的，即单条数据写入之后可以立即查询。为了提高数据写入的性能，ES 在内存和磁盘之间增加了一层系统缓存。ES 响应写入数据的请求后，会先将数据存储在内存中，此时该数据还不能被搜索到，当内存中数据刷到系统缓存后，才能被搜索到

## 架构原理

### 节点

按照职责可以分为 master 节点、数据节点和协调节点

-   master 节点负责维护整个集群的相关工作，管理集群的变更，如创建/删除索引、节点健康状态监测、节点上/下线等。一个集群中只有一个节点可以成为 master 节点，但是可以有一个或多个节点参与 master 节点的选举
-   数据节点主要负责索引数据的保存工作，也执行数据的其他操作，如文档的删除、修改和查询操作
-   协调节点与客户端进行沟通，其生命周期是和一个单独的请求相关的，即当客户端向集群中的某个节点发起请求时，此时该节点被称为当前请求的协调节点；当它将响应结果返回给客户端后，该协调节点的生命周期就结束了

### 分片

ES 为了支持分布式搜索，会把数据按照分片进行切分。一个索引由一个或者多个分片构成，并且每个分片有 0 个甚至多个副分片。多个分片分布在不同的节点中，通过这种分布式结构提升了分片数据的高可用性和服务的高并发支持

**高可用**：集群中的索引主分片和副分片在不同的计算机上，如果某个主分片所在的节点宕机，则原有的某个副分片会提升为主分片继续对外进行服务

**高并发**：当客户端对某个索引的请求被分发到ES的协调节点时，协调节点会将请求进行转发，转发的对象是包含这个索引的所有分片的部分节点。协调节点中有一份分片-节点路由表，该表主要存放分片和节点的对应关系。协调节点采用轮询算法，选取该索引的主/副分片所在的节点进行请求转发。一个索引的主分片设定后就不能再修改，如果想继续提升索引的并发性能，则可以增加索引的副分片个数，此时协调节点会将这些副分片加入轮询算法中

### 文档读写

当 ES 协调节点接收到来自客户端对某个索引的写入文档请求时，该节点会根据一定的路由算法将该文档映射到某个主分片上，然后将请求转发到该分片所在的节点。完成数据的存储后，该节点会将请求转发给该分片的其他副分片所在的节点，直到**所有副分片节点全部完成写入**，ES 协调节点向客户端报告写入成功

当 ES 协调节点接收到来自客户端的获取某个索引的某文档的请求时，协调节点会找到该文档所在的所有分片，然后根据轮询算法在主/副分片中选择一个分片并将请求转发给该分片所在的节点，该节点会将目标数据发送给协调节点，协调节点再将数据返回给客户端

## 应用场景

1.   **搜索引擎**：创建索引时，业务系统模块把数据存储到数据库中，第三方数据同步模块（如 Canal）负责将数据库中的数据按照业务需求同步到 ES 中。搜索时，前端应用先向搜索模块发起搜索请求，然后搜索模块组织搜索 DSL 向 ES 发起请求，ES 响应搜索模块的请求开始搜索，并将搜索到的商品信息（如名称、价格、地理位置等）进行封装，然后把数据传送给搜索模块，进而数据再由搜索模块传递到前端进行展现。较典型的应用领域是垂直搜索，如电商搜索、地图搜索、新闻搜索等各类站内搜索
2.   **推荐系统**：ES 在高版本中引入了高维向量的数据类型。可以把推荐模型算法计算的商品和用户向量存储到 ES 索引中，当实时请求时，加载用户向量并使用 ES 的 Script Score 进行查询，使每个文档最终的排序分值等于当前用户向量与当前文档向量的相似度。为同时满足实时向量计算和实时数据过滤的需求，可以在 Script Score 查询中添加 filter（即过滤条件，如库存、上下架状态等）
3.   **二级索引**：对于强事务性的数据，需要将其存储在关系型数据库中，如果还需要使用任意组合字段进行查询，或者按照某些文本字段进行搜索且这些字段是弱事务性的，那么可以考虑使用 ES 作为二级索引
4.   **日志分析**：ES 具有很强的查询能力，支持任意字段的各种组合查询，同时它又具有很强大的数据统计和分析能力，因此也可以当作数据分析引擎。ES 官方提供的 ELK（Elasticsearch + Logstash + Kibana）全家桶可以完成日志采集、索引创建再到可视化的数据分析等工作，使用户可以 0 代码完成搭建工作。ES 支持的日志分析类型可以是多种多样的，生产中的用户行为日志、Web 容器日志、接口调用日志及数据库日志等都可以通过 ELK 进行分析

# 客户端

## Kibana

Kibana 是 ELK 家族中一个开源、免费的可视化数据搜索和分析平台。借助 Kibana，用户不需要编码就可以将 ES 中分析的结果进行可视化呈现，如以常用的饼图、柱状图和时序图等方式呈现。除了可视化数据分析功能，Kibana 还提供了 Dev Tools，它是一款可以与 ES 进行交互式请求的工具，可以借助它进行 DSL 调试

## Jest 客户端

JestClient 是一款基于 HTTP 实现的 ES 客户端，在 RestHighLevelClient 出现之前，它填补了 ES 缺少 HTTP REST 接口客户端的空白。JestClient 具有一定的 ES 服务端版本兼容性，支持数据的同步和异步操作，同时，它也提供了索引时和搜索结果的 POJO 映射机制，即 OR-Mapping 功能

需要使用的依赖

```xml
<dependencies> 
    <!-- Spring Boot Web依赖--> 
    <dependency> 
        <groupId>org.springframework.boot</groupId> 
        <artifactId>spring-boot-starter-web</artifactId> 
    </dependency> 
    <!--Jest依赖--> 
    <dependency> 
        <groupId>io.searchbox</groupId> 
        <artifactId>jest</artifactId> 
        <version>6.3.1</version> 
    </dependency> 
    <!--简化开发组件Lombok--> 
    <dependency> 
        <groupId>org.projectlombok</groupId> 
         <artifactId>lombok</artifactId> 
    </dependency> 
    <!--ES依赖--> 
    <dependency> 
        <groupId>org.elasticsearch</groupId> 
        <artifactId>elasticsearch</artifactId> 
        <version>7.10.2</version> 
    </dependency> 
</dependencies>
```

注意：在项目启动时，Jest 并不会连接 ES，只有在调用 `execute()` 方法时 Jest 才开始进行连接

```java
import io.searchbox.client.JestClient; 
import io.searchbox.client.JestResult; 
import io.searchbox.core.Search; 
import org.elasticsearch.index.query.MatchQueryBuilder; 
import org.elasticsearch.index.query.QueryBuilders; 
import org.elasticsearch.search.builder.SearchSourceBuilder; 
import org.springframework.beans.factory.annotation.Autowired; 
import org.springframework.stereotype.Service; 
import java.util.List; 
 
@Service 
public class EsService { 
    @Autowired 
    JestClient jestClient; 
    public  List<Hotel> getHotelFromTitle(String keyword){ 
        MatchQueryBuilder matchQueryBuilder=QueryBuilders.matchQuery 
("title",keyword);       //match搜索 
        SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
        searchSourceBuilder.query(matchQueryBuilder); 
        Search search=new Search.Builder(searchSourceBuilder.toString()). 
addIndex("hotel").build();       //创建搜索请求 
        try{ 
            JestResult jestResult=jestClient.execute(search); //执行搜索 
            if(jestResult.isSucceeded()){   //判断搜索是否成功 
                //将结果封装到Hotel类型的List中 
                return jestResult.getSourceAsObjectList(Hotel.class); 
            } 
        }catch (Exception e){ 
            e.printStackTrace(); 
        } 
        return null; 
    } 
}
```

## Java 客户端

ES 和客户端的通信通过 HTTP 进行，用户可以使用任意语言完成搜索。此外，ES 提供了 Java、Python、Ruby、PHP 和 Go 等多种语言的 API，从客户端连接到搜索 DSL 构建，再到搜索结果的解析，使用这些 API 可以更好地完成搜索

Java 客户端分为低级客户端和高级客户端两种。低级客户端兼容所有版本的 ES，但其需要提供 JSON 字符串，因此这种开发方式比较低效。高级客户端是基于低级客户端开发出来的，屏蔽了底层技术，使用户可以更专注于搜索业务，这是官方推荐的开发方式

需要使用的依赖

```xml
<dependencies>
        <!-- Spring Boot Web依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- ES客户端依赖 -->
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-high-level-client</artifactId>
            <version>7.10.2</version>
        </dependency>

        <!-- ES依赖 -->
        <dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>7.10.2</version>
        </dependency>

        <!-- 使用Lombok简化开发 -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
</dependencies>
```

实现带验证的 Java 客户端

```java
import org.apache.http.HttpHost;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.UsernamePasswordCredentials;
import org.apache.http.client.CredentialsProvider;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.apache.http.impl.nio.client.HttpAsyncClientBuilder;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestClientBuilder;
import org.elasticsearch.client.RestHighLevelClient;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

import java.util.Arrays;
import java.util.Objects;

@Component
public class EsClient {
    @Value("${elasticsearch.rest.hosts}")               //读取ES主机+端口配置
    private String hosts;
    @Value("${elasticsearch.rest.username}")            //读取ES用户名
    private String esUser;
    @Value("${elasticsearch.rest.password}")            //读取ES密码
    private String esPassword;

    @Bean
    public RestHighLevelClient initClient1() { 
        //根据配置文件配置HttpHost数组 
        HttpHost[] httpHosts = Arrays.stream(hosts.split(",")).map( 
                host -> { 
                    //分隔ES服务器的IP和端口 
                    String[] hostParts = host.split(":"); 
                    String hostName = hostParts[0]; 
                    int port = Integer.parseInt(hostParts[1]); 
                    return new HttpHost(hostName, port, HttpHost.DEFAULT_ 
SCHEME_NAME); 
                }).filter(Objects::nonNull).toArray(HttpHost[]::new); 
        //生成凭证 
        final CredentialsProvider credentialsProvider = new BasicCredentials 
Provider(); 
        credentialsProvider.setCredentials(AuthScope.ANY, 
                //明文凭证 
                new UsernamePasswordCredentials(esUser, esPassword)); 
        //返回带验证的客户端 
        return new RestHighLevelClient( 
                RestClient.builder( 
                        httpHosts) 
                        .setHttpClientConfigCallback(new RestClientBuilder. 
HttpClientConfigCallback() { 
                            public HttpAsyncClientBuilder customizeHttpClient 
(HttpAsyncClientBuilder httpClientBuilder) { 
                                httpClientBuilder.disableAuthCaching(); 
                                return httpClientBuilder.setDefaultCredentials 
Provider(credentialsProvider); 
                            } 
                        })); 
    } 
}
```

使用客户端实现 Service

```java
import org.elasticsearch.action.search.SearchRequest;
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.index.query.QueryBuilders;
import org.elasticsearch.rest.RestStatus;
import org.elasticsearch.search.SearchHit;
import org.elasticsearch.search.SearchHits;
import org.elasticsearch.search.builder.SearchSourceBuilder;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

@Service
public class EsService {
    @Autowired
    RestHighLevelClient client;

    public List<Hotel> getHotelFromTitle(String keyword){
        SearchRequest searchRequest=new SearchRequest("hotel");//客户端请求
        SearchSourceBuilder searchSourceBuilder=new SearchSourceBuilder();
        //构建query
        searchSourceBuilder.query(QueryBuilders.matchQuery("title",keyword));
        searchRequest.source(searchSourceBuilder);
        List<Hotel> resultList=new ArrayList<Hotel>();
        try{
            SearchResponse searchResponse=client.search(searchRequest, 
RequestOptions.DEFAULT);
            RestStatus status = searchResponse.status();
            if(status!=RestStatus.OK){
                return null;
            }
            SearchHits searchHits=searchResponse.getHits();
            for(SearchHit searchHit:searchHits){
                // Hotel 是一个数据类
                Hotel hotel=new Hotel();
                hotel.setId(searchHit.getId());                                 //文档_id
                hotel.setIndex(searchHit.getIndex());                           //索引名称
                hotel.setScore(searchHit.getScore());                           //文档得分

                //转换为Map
                Map<String, Object> dataMap= searchHit.getSourceAsMap();
                hotel.setTitle((String) dataMap.get("title"));  //设置标题
                hotel.setCity((String) dataMap.get("city"));    //设置城市
                hotel.setPrice((Double) dataMap.get("price"));  //设置价格
                resultList.add(hotel);
            }
            return resultList;
        }catch (Exception e){
            e.printStackTrace();
        }
        return null;
    }
}
```

## Spring Boot 客户端

Spring Data Elasticsearch 是 Spring Boot 套件中的一个组件，在Spring Boot 中连接 ES 可以使用 Spring Data Elasticsearch。Spring Data Elasticsearch 是 Spring Data 项目的一部分，该项目致力于**提供一致的基于 Spring 的数据查询和存储编程模型**

Spring Data Elasticsearch 封装了创建客户端的逻辑并与服务端保持**长连接**，并且通过对 Repository 接口的自动实现，Spring Data Elasticsearch 可以直接通过方法名的语义实现查询功能，如常见的 findBy + 字段名 + 操作。此外，Spring Data Elasticsearch 还具有 OR-Mapping 功能，即查询到数据后，可以将数据直接封装到自定义的 POJO 中，方便后续对数据进行加工处理

需要使用的依赖

```xml
<dependencies>
    <!-- Spring Boot Web依赖-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--Spring Data Elasticsearch依赖-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    </dependency>
    <!--使用Lombok简化开发-->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
</dependencies>
```

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

### 关闭索引

索引在某一时间内属于冷数据或归档数据，这时可以使用索引的关闭功能

`POST /index_name/_close`

当索引关闭时，只能通过 ES 的 API 或者监控工具看到索引的元数据，但不能写入和搜索数据

### 打开索引

索引关闭后，需要开启读写服务时可以将其设置为打开状态

`POST /index_name/_open`

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
3.   当一个别名指向多个索引时，写入数据的请求不可以指向这个别名，除非在设置时指定了数据写入转发的索引对象

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
            "alias": "myindex_alias",
            // 设置写入转发对象
            "is_write_index": true
        }
    }
    ]
}
```

# 映射操作

在使用数据之前，需要构建数据的组织结构。这种组织结构在关系型数据库中叫作表结构，在 ES 中叫作映射

作为无模式搜索引擎，ES 可以在数据写入时猜测数据类型，从而自动创建映射。但有时 ES 创建的映射中的数据类型和目标类型可能不一致，当需要严格控制数据类型时，还是需要用户手动创建映射

## 查看映射

`GET /index_name/_mapping`

返回信息和建立该索引时信息保持一致

## 扩展映射

映射中的字段类型是不能修改的，但字段可以扩展。最常见的扩展方式是增加字段、为 object 类型的数据新增属性

```json
// 为索引增加 tag 字段
POST /index_name/_mapping
{
    "properties": {
        "tag": {
            "type": "keyword"
        }
    }
}
```

## 基本数据类型

1.   keyword：不进行切分的字符串类型，一般用于文档的过滤、排序和聚合。查询时一般使用 term 查询
2.   text：可进行切分的字符串类型。搜索时使用 match 搜索
3.   数值：ES 支持 long、integer、short、byte、double、float、half_float、scaled_float 和 unsigned_long。为节约存储空间并提升搜索和索引的效率，在满足需求的情况下尽可能选择范围小的数据类型，可用于文档的过滤、排序和聚合。搜索时一般使用 term 或范围搜索
4.   布尔：用于二值表示，写入或查询数据时，使用 true 和 false。查询时使用 term 查询
5.   日期：ES 中存储日期使用标准的 UTC 格式。一般使用如下形式表示日期类型数据：1）格式化的日期字符串；2）毫秒级的长整型，表示从1970年1月1日0点到现在的毫秒数；3）秒级别的整型，表示从1970年1月1日0点到现在的秒数。搜索时一般使用范围查询

## 复杂数据类型

1.   数组：ES 数组没有定义方式，其使用方式是开箱即用的，即无须事先声明，在写入时把数据用中括号 `[]` 括起来，由 ES 对该字段完成定义。如果事先已经定义了字段类型，在写数据时以数组形式写入，ES 也会将该类型转为数组。数组字段的搜索类型取决于数组元素
2.   对象：和数组类型一样，对象类型也不用事先定义，在写入文档的时候 ES 会自动识别并转换为对象类型
3.   地理：该类型的定义需要在 mapping 中指定目标字段的数据类型为 geo_point 类型

## 动态映射

当字段没有定义时，ES 可以根据写入的数据自动定义该字段的类型，这种机制叫作动态映射。数组和对象类型字段就使用了动态映射机制

## 多字段

针对同一个字段，有时需要不同的数据类型，这通常表现在为了不同的目的以不同的方式索引相同的字段

例如，在订单搜索系统中，既希望能够按照用户姓名进行搜索，又希望按照姓氏进行排列，可以在 mapping 定义中将姓名字段先后定义为 text 类型和 keyword 类型，其中，keyword 类型的字段叫作子字段，这样 ES 在建立索引时会将姓名字段建立两份索引，即 text 类型的索引和 keyword 类型的索引

```json
PUT /order 
{ 
  	"mappings": { 
        "properties": { 
            "order_id": {     // 定义order_id字段类型为keyword
                "type": "keyword" 
              }, 
              "user_id": {     // 定义user_id字段类型为keyword 
                "type": "keyword" 
              }, 
              "user_name": {    // 定义user_name字段类型为text 
            	"type": "text", 
                "fields": {     // 定义user_name多字段 
                  // 定义user_name字段的子字段user_name_keyword，并定义其类型为keyword 
                  "user_name_keyword": { 
                    "type": "keyword" 
                  }
                }
              }, 
              "hotel_id": {     // 定义hotel_id字段类型为keyword 
                "type": "keyword" 
              }
        }
    }
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

```java
// Java 创建示例
public void singleIndexDoc(Map dataMap, String indexName, String indexId) { 
    IndexRequest indexRequest = new IndexRequest(indexName).id(indexId).source(dataMap);         //构建IndexRequest对象并设置对应的索引和_id字段名称 
    try { 
        IndexResponse indexResponse = client.index(indexRequest, RequestOptions.DEFAULT); //执行写入 
        //通过IndexResponse获取索引名称 
        String index = indexResponse.getIndex(); 
        String id = indexResponse.getId();//通过IndexResponse获取文档ID 
        //通过IndexResponse获取文档版本 
        Long version = indexResponse.getVersion(); 
        System.out.println("index=" + index + ", + id + ",version=" + version ); 
    } catch (Exception e) { 
    	e.printStackTrace(); 
    } 
} 
```

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

除此之外，还可以使用 Linux 系统的 curl 命令，其支持上传文件，用户可以将批量写入的 JSON 数据保存到文件中，然后使用 curl 命令进行提交

```java
// Java 示例
public void bulkIndexDoc(String indexName, String docIdKey, List> recordMapList) { 
    //构建批量操作BulkRequest对象 
    BulkRequest bulkRequest = new BulkRequest(indexName); 
    for (Ma dataMap: recordMapList) { //遍历数据 
        //获取主键作为ES索引的主键 
        String docId = dataMap.get(docIdKey).toString();
        //构建IndexRequest对象
        IndexRequest indexRequest = new IndexRequest().id(docId).source(dataMap);
        //添加IndexRequest
        bulkRequest.add(indexRequest); 
    }
    
    bulkRequest.timeout(TimeValue.timeValueSeconds(5)); //设置超时时间 
    try { 
        //执行批量写入
        BulkResponse bulkResponse = client.bulk(bulkRequest, RequestOptions.DEFAULT);                                      
        if (bulkResponse.hasFailures()) { //判断执行状态 
            System.out.println("bulk fail,message:" + bulkResponse.buildFailureMessage()); 
        }
    } catch (IOException e) { 
        e.printStackTrace(); 
    }
}
```

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

为提升搜索体验，有时需要给前端传递搜索匹配结果的文档条数，即需要对搜索结果进行计数

```json
GET /<index_name>/_count
{
    "query": {
        "term": {
            "<field>": {
               	"value": "<value>"
            }
        }
    }
}
```

```java
public long getCityCount() { 
    //客户端的count请求 
    CountRequest countRequest=new CountRequest("hotel"); 
    //创建搜索builder 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
    //构建query 
    searchSourceBuilder.query(new TermQueryBuilder("city","北京")); 
    countRequest.source(searchSourceBuilder);   //设置查询 
    try { 
        CountResponse countResponse=client.count(countRequest,RequestOptions.DEFAULT);                              //执行count 
        return countResponse.getCount();       //返回count结果 
    } catch (Exception e) { 
        e.printStackTrace(); 
    } 
    return 0; 
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

### 查询分析

**性能分析**：在使用 ES 的过程中，有的搜索请求的响应可能比较慢，其中大部分的原因是 DSL 的执行逻辑有问题。ES 提供了 profile 功能，该功能详细地列出了搜索时每一个步骤的耗时，可以帮助用户对 DSL 的性能进行剖析

```json
GET /<index_name>/_search
{
    // 打开性能分析
    "profile": true,
    "query": {
        "term": {
            "<field_name>": {
                "value": "<exact_value>"
            }
        }
    }
}
```

需要注意的是，使用 profile 功能是有资源损耗的，建议用户只在前期调试的时候使用该功能，在生产中不要开启 profile 功能

**评分分析**：在使用搜索引擎时，一般都会涉及排序功能。如果用户不指定按照某个字段进行升序或者降序排列，那么 ES 会使用自己的打分算法对文档进行排序。有时我们需要知道某个文档具体的打分详情，以便于对搜索 DSL 问题展开排查。ES 提供了 explain 功能来帮助使用者查看搜索时的匹配详情

```json
GET /<index_name>/_explain/<doc_id>
{
    "query" : {}
}
```

## 删除文档

### 删除单个文档

```json
DELETE /<index_name>/_doc/<document_id>
```

```java
// Java 删除示例
public void singleDelete(String index, String docId) { 
    //构建删除请求 
    DeleteRequest deleteRequest=new DeleteRequest(index,docId); 
    try { 
        client.delete(deleteRequest, RequestOptions.DEFAULT);//执行删除 
    } catch (IOException e) { 
        e.printStackTrace(); 
    } 
}
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

     ```java
     public void bulkDelete(String index, String docIdKey, List docIdList) {  
         BulkRequest bulkRequest = new BulkRequest();    //构建BulkRequest对象 
         for (String docId : docIdList) {            //遍历文档_id列表 
             //构建删除请求 
             DeleteRequest deleteRequest=new DeleteRequest(index,docId); 
             bulkRequest.add(deleteRequest);        //创建UpdateRequest对象 
         }
         
         try { 
             //执行批量删除
             BulkResponse bulkResponse = client.bulk(bulkRequest,RequestOptions.DEFAULT); 
             if (bulkResponse.hasFailures()) {      //判断状态 
                 System.out.println("bulk fail,message:" + bulkResponse.buildFailureMessage());
             } 
         } catch (Exception e) { 
             e.printStackTrace(); 
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
     
     ```java
     public void deleteByQuery(String index,String city) {  
         //构建DeleteByQueryRequest对象 
         DeleteByQueryRequest deleteByQueryRequest=new DeleteByQueryRequest(index); 
         //设置按照城市查找文档的query 
         deleteByQueryRequest.setQuery(new TermQueryBuilder("city",city)); 
         try { 
             //执行删除命令 
             client.deleteByQuery(deleteByQueryRequest,RequestOptions.DEFAULT); 
         } catch (IOException e) { 
             e.printStackTrace(); 
         }
     }
     ```

## 更新文档

### 更新单个文档

更新操作通过 _update API 执行，该接口允许部分更新现有文档的字段

```json
POST /<index_name>/_update/<document_id>
{
    "doc": {
        "<field>": "<value>"
    }
}
```

```java
// Java 更新示例
public void singleUpdate(String indexName, String docIdKey, Map recordMap) { 
    UpdateRequest updateRequest = new UpdateRequest(indexName, docIdKey); 
    updateRequest.doc(recordMap); 
    try { 
        UpdateResponse updateResponse=client.update(updateRequest, RequestOptions.DEFAULT); 
        //通过IndexResponse获取索引名称 
        String index = updateResponse.getIndex(); 
        //通过IndexResponse获取文档ID 
        String id = updateResponse.getId(); 
        //通过IndexResponse获取文档版本 
        Long version = updateResponse.getVersion(); 
        System.out.println("index=" + index + ", + id + ", version=" +version); 
    } catch (IOException e) { 
        e.printStackTrace(); 
    } 
}
```

除了普通的 update 功能，ES 还提供了 upsert。upsert 是 update 和 insert 的合体字，表示更新/插入数据。如果目标文档存在，则执行更新逻辑；否则执行插入逻辑

```json
POST /<index_name>/_update/<document_id>
{
    "doc": {
        "<field>": "<value>"
    },
    "upsert": {
        "<field>": "<value>"
    }
}
```

```java
// Java upsert 示例
public void singleUpsert(String index, String docIdKey, Map recordMap,Map upRecordMap) { 
    //构建UpdateRequest 
    UpdateRequest updateRequest = new UpdateRequest(index, docIdKey); 
    updateRequest.doc(recordMap);        //设置更新逻辑 
    updateRequest.upsert(upRecordMap);   //设置插入逻辑 
    try { 
        //执行upsert命令 
        client.update(updateRequest, RequestOptions.DEFAULT); 
    } catch (IOException e) { 
        e.printStackTrace(); 
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
     ```java
    // Java 示例
    public void bulkUpdate(String index, String docIdKey, List> recordMapList) { 
        BulkRequest bulkRequest = new BulkRequest();//构建BulkRequest对象 
        for (Map dataMap : recordMapList) {//遍历数据列表 
            String docId = dataMap.get(docIdKey).toString(); 
            dataMap.remove(docId);            //将ID字段从map中删除 
            //创建UpdateRequest对象 
            bulkRequest.add(new UpdateRequest(index, docId).doc(dataMap)); 
        }

        try {
            //执行批量更新
            BulkResponse bulkResponse = client.bulk(bulkRequest, RequestOptions.DEFAULT); 
            if (bulkResponse.hasFailures()) { //判断状态 
                System.out.println("bulk fail,message:" + bulkResponse.buildFailureMessage()); 
            } 
        } catch (Exception e) { 
            e.printStackTrace(); 
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
     
     ```java
     // Java 示例
     public void updateCityByQuery(String index, String oldCity, String newCity) { 
         //构建UpdateByQueryRequest对象
         UpdateByQueryRequest updateByQueryRequest=new UpdateByQueryRequest(index); 
         //设置按照城市查找文档的query 
         updateByQueryRequest.setQuery(new TermQueryBuilder("city",oldCity)); 
         updateByQueryRequest.setScript(new Script("ctx._source['city']= '"+newCity+"';"));      //设置更新城市字段的脚本逻辑 
         try { 
             client.updateByQuery(updateByQueryRequest,RequestOptions.DEFAULT);              //执行更新 
         } catch (IOException e) { 
             e.printStackTrace(); 
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

```java
public void printResult(SearchRequest searchRequest) { 
    try {
        // 执行搜索
        SearchResponse searchResponse = client.search(searchRequest, RequestOptions.DEFAULT);
        SearchHits searchHits = searchResponse.getHits(); //获取搜索结果集 
        for (SearchHit searchHit : searchHits) {          //遍历搜索结果集 
            String index = searchHit.getIndex();          //获取索引名称 
            String id = searchHit.getId();                //获取文档_id 
            Float score = searchHit.getScore();           //获取得分 
            String source = searchHit.getSourceAsString(); //获取文档内容 
            //打印数据 
            System.out.println("index=" + index + ", + id= " id + "score= " + score + ",source=" + source);
        }
    } catch (Exception e) { 
        e.printStackTrace(); 
    } 
}

public void matchAllSearch() { 
    // 新建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel");  
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    // 新建match_all查询，并设置boost值为2.0
    MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery().boost(2.0f); 
    searchSourceBuilder.query(matchAllQueryBuilder); 
    searchRequest.source(searchSourceBuilder);    //设置查询 
   printResult(searchRequest);                    //打印结果 
}
```

可以在 `match_all` 查询中添加额外的参数来控制搜索结果的显示，例如设置返回的文档数量（size）、开始返回的文档位置（from）、排序规则（sort）以及选择返回哪些字段（_source）

`_source` 示例

```json
// 不查看源数据，仅查看元字段
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    },
    "_source": false
}

// 返回指定字段
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    },
    "_source": ["field1", "field2"]
}

// 返回符合 pattern 的字段，如以 obj. 开头的字段
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    },
    "_source": "obj.*"
}
```

```java
// Java 返回指定字段示例，等价于 _source
SearchRequest searchRequest = new SearchRequest("hotel"); //客户端请求 
//创建搜索builder 
SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
//构建query 
searchSourceBuilder.query(new TermQueryBuilder("city","北京")); 
//设定希望返回的字段数组 
searchSourceBuilder.fetchSource(new String[]{"title","city"} , null); 
searchRequest.source(searchSourceBuilder);
```

使用 `from` 和 `size` 定义搜索位置和每页显示的文档数量，从而实现分页显示。默认情况下，用户最多可以取得 10000 个文档，即 from 为 0 时，size 参数最大为 10000，如果请求超过该值，ES 会返回请求数量过多的错误信息

```json
GET /<index_name>/_search
{
    // 设置起始位置
    "from": 0,
    // 设置返回数量
    "size": 20,
    "query": {
        "match_all": {}
    }
}
```

```java
SearchRequest searchRequest = new SearchRequest("hotel"); //客户端请求 
//创建搜索的builder 
SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
//构建query 
searchSourceBuilder.query(new TermQueryBuilder("city","北京")); 
searchSourceBuilder.from(20);      //设置from参数 
searchSourceBuilder.size(10);      //设置size参数 
searchRequest.source(searchSourceBuilder);
```

注意：ES 不适合**深翻页**，即请求的 from 值很大。当深翻页的请求过多时会增加各个分片所在节点的内存和 CPU 消耗。尤其是协调节点，随着页码的增加和并发请求的增多，该节点需要对这些请求涉及的分片数据进行汇总和排序，过多的数据会导致协调节点资源耗尽而停止服务

### 排序

在默认情况下，ES 对搜索结果是按照相关性降序排序的。有时需要按照某些字段的值进行升序或者降序排序

ES 提供了 sort 子句可以对数据进行排序。使用 sort 子句一般是按照字段信息进行排序，不受相关性影响，而且打分步骤需要耗费一定的硬件资源和时间，因此默认情况下，不对文档进行打分

#### 按普通字段值排序

```json
// 返回索引中前 10 个文档，按照文档评分进行排序
GET /<index_name>/_search
{
    "query": {
        "match_all": {}
    },
    "size": 10,
    "sort": [
        {
            "_score": {
                "order": "desc"
            }
        }
    ]
}
```

```java
// Java 示例
public void commonSort(){  
    //创建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    //构建match查询 
    searchSourceBuilder.query(QueryBuilders.matchQuery("title","金都")); 
    searchRequest.source(searchSourceBuilder);        //设置查询请求 
    //设置按照价格进行降序排序 
    searchSourceBuilder.sort("price", SortOrder.DESC); 
    //设置按照口碑值进行降序排序 
    searchSourceBuilder.sort("praise", SortOrder.DESC); 
   printResult(searchRequest);                        //打印搜索结果 
}
```

#### 按地理距离排序

使用 geo_distance 查询，配合 sort 可以指定另一种排序规则，即按照文档坐标与指定坐标的距离对结果进行排序

使用时，需要在 sort 内部指定排序名称为 _geo_distance，并指定目的地坐标。除了可以指定升序或者降序排列外，还可以指定排序结果中 sort 子句中的距离的计量单位，默认值为 km

在进行距离计算时，系统默认使用的算法为 arc，该算法的特点是计算精准但是耗费时间较长，用户可以使用 distance_type 参数选择另一种计算速度快但经度略差的算法，名称为 plane

```json
GET /hotel/_search 
{ 
    "_source": [                         //返回部分字段 
        "title", 
        "city", 
        "location" 
    ], 
    "query": { 
        "geo_distance": { 
              "distance": "5km",             //设置地理范围为5km 
              "location": {                  //设置中心点坐标 
                    "lat": "39.915143", 
                    "lon": "116.4039" 
              } 
        } 
    }, 
    "sort": [                           //设置排序逻辑 
    { 
        "_geo_distance": { 
            "location": {                //设置排序的中心点坐标 
                  "lat": "39.915143", 
                  "lon": "116.4039" 
            }, 
            "order": "asc",             //按距离由近到远进行排序 
            "unit": "km",               //排序所使用的距离的计量单位 
            "distance_type": "plane"  //排序所使用的距离计算算法 
        } 
    } 
    ] 
}
```

```java
// Java 示例
public void geoDistanceSearchSort() throws IOException {  
    //创建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    //创建geo_distance查询，搜索距离中心点5km范围内的酒店 
    searchSourceBuilder.query(QueryBuilders.geoDistanceQuery("location").distance(5, DistanceUnit.KILOMETERS).point(39.915143, 116.4039)); 
    //创建geo_distance_sort排序，设定按照与中心点的距离进行升序排序 
    GeoDistanceSortBuilder geoDistanceSortBuilder = SortBuilders.geoDistanceSort("location", 39.915143, 116.4039).point(39.915143, 116.4039).unit(DistanceUnit.KILOMETERS).order(SortOrder.ASC); 
    searchSourceBuilder.sort(geoDistanceSortBuilder);  //设置排序规则 
    searchRequest.source(searchSourceBuilder);         //设置查询 
    //开始搜索 
    SearchResponse searchResponse = client.search(searchRequest, RequestOptions.DEFAULT); 
    SearchHits searchHits = searchResponse.getHits();  //获取搜索结果 
    System.out.println("search result distance sort:");
    
    //开始遍历搜索结果 
    for (SearchHit searchHit : searchHits) { 
        //得到酒店距离中心点的距离 
        double geoDistance = (double) searchHit.getSortValues()[0];
        //以Map形式获取文档_source内容 
        Map sourceMap = searchHit.getSourceAsMap(); 
        Object title = sourceMap.get("title"); 
        Object city = sourceMap.get("city"); 
        //打印结果 
        System.out.println("title=" + title + ",city=" + city + ",geoDistance:" + geoDistance); 
    }
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

```java
// 日期类型的 term 查询，其余类型查询可通过 API 调用完成
public void termDateSearch() { 
    // 创建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
    // 构建term查询
    searchSourceBuilder.query(QueryBuilders.termQuery("create_time", "20210509160000"));
    searchRequest.source(searchSourceBuilder);  // 设置查询请求 
   printResult(searchRequest);                  // 打印搜索结果 
}
```

处理多值字段时，term 查询是包含，即查找数组中是否包含匹配项

term 查询对输入不做分词，即会将输入作为一个整体，在倒排索引中查找准确的词项。因此不需要使用相关度算分公式为每个包含该词项的文档进行相关度算分，可以通过 constant_score 将查询转换成一个 filtering，避免算分，并利用缓存，提高性能。同理，constant_score 中 boost 参数可以控制命中文档的得分，默认为 1.0

-   ﻿将 query 转成 filter，忽略 TF-IDF 计算，避免相关性算分的开销
-   ﻿﻿filter 可以有效利用缓存

```json
// constant_score 示例
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

```java
// Java constant_score 示例
public void constantScoreSearch() {     
    //新建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    //构建设施包含“停车场”的constant score查询 
    ConstantScoreQueryBuilder constantScoreQueryBuilder = new ConstantScore QueryBuilder(QueryBuilders.termQuery("amenities", "停车场")); 
    searchSourceBuilder.query(constantScoreQueryBuilder); 
    constantScoreQueryBuilder.boost(2.0f); 
    searchRequest.source(searchSourceBuilder);  //设置查询 
   	printResult(searchRequest);                  //打印结果 
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

```java
// Java terms 查询示例
public void termsSearch() { 
    //创建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    //构建terms查询 
    searchSourceBuilder.query(QueryBuilders.termsQuery("city","北京","天津")); 
    searchRequest.source(searchSourceBuilder);  //设置查询请求 
   printResult(searchRequest);                  //打印搜索结果 
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

```java
// Java 范围查询示例，日期类型要注意格式是否符合 mappings 定义
public void rangeSearch() { 
    //创建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    //构建range查询 
    QueryBuilder queryBuilder = QueryBuilders.rangeQuery("create_time").gte("20210115120000").lte("20210116120000");
    searchSourceBuilder.query(queryBuilder); 
    searchRequest.source(searchSourceBuilder);   //设置查询请求 
    printResult(searchRequest);                  //打印搜索结果 
}
```

### exists 存在查询

用于筛选具有特定字段值的文档。这种查询类型适用于检查文档中是否存在某个字段，或者该字段是否包含非空值。通过使用 exists 检索，你可以有效地过滤掉缺少关键信息的文档，从而专注于包含所需数据的结果。应用场景包括但不限于数据完整性检查、查询特定属性的文档以及对可选字段进行筛选等

在某些场景下，我们希望找到某个字段不为空的文档，则可以用 exists 搜索。字段不为空的条件有：

-   值存在且不是 null
-   值不是空数组
-   值是数组，但不是 [null]

```json
GET /<index_name>/_search
{
    "query": {
       "exists": {
           "field": "<field_name>"
       }
    }
}
```

```java
// Java exists 查询示例
public void existsSearch(){ 
    SearchRequest searchRequest=new SearchRequest("hotel_1"); 
    SearchSourceBuilder searchSourceBuilder=new SearchSourceBuilder(); 
    searchSourceBuilder.query(QueryBuilders.existsQuery("tag")); 
    searchRequest.source(searchSourceBuilder); 
    printResult(searchRequest); 
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
               // AUTO 为 [0, 1, 2]，即表示编辑距离不超过2
               "fuzziness": "AUTO",
           	   // 搜索词的前缀长度，表示在此长度内不会应用模糊匹配
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
               // 自定义脚本，用于动态计算匹配数量
               "minimum_should_match_script": {
                   "source": "<script>"
               }
               // 也可以静态指定需要匹配的数量
               // "minimum_should_match": "<value>"
    		   // 也可以静态指定需要匹配的字段
               // "minimum_should_match_field": "<name>"
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

// 使用 operator 规定查询词之间的关系
GET /<index_name>/_search
{
    "query": {
       "match": {
           "<field_name>": {
               "query": "<query_str>",
               "operator": "or",
               // 最小匹配参数，其值为一个数值，意义为可以匹配上的词的个数
               "minimum_should_match": <value>
           }
       }
    }
}
```

```java
// Java match 查询示例
public void matchSearch() { 
    SearchRequest searchRequest = new SearchRequest();  //新建搜索请求 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    //新建match查询，并设置operator值为and
    searchSourceBuilder.query(QueryBuilders.matchQuery("title", "金都").operator(Operator.AND));
    searchRequest.source(searchSourceBuilder);         //设置查询 
   	printResult(searchRequest);                         //打印结果 
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

```java
// Java multi_match 查询示例
public void multiMatchSearch() { 
    SearchRequest searchRequest = new SearchRequest();  //新建搜索请求 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    // 新建multi_match查询，从"title"和"amenities"字段查询"假日"
    searchSourceBuilder.query(QueryBuilders.multiMatchQuery("假日", "title", "amenities")); 
    searchRequest.source(searchSourceBuilder);    //设置查询 
   	printResult(searchRequest);                    //打印结果 
}
```

### match_phrase 短语查询

用于执行短语搜索，它不仅匹配整个短语，而且还考虑了短语中各个词的**顺序和位置**。这种查询类型对于搜索**精确短语**非常有用，尤其是在用户输入的查询与文档中的文本表达方式需要严格匹配时

```json
GET /<index_name>/_search
{
    "query": {
       "match_phrase": {
           "<field_name>": {
               "query": "<phrase>",
               // 匹配查询词条可间隔的距离
               "slop": <value>
           }
       }
    }
}
```

```java
// Java 短语查询示例
public void matchPhraseSearch() { 
    SearchRequest searchRequest = new SearchRequest(); //新建搜索请求 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    //新建match_phrase查询，并设置slop值为2 
    QueryBuilder matchPhraseQueryBuilder=QueryBuilders.matchPhraseQuery("title","文雅酒店").slop(2); 
    searchSourceBuilder.query(matchPhraseQueryBuilder); 
    searchRequest.source(searchSourceBuilder);  //设置查询 
   	printResult(searchRequest);                  //打印结果 
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
| must     | 与查询，可包含多个查询条件，每个条件均满足的文档才能被搜索到，每次查询需要计算相关度得分，属于搜索上下文 |
| should   | 或查询，可包含多个查询条件，不存在 must 和 filter 条件时，至少要满足多个查询条件中的一个，文档才能被搜索到，否则需满足的条件数量不受限制，匹配到的查询越多相关度越高，也属于搜索上下文 |
| must_not | 非查询，可包含多个过滤条件，每个条件均不满足的文档才能被搜索到，每个过滤条件不计算相关度得分，结果在一定条件下会被缓存，属于过滤上下文 |
| filter   | 可包含多个过滤条件，每个条件均满足的文档才能被搜索到，每个过滤条件不计算相关度得分，结果在一定条件下会被缓存，属于过滤上下文 |

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

```java
// Java must 示例
public void mustSearch(){ 
    SearchRequest searchRequest=new SearchRequest("hotel"); //新建请求 
    SearchSourceBuilder searchSourceBuilder=new SearchSourceBuilder(); 
    BoolQueryBuilder boolQueryBuilder=QueryBuilders.boolQuery(); 
    //构建城市term查询 
    TermQueryBuilder termQueryIsReady=QueryBuilders.termQuery("city","北京");
    //构建价格range查询
    RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("price").gte(350).lte(400);                               
    //进行关系“与”查询 
    boolQueryBuilder.must(termQueryIsReady).must(rangeQueryBuilder); 
    searchSourceBuilder.query(boolQueryBuilder); 
    searchRequest.source(searchSourceBuilder);  //设置查询 
   	printResult(searchRequest);                  //打印结果 
}
```

```java
// Java should 示例
public void shouldSearch() { 
    //新建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery(); 
    //构建城市为“北京”的term查询 
    TermQueryBuilder termQueryIsReady = QueryBuilders.termQuery("city", "北京"); 
    //构建城市为“天津”的term查询 
    TermQueryBuilder termQueryWritter = QueryBuilders.termQuery("city", "天津"); 
    //进行关系“或”查询 
    boolQueryBuilder.should(termQueryIsReady).should(termQueryWritter); 
    searchSourceBuilder.query(boolQueryBuilder); 
    searchRequest.source(searchSourceBuilder);   //设置查询 
   	printResult(searchRequest);                   //打印结果 
}
```

```java
// Java must not 示例
public void mustNotSearch() { 
    //新建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery(); 
    //构建城市为“北京”的term查询 
    TermQueryBuilder termQueryIsReady = QueryBuilders.termQuery("city", "北京"); 
    //构建城市为“天津”的term查询 
    TermQueryBuilder termQueryWritter = QueryBuilders.termQuery("city", "天津"); 
    //进行关系“必须不”查询 
    boolQueryBuilder.mustNot(termQueryIsReady).mustNot(termQueryWritter); 
    searchSourceBuilder.query(boolQueryBuilder); 
    searchRequest.source(searchSourceBuilder);     //设置查询 
   	printResult(searchRequest);                     //打印结果 
}
```

```java
// Java filter 示例
public void filterSearch() { 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery(); 
    boolQueryBuilder.filter( QueryBuilders.termQuery("city", "北京")); 
    boolQueryBuilder.filter(QueryBuilders.termQuery("full_room", false)); 
    searchSourceBuilder.query(boolQueryBuilder); 
    searchRequest.source(searchSourceBuilder); 
    printResult(searchRequest); 
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

```java
// Java 高亮示例
public void hightLightSearch() {     
    SearchRequest searchRequest = new SearchRequest();  //新建搜索请求 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
    //新建match查询，并设置operator的值为and
    searchSourceBuilder.query(QueryBuilders.matchQuery("title", "金都").operator(Operator.AND)); 
    searchRequest.source(searchSourceBuilder);  //设置查询 
    //新建高亮显示搜索 
    HighlightBuilder highlightBuilder = new HighlightBuilder(); 
    highlightBuilder.preTags("");        //设置高亮显示标签前缀 
    highlightBuilder.postTags("");      //设置高亮显示标签后缀 
    highlightBuilder.field("title");           //设置高亮显示字段 
    searchSourceBuilder.highlighter(highlightBuilder); //设置高亮显示搜索 
    try { 
        SearchResponse searchResponse = client.search(searchRequest, RequestOptions.DEFAULT);                       //执行搜索 
        SearchHits searchHits = searchResponse.getHits();  //获取搜索结果集
        
        for (SearchHit searchHit : searchHits) { //遍历搜索结果集
            //得到高亮显示搜索结果
            Text[] texts = searchHit.getHighlightFields().get("title").getFragments(); 
            for (Text text : texts) {          //遍历高亮显示搜索 
                System.out.println(text);      //打印每一个高亮显示结果
            } 
            System.out.println("--------------------------------------"); 
        } 
    } catch (Exception e) { 
        e.printStackTrace(); 
    } 
}
```

ES 支持的高亮显示搜索策略有 plain、unified 和 fvh，用户可以根据搜索场景进行选择

选择高亮显示搜索策略时，只需要在 highlight 子句中指定 type 字段的值即可

### plain

plain 是精准度比较高的策略，因此它必须将文档全部加载到内存中，并重新执行查询分析。由此可见，plain 策略在处理大量文档或者大文本的索引进行多字段高亮显示搜索时耗费的资源比较严重。因此 plain 策略适合在**单个字段**上进行简单的高亮显示搜索

### unified

unified 策略是由 Lucene Unified Highlighter 来实现的，其使用 BM25（Best Match25）算法进行匹配。在默认情况下，ES 高亮显示使用的是该策略

### fvh

上述两种策略在大文本索引高亮显示搜索时速度低，Lucene 还提供了基于向量的高亮显示搜索策略 fvh（fast vector highlighter）

fvh 策略更适合在文档中包含**大字段**的情况（如超过1MB）下使用，如果计算机的 I/O 性能更好（如使用 SSD），则 fvh 策略在速度上的优势更加明显

如果要使用 fvh 策略进行高亮显示搜索，需要设定字段的 term_vector 属性值为 with_positions_offsets

## 地理空间查询

ElasticSearch 中地理空间数据通常存储在 geo_point 字段类型中，其包含经纬度坐标，用于表示地球上的一个点

允许用户基于地理位置信息来搜索和过滤数据。在 Elasticsearch 这样的全文搜索引擎中，地理空间位置查询被广泛应用，例如在旅行、房地产、物流和零售等行业，用于提供基于位置的搜索功能

geo_distance 查询方式需要用户指定一个坐标点，在指定距离该点的范围后，ES 即可查询到相应的文档

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

```java
// Java geo_distance 示例
public void geoDistanceSearch() { 
    // 新建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    // 新建geo_distance查询，设置基准点坐标和周边距离 
    searchSourceBuilder.query(QueryBuilders.geoDistanceQuery("location").distance(5, DistanceUnit.KILOMETERS).point(40.026919, 116.47473)); 
    searchRequest.source(searchSourceBuilder);     // 设置查询 
   	printResult(searchRequest);                    // 打印结果
}
```

geo_shape 查询提供的是矩形内的搜索，需要用户给出左上角的顶点地理坐标和右下角的顶点地理坐标

```json
GET /my_index/_search
{
    "query": {
        "bool": {
            "must": {
                "match_all": {}
            },
            "filter": {
                "geo_bounding_box": {
                    "location": {
                        "top_left": {
                            "lat": 39.922,
                            "lon": 116.457
                        },
                        "bottom_right": {
                            "lat": 39.907,
                            "lon": 116.479
                        }
                    }
                }
            }
        }
    }
}
```

geo_polygon 比 geo_shape 提供的地理范围功能更加灵活，它支持多边形内的文档搜索，使用该查询需要提供多边形所有顶点的地理坐标

```json
GET /my_index/_search
{
    "query": {
        "bool": {
            "must": {
                "match_all": {}
            },
            "filter": {
                "geo_polygon": {
                    "location": {
                        points: [
                            {
                                "lat": 39.959,
                                "lon": 116.417
                            },
                            {
                                "lat":39.962,
                                "lon":116.432
                            },
                            {
                                "lat": 39.965,
                                "lon": 116.421
                            }
                        ]
                    }
                }
            }
        }
    }
}
```

```java
// Java geo_poly 示例
public void geoPolygonSearch() { 
    // 新建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    // 新建geo_distance查询，设置基准点坐标和周边距离 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    // 新建多边形顶点列表 
    List geoPointList = new ArrayList(); 
    // 添加多边形顶点 
    geoPointList.add(new GeoPoint(39.959829, 116.417088)); 
    geoPointList.add(new GeoPoint(39.960272, 116.432035)); 
    geoPointList.add(new GeoPoint(39.965802, 116.421399));
    // 新建geo_polygon查询
    searchSourceBuilder.query(QueryBuilders.geoPolygonQuery("location", geoPointList)); 
    searchRequest.source(searchSourceBuilder);  //设置查询 
    printResult(searchRequest);                  //打印结果 
}
```

## 搜索建议

搜索建议，即在用户输入搜索关键词的过程中系统进行自动补全，用户可以根据自己的需求单击搜索建议的内容直接进行搜索。在搜索时，用户每输入一个字符，前端就需要向后端发送一次查询请求对匹配项进行查询，因此这种场景对后端响应速度的要求比较高。通过协助用户进行搜索，可以避免用户输入错误的关键词，引导用户使用更合适的关键词，提升用户的搜索体验和搜索效率

ES 使用 Completion Suggester 实现搜索建议查询

注意：为了使用 Completion Suggester，其对应的字段类型需要定义为 completion 类型

```json
GET /my_index/_search
{
    "suggest": {
        "<name>": {
            "prefix": "<value>",
            "completion": {
                "field": "<field_name>"
            }
        }
    }
}
```

和普通搜索不同的是，搜索建议的结果不是封装在 hits 中，而是单独封装在 suggest 中

```java
// Java suggest 示例
public void suggestSearch() throws IOException { 
    //创建搜索请求,指定索引名称为hotel_sug 
    SearchRequest searchRequest = new SearchRequest("hotel_sug"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    //创建completion类型的搜索建议 
    CompletionSuggestionBuilder comSuggest = SuggestBuilders.completionSuggestion("query_word").prefix("如家"); 
    SuggestBuilder suggestBuilder = new SuggestBuilder(); 
    //添加搜索建议，"hotel_zh_sug"为自定义名称 
    suggestBuilder.addSuggestion("hotel_zh_sug", comSuggest); 
    searchSourceBuilder.suggest(suggestBuilder); //设置搜索建议请求 
    searchRequest.source(searchSourceBuilder);   //设置查询请求 
    SearchResponse response = client.search(searchRequest, RequestOptions.DEFAULT);       //进行搜索,获取搜索结果 
    CompletionSuggestion suggestion = response.getSuggest().getSuggestion("hotel_zh_sug");       //获取搜索建议结果 
    System.out.println("sug result:"); 
    //遍历搜索建议结果并进行打印 
    for (CompletionSuggestion.Entry.Option option : suggestion.getOptions()) { 
        System.out.println("sug:" + option.getText().string()); 
    }
}
```

**注意**：ES 提供的 Completion Suggester 功能使用的索引结构不是倒排索引，而是在内存中构建 **FST（Finite StateTransducers）**。构建该数据结构是有比较大的内存存储成本的，因此在生产环境中向索引中添加数据时一定要关注 ES 节点的内存消耗，避免数据量过大造成 ES 节点内存耗尽从而影响集群服务

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
	"fields": ["<field1>", "<field2>"]
}
```

# 分析器

分析器一般用在下面两个场景中：

-   创建或更新文档时（合称索引时），对相应的文本字段进行分词处理
-   查询文本字段时，对查询语句进行分词

分析器结构遵循**三段式**原则，即字符过滤器、分词器和词语过滤器。其中，字符过滤器可以有 0 个或多个，分词器必须只有一个，词语过滤器可以有 0 个或多个

ES 默认使用 standard 分析器，由 standard分词器、Lower Case 分词过滤器和 Stop Token 分词过滤器构成的

ES 内置的分析器

| 名称              | 功能                                             |
| ----------------- | ------------------------------------------------ |
| simple 分析器     | 按非字母字符进行词语拆分，并将所有词语转换为小写 |
| language 分析器   | 语言分析器                                       |
| whitespace 分析器 | 按照空白字符拆分词语                             |
| pattern 分析器    | 使用正则表达式将文本拆分为词语                   |

## 字符过滤器

字符过滤器是分析器处理文本数据的第一道工序，它接收原始的字符流，对原始字符流中的字符进行添加、删除或者转换操作，进而改变原始的字符流

ES 常用字符过滤器

| 名称                 | 功能                       |
| -------------------- | -------------------------- |
| 映射关系字符过滤器   | 根据配置的映射关系替换字符 |
| HTML 擦除过滤器      | 去掉 HTML 元素             |
| 正则表达式替换过滤器 | 用正则表达式处理字符串     |

## 分词器

分词器在分析器中负责非常重要的一环工作，即按照规则来切分词语

对于英文来说，简单的分词器通常是根据空格及标点符号进行切分。然而对于中文分词来说，字符之间往往没有空格，因此采用英文的切分规则是不可取的

分词器对文本进行切分后，需要保留词语与原始文本之间的对应关系，因此分词器还负责记录每个 Token 的位置，以及开始和结束的字符偏移量

ES 常用分词器

| 名称       | 功能                                                     |
| ---------- | -------------------------------------------------------- |
| 标准分词器 | 对英文分词时，基于语法分词；对中文分词时，切分成单字     |
| 字母分词器 | 使用非字母的字符作为分词标记                             |
| 小写分词器 | 功能上等同于字母分词器，并且把所有分词结果转换为小写形式 |
| 空格分词器 | 使用空格作为分词标记                                     |

## 分词过滤器

分词过滤器接收分词器的处理结果，并可以将切分好的词语进行加工和修改，进而对分词结果进行规范化、统一化和优化处理

ES 常用分词过滤器

| 名称              | 功能                     |
| ----------------- | ------------------------ |
| Lower Case 过滤器 | 将所有字母转换为小写形式 |
| Stop Token 过滤器 | 将停用词从分词结果中移除 |
| 同义词分词过滤器  | 为分词结果添加同义词     |

## analyze

ES 提供了分析 API 帮助理解分析器的运行结果。在 DSL 中可以直接使用参数 analyzer 来指定分析器的名称进行测试

```json
POST _analyze
{
    "analyzer": "<analyzer_name>",
    "text": "<test_txt>"
}
```

分析 API 返回信息的参数包含

-   token：文本被切分为词语后的某个词语
-   start_offset：该词在文本中的起始偏移位置
-   end_offset：该词在文本中的结束偏移位置
-   type：词性，各个分词器的值不一样
-   position：分词位置，指明该词语在原文本中是第几个出现的

## 自定义分析器

在有些场景中，某个文本字段不是自然语言而是在某种规则下的编码。当系统内置的分析器不满足需求时，用户可以使用自定义分析器

首先，需要在索引创建的 DSL 中定义分析器 custom_analyzer，该分析器中只有一个分词组件，该分词组件使用逗号进行词语切分；然后在 mappings 中使用 analyzer 参数指定字段（sup_env）的分析器为定义好的 custom_analyzer 分析器

```json
PUT /hotel 
{ 
    "settings": { 
        "analysis": { 
            "analyzer": { 
                "custom_analyzer": {                  //自定义分析器 
                    "tokenizer": "comma_tokenizer"     //使用comma_tokenizer分词器 
                } 
            }, 
            "tokenizer": {         //定义分词器 
                "comma_tokenizer": { 
                      "type": "pattern", 
                      "pattern": ","     //指定切分时使用的分隔符 
                } 
            } 
        } 
    }, 
    "mappings": { 
        "properties": { 
            "title": { 
                "type": "text", 
                "analyzer": "whitespace",  //设定title字段索引时使用whitespace分析器 
                //设定title字段搜索时使用whitespace分析器 
                "search_analyzer": "whitespace" 
            }, 
            "sup_env": { 
                "type": "text", 
                //设置sup_env字段使用comma_analyzer分析器 
                "analyzer": "comma_analyzer" 
            } 
        } 
    } 
}
```

## 中文分析器

对于中文来说，一般由一个或多个字组合在一起形成一个词语，并且句子中没有词的界限。根据不同的使用场景，对于词语切分颗粒度的需求也是不一样的

中文分词根据实现原理和特点，分词的切分算法主要有两种，

1.   基于词典的分词算法：按照某种策略将提前准备好的词典和待匹配的字符串进行匹配，当匹配到词典中的某个词时，说明该词分词成功。该算法是匹配算法中最简单、速度最快的算法，其分词算法分为 3 种，即正向最大化匹配法、逆向最大化匹配法和双向最大化匹配法
2.   基于统计的机器学习算法：事先构建一个语料库，该语料库中是标记好的分词形式的语料，然后统计每个词出现的频率或者词与词之间共现的频率等，基于统计结果给出某种语境下应该切分出某个词的先验概率，后续进行分词时，使用先验概率给出文本应该切分的结果。这类算法中代表的算法有 HMM、CRF、深度学习等，比如结巴分词基于 HMM 算法、HanLP 分词工具基于 CRF 算法等

中文分词的难点：

-   分词标准：不同的分词器使用的分词标准不同，分词的结果也不同
-   分词歧义：使用分词器对文本进行切分，切分后的结果和原来的字面意义不同
-   新词识别：新词也称未登录词，即该词没有在词典或者训练语料中出现过。在这种情况下，分词器很难识别出该词

### IK 分析器

IK 分析器是一个开源的、基于 Java 语言开发的轻量级的中文分词工具包，它提供了多种语言的调用库。其实现了词典的冷更新和热更新，用户可以选择适合自己的方式进行词典的更新

IK 分析器提供了两个子分析器，即 ik_smart 和 ik_max_word，另外它还提供了两个和分析器同名的子分词器

ik_max_word 和 ik_smart 分析器的主要区别在于切分词语的粒度上，ik_smart 的切分粒度比较粗，而 ik_max_word 将文本进行了最细粒度的拆分，甚至穷尽了各种可能的组合

使用 IK 分析器还可以在其安装目录下的 config 文件夹中创建 my.dict 文件自定义添加词典，并在添加完成后修改 IK 分析器的配置文件，路径为 config/IKAnalyzer.cfg.xml ，将新建的字典文件加入 ext_dict 选项中使其生效

### HanLP 分析器

HanLP 是由一系列模型与算法组成的 Java 工具包，它从中文分词开始，覆盖词性标注、命名实体识别、句法分析、文本分类等常用的 NLP 任务，提供了丰富的 API，被广泛用于 Lucene、Solr 和 ES 等搜索平台。HanLP 支持最短路分词、N-最短路分词和 CRF 分词等分词算法

HanLP 分析器提供了众多的子分析器，如 hanlp、hanlp_standard、hanlp_crf 和 hanlp_n_short 等

同样也可以给 HanLP 分析器添加用户自定义词典。在 \${ES_HOME}/plugins/analysis-hanlp/data/dictionary/custom 目录中新建字典文件 my.dict，然后在该文件中添加需要的词语（当有多个词语需要添加时，每个词语单独一行），并在词语添加完成后修改 \${ES_HOME}config/analysis-hanlp/hanlp.properties 配置文件，修改选项 CustomDictionaryPath，在后面添加上面新建的字典文件即可

## 同义词

在搜索场景中，同义词用来处理不同的查询词，有可能是表达相同搜索目标的场景。用户在使用这些与同义词相关的关键词进行搜索时，搜索引擎返回的搜索结果应该是一致的

用户可以通过 ES 中的分析器来使用同义词，使用方式分为两种

1.   建立索引时指定同义词并构建同义词的倒排索引：在 ES 内置的分词过滤器中，有一种分词过滤器叫作 synonyms，它是一种支持用户自定义同义词的分词过滤器

     ```json
     PUT /hotel 
     { 
         "settings": { 
             "analysis": { 
                 "filter": {                       //定义分词过滤器 
                     "ik_synonyms_filter": {  
                         "type": "synonym", 
                         "synonyms": [                //在分词过滤器中定义近义词 
                             "北京,首都", 
                             "天津,天津卫", 
                             "假日,度假" 
                         ] 
                     } 
                 }, 
                 "analyzer": {                      //自定义分析器 
                     "ik_analyzer_synonyms": { 
                         "tokenizer": "ik_max_word",   //指定分词器 
                         "filter": [                   //指定分词过滤器 
                             "lowercase", 
                             "ik_synonyms_filter" 
                         ]
                     } 
                 } 
             } 
         }, 
         "mappings": { 
             "properties": { 
                 "title": { 
                 "type": "text", 
                 "analyzer": "ik_analyzer_synonyms"  //指定索引时使用自定义的分析器 
                 }
         	} 
         } 
     }
     ```

2.   在搜索时指定字段的 search_analyzer 查询分析器使用同义词：在 ES 内置的分词过滤器中还有个分词过滤器叫作 synonym_graph，它是一种支持查询时用户自定义同义词的分词过滤器

     ```json
     PUT /hotel 
     { 
         "settings": { 
             "analysis": { 
                 "filter": {                          //定义分词过滤器 
                     "ik_synonyms_graph_filter": { 
                         "type": "synonym_graph", 
                         "synonyms": [                   //在分词过滤器中定义近义词 
                             "北京,首都", 
                             "天津,天津卫", 
                             "假日,度假" 
                         ] 
                     } 
                 }, 
                 "analyzer": {                       //自定义分析器 
                     "ik_analyzer_synonyms_graph": { 
                         "tokenizer": "ik_max_word",     //指定分词器 
                         "filter": [      //指定分词过滤器 
                             "lowercase", 
                             "ik_synonyms_graph_filter" 
                         ] 
                     } 
                 } 
             } 
         }, 
         "mappings": { 
             "properties": { 
                 "title": { 
                     "type": "text", 
                     "analyzer": "ik_max_word", 
                     //指定查询时使用自定义的分析器 
                     "search_analyzer": "ik_analyzer_synonyms_graph" 
                 } 
             } 
         } 
     }
     ```

两种方式命中的结果集和索引时使用的同义词一致，但是结果的**排序**却不同。这是因为在索引时使用同义词会计算全部的同义词的 TF/IDF 值，在搜索时进行的相关性计算，是将同义词和其他词同等对待，也就是将其 TF/IDF 值计算在内。而在搜索时使用同义词，需要 ES 将同义词转换后再进行相关性计算

注意：如果有更新同义词的需求，则只能使用查询时使用同义词的这种方式

如果同义词比较多，在 settings 中进行配置时将非常烦琐。ES 支持用户将同义词放在文件中，文件的位置必须是在 \${ES_HOME}/config 目录及其子目录下，注意该文件必须存在于 ES 集群中的每一个节点上。在 \${ES_HOME}/config 目录下建立一个子目录 mydict，然后在该目录下创建一个名称为 synonyms.dict 的文件，然后在创建索引时，在 settings 中指定同义词文件及其路径即可

## 停用词

停用词也叫停止词，是指文本在被分词之后的词语中包含的无搜索意义的词。在构建搜索引擎索引时，常常忽略这样的词，这样可以大大提升搜索效率

可以通过创建自定义分析器的方式使用停用词，方法是在分析器中指定停用词过滤器，在过滤器中可以指定若干个停用词

```json
PUT /hotel 
{ 
    "settings": { 
        "analysis": { 
            "filter": {                  //定义分词过滤器 
                "my_stop": {               //指定停用词过滤器 
                    "type": "stop", 
                    "stopwords": [           //指定停用词集合 
                        "我", 
                        "的", 
                        "这" 
                    ] 
                } 
            }, 
            "analyzer": {                //自定义分析器 
                "standard_stop": { 
                    "tokenizer": "standard", 
                    "filter":["my_stop"]     //指定分词过滤器 
                } 
            } 
        } 
    }, 
    "mappings": { 
        "properties": { 
            "title": { 
                "type": "text", 
                "analyzer": "standard_stop" //指定自定义分析器 
            } 
        } 
    } 
}
```

此外常用的内置分析器都自带有停用词过滤器，只需对其参数进行设置即可

如果用户想要使用 IK 分析器时添加自定义停用词文件，只需要在 \${ES_HOME}/plugins/ik-analysis/config 目录下创建 my_stopword.dict 文件，并在其中添加停用词，然后修改 \${ES_HOME}/plugins/ik-analysis/config/IKAnalyzer.cfg.xml 文件，设置配置项 ext_stopwords 的值为停用词词典的文件名称即可

同样在 HanLP 分析器中也可以自定义停用词文件，文件位置为 \${ES_HOME}/plugins/analysis-hanlp/data/dictionary/stopwords.txt，在该文件中已经有一些 HanLP 分析器内置的停用词，在该文件末尾追加停用词即可。但需要注意的是，HanLP 分析器默认不启动停用词，需要将 my_tokenizer分词器的enable_stop_dictionary 属性被设置为 true，表示当前分词器启用停用词

```json
PUT /hotel 
{ 
    "settings": { 
        "analysis": { 
            "tokenizer": {                             //自定义分词器 
                "my_tokenizer": { 
                    "type": "hanlp_standard",              //封装hanlp_standard分词过滤器 
                    "enable_stop_dictionary": true         //启用停用词词典 
                } 
            }, 
            "analyzer": {                               //自定义分析器 
                "my_hanlp": { 
                    "tokenizer": "my_tokenizer" 
                } 
            } 
        } 
    }, 
    "mappings": { 
        "properties": { 
            "title": { 
                "type": "text", 
                "analyzer": "my_hanlp"                   //指定自定义分析器 
            } 
        } 
    } 
}
```

## 拼音搜索

拼音搜索在中文搜索环境中是经常使用的一种功能，用户只需要输入关键词的拼音全拼或者拼音首字母，搜索引擎就可以搜索出相关结果

在 ES 中可以使用拼音分析器插件进行拼音搜索。拼音分析器提供的分析器为 pinyin，另外还提供了与其同名的分词器和分词过滤器

使用拼音分析器时，有很多的选项可以设置，例如，是否显示单字拼音的首字母、是否显示组合词的首字母、是否显示查询词的全部拼音等

## 拼写纠错

用户在使用搜索引擎的过程中，输入的关键词可能会出现拼写错误的情况。针对错误的关键词，绝大多数的搜索引擎都能自动识别并进行纠正，然后将纠正后的关键词放到索引中匹配数据。如果拼写错误特别多导致无法纠正，则会直接告知用户当前搜索没有匹配的结果

使用 ES 进行拼写纠错，首先需要搜集一段时间内用户搜索日志中有搜索结果的查询词，然后单独建立一个纠正词索引。当用户进行搜索时，如果在商品索引中没有匹配到结果，则在纠正词索引中进行匹配，如果有匹配结果则给出匹配词，并给出该匹配词对应的商品结果，如果没有匹配结果则告知用户没有搜索到商品

在 ES 中进行纠错匹配时使用 fuzzy-match 搜索，该搜索使用编辑距离和倒排索引相结合的形式完成纠错

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
                    // 原始分数 *（销量 + 浏览人数）
                    "source": "_score * (doc['sales'].value + doc['visitors'].value)"
                }
            }
        }
    }
}
```

```java
// Java function_score 示例
public void functionScoreSearch() { 
    //创建搜索请求 
    SearchRequest searchRequest = new SearchRequest("hotel"); 
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
    //构建term查询 
    TermQueryBuilder termQuery = QueryBuilders.termQuery("city", "北京");
    //构建随机函数
    ScoreFunctionBuilder scoreFunction = ScoreFunctionBuilders.randomFunction(); 
    //构建Function Score查询 
    FunctionScoreQueryBuilder funcQuery = QueryBuilders.functionScoreQuery(termQuery, scoreFunction).boostMode(CombineFunction.SUM);
    searchSourceBuilder.query(funcQuery); 
    searchRequest.source(searchSourceBuilder);  //设置查询请求 
   	printResult(searchRequest);                  //打印搜索结果 
}
```



### rescore_query

二次评分是指重新计算查询所返回的结果文档中指定文档的得分

ElasticSearch 会截取查询返回的前 N 条结果，并使用预定义的二次评分方法来重新计算其得分。但对全部有序的结果集进行重新排序的话，开销势必很大，使用 rescore_query 可以只对结果集的子集进行处理。该方式适用于对查询语句的结果不满意，需要重新打分的场景

```json
// 查询 content 字段中包含”实战“的文档，权重为0.7
// 并对文档中 title 为 MySQL 的文档增加评分，权重为1.2
// window_size 为 50，表示取分片结果的前 50 进行重新算
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

## 指标聚合 Metric Aggregation

### 单值分析

只输出一个分析结果，包含

-   min, max, avg, sum
-   cardinality

```json
// 统计最大、最小和平均值
POST /employees/_search
{
    "aggs": {
        "max_salary": {
            "max": {
                "field": "salary"
            }
        },
        "min_salary": {
            "min": {
                "field": "salary"
            }
        },
        "avg_salary": {
            "avg": {
                "field": "salary"
            }
        }
    }
}
```

```json
// 统计基数
POST /employees/_search
{
    "size": 0,
    "aggs": {
        "card_salary": {
            "cardinality": {
                "field": "salary"
            }
        }
    }
}
```

### 多值分析

输出多个分析结果，包含

-   stats, extended stats
-   percentile, percentile rank
-   top hits

```json
POST /employees/_search
{
    "size": 0,
    "aggs": {
        "stats_salary": {
            "stats": {
                "field": "salary"
            }
        }
    }
}
```

## 桶聚合 Bucket Aggregation

按照一定规则，将文档分配到不同的桶中，从而达到分类的目的

terms 类型，需要字段支持 filedata

-   ﻿﻿keyword，默认支持 fielddata
-   ﻿﻿text 需要在 Mapping 中开启 fielddata，会按照分词后的结果进行分桶（不建议对 text 进行分组）

数字类型

-   ﻿﻿Range / Data Range
-   ﻿﻿Histogram（直方图）/ Date Histogram

```json
GET /employees/_search
{
    "aggs": {
        "jobs": {
      		"terms": {
        		"field": "job.keyword",
          		"size": 10,
                "order": {
                    "_count": "desc"
                }
            }
        }
    }
}
```

```json
POST /employees/_search
{
    "size": 0,
    "aggs": {
        "salary_range": {
            "range": {
                "field": "salary",
                "ranges": [
                    {
                        "to": 10000
                    },
                    {
                        "from": 10000,
                        "to": 20000
                    },
                    {
                        "key": ">20000",
                        "from": 20000
                    }
                ]
            }
        }
    }
}
```

支持嵌套，即可以在桶里再做分桶

```json
POST /employees/_search
{
    "size": 0,
    "aggs": {
        "Job_salary_stats": {
            "terms": {
                "field": "job.keyword"
            },
            "aggs": {
                "salary": {
                    "stats": {
                        "field": "salary"
                    }
                }
            }
        }
    }
}
```

## 管道聚合 Pipeline Aggregation

支持对聚合分析的结果，再次进行聚合分析。Pipeline 的分析结果会输出到原结果中，根据位置的不同，分为两类：

-   ﻿﻿Sibling - 结果和现有分析结果同级
    -   ﻿﻿max, min, avg & sum bucket
    -   ﻿﻿stats, extended status bucket
    -   ﻿﻿percentiles bucket

-   ﻿﻿Parent - 结果内嵌到现有的聚合分析结果之中
    -   ﻿﻿derivative（求导）
    -   ﻿﻿cumultive sum（累计求和）
    -   moving function（移动平均值）

```json
// 平均工资最低的工种
POST /employees/_search
{
    "size": 0,
    "aggs": {
        "jobs": {
            "terms": {
                "field": "job.keyword",
                "size": 10
            },
            "aggs": {
                "avg_salary": {
                    "avg": {
                        "field": "salary"
                    }
                }
            }
        },
        "min_salary_by_job": {
            "min_bucket": {
                "buckets_path": "jobs>avg_salary"
            }
        }
    }
}
```

## 聚合问题

ElasticSearch 适用于海量数据搜索的场景。在对海量数据进行聚合分析时，会损失搜索的精准度来满足实时性需求

解决方案

1.   设置主分片为 1：ES7 以后默认设置为 1
2.   调大 shard_size 值：shard_size 值越大，结果越趋近于精准聚合结果值。此外，还可以通过 show_term_doc_count_error 参数显示最差情况下的错误值，用于辅助确定 shard_size 大小。适用于数据量大、分片数多的集群业务场景
3.   将 size 设置全量值，来解决精度问题：如果分片数据量极大，这样做会耗费巨大的 CPU 资源来排序，而且可能会阻塞网络。由于性能问题，不推荐使用，仅适用于对聚合精准度要求极高的业务场景
4.   使用 Clickhouse/ Spark 进行精准聚合：适用于数据量非常大、聚合精度要求高、响应速度快的业务场景

## 性能优化

优化方案

1.   插入数据时对索引进行预排序
     -   ﻿﻿Index sorting（索引排序）可用于在插入时对索引进行预排序，而不是在查询时再对索引进行排序，这将提高范围查询（range query）和排序操作的性能
     -   ﻿在 ElasticSearch 中创建新索引时，可以配置如何对每个分片内的段进行排序
     -   适用于读多写少的场景

2.   使用节点查询缓存
     -   可用于有效缓存过滤器（flter）操作的结果。如果多次执行同一 filter 操作，这将很有效，但是即便更改过滤器中的某一个值，也将意味着需要计算新的过滤器结果
     -   可以执行一个带有过滤查询的搜索请求，Elasticsearch 将自动尝试使用节点查询缓存来优化性能
3.   使用分片请求缓存：聚合语句中设置 size 为 0，使用分片请求缓存结果，即只返回聚合结果，不返回查询结果
4.   拆分聚合，使聚合并行化：ES 默认聚合为非并行运行。但为每个聚合提供自己的查询并执行 msearch 时，性能会有显著提升
