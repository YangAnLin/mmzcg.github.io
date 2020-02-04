[toc]

# 1.手动安装单机ElasticSearch

1. 下载tar包,(6.6.2)
```shell
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.6.2.tar.gz
```
2. 解压缩之后修改配置文件
```properties
vi config/Elasticsearch.yml
network.host: 0.0.0.0 #设置成外网也可以访问
```

```properties
vi config/sysctl.conf
vm.max_map_count=262144 # 修改系统环境该变量,修改完需要重启才能生效
```

```properties
vim /etc/security/limits.conf   # 允许打开的最大文件描述

# 加入
* soft nofile 65536
* hard nofile 65536
* soft nproc 4096 或者(65536)
* hard nproc 4096 或者(65536)
```

```properties
vi config/jvm.options  # 修改JVM分配大小 这两个值要一致
-Xms1g
-Xmx1g
```
3. 启动
```shell
bin/elasticsearch
bin/elasticsearch -d  #后台启动
```
4. 安装分词器
下载解压缩的哦elasticsearch的plugin目录下
IK分词器
https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v6.6.2
```shell
POST /_analyze { "analyzer": "ik_smart", "text": " 程序员 }
```
拼音分词器
https://github.com/medcl/elasticsearch-analysis-pinyin/releases/tag/v6.6.2
```json
GET /medcl/_analyze
    {
    "text": ["程序员"],
    "analyzer": "pinyin_analyzer"
    }
```

# 2.手动安装集群ElasticSearch

1.修改一台elasticsearch的elasticsearch.yml

```shell
cluster.name: kkb-es
node.name: node0
node.master: true
network.host: 0.0.0.0
http.port: 9200
transport.tcp.port: 9300
discovery.zen.ping.unicast.hosts: ["127.0.0.1:9300","127.0.0.1:9301","127.0.0
.1:9302"]
discovery.zen.minimum_master_nodes: 2
http.cors.enabled: true
http.cors.allow-origin: "*"
```

2.拷贝到其他的服务器

修改上面呢的配置文件中的3个配置

```
node.name: node0
http.port: 9200
transport.tcp.port: 9300
```

3.检查健康状态

```shell
http://localhost:9200/_cat/health?v
```



# 3.手动单机Kibana安装

1. 下载解压
```shell
https://artifacts.elastic.co/downloads/kibana/kibana-6.6.2-linux-x86_64.tar.gz
```
2. 修改配置文件
```shell
vi kibana-6.4.2-linux-x86_64/config/kibana.yml
server.port: 5600
server.host: "0.0.0.0"
elasticsearch.url: "http://localhost:9200"
```
3. 启动
```shell
./bin/kibana
```


# 4.手动单机Logstash安装

`mysql-connector-java-5.1.48.jar`是放在`logstash-6.6.2/logstash-core/lib/jars`,这样就不需要写相对路径了

```json
input {
    jdbc {
        jdbc_driver_library => "mysql-connector-java-5.1.48.jar"
        jdbc_driver_class => "com.mysql.jdbc.Driver"
        jdbc_connection_string => "jdbc:mysql://192.168.111.128:3306/ssm"
        jdbc_user => "root"
        jdbc_password => "123456"
        jdbc_paging_enabled => "true"
        jdbc_page_size => "2"
        statement => "SELECT id,'name',phone FROM phone where update_data >= :sql_last_value"
        tracking_column_type => "timestamp"
        tracking_column => "update_date"
        schedule => "* * * * *"
    }
}
output {
    elasticsearch {
        index => "phone"
        document_id=> "%{id}"
        hosts => ["http://192.168.111.128:9200"]
    }
}
```

启动命令

```shell
./logstash -f ../config/logstash-sample.conf
```

# 5.API简单使用

## 1.查询索引和映射

```shell
# 这两个都是查看索引,只是第三行比第三行多几个字段
GET lib
GET /lib/_settings

# 查询所有索引
GET /_all/_settings

# 查询映射
GET /lib2/_mapping
```

## 2.添加索引

```shell
# 方式一,使用的是默认的配置
PUT /lib

# 方式二
PUT /lib/
{
  "settings": {
    "index": {
      "number_of_shards": 5,
      "number_of_replicas": 1
    }
  }
}

# 方式三,指定字段类型
PUT /lib3
{
  "mappings": {
    "user":{
      "properties":{
        "name":{"type":"text"},
        "address":{"type":"text"},
        "age":{"type":"text"},
        "interests":{"type":"text"},
        "birthday":{"type":"text"}
      }
    }
  }
}
```

## 3.添加文档

```shell
# 指定id的时候用PUT
PUT /lib/user/1
{
    "first_name":"Jame",
    "last_name":"Smith",
    "age":"32",
    "about":"I like to collect rock albums",
    "interests":["music"]
}

# 没有指定ID用POST,创建的时候会自动生成ID
POST /lib/user/
{
    "first_name":"anthony",
    "last_name":"Smith",
    "age":"32",
    "about":"I like to collect rock albums",
    "interests":["music"]
}
```

## 4.查询文档

```shell
# 指定ID
GET /lib/user/1

# 查看文档中的指定字段
GET /lib/user/1?_source=age,about

# 查询索引下的所有文档
GET /tt/ttt/_search
```

## 5.更新文档

```shell
# 以覆盖的方式,如果有这个id,就覆盖更新,如果没有id,就创建
PUT /lib/user/1
{
    "first_name":"Jame",
    "last_name":"Smith",
    "age":"36",
    "about":"I like to collect rock albums",
    "interests":["music"]
}

# 只是更新文档
POST /lib/user/1/_update
{
  "doc":{
     "age":"37"
  }
}
```

## 6.删除文档和索引

```shell
# 删除文档
DELETE /lib/user/1 

# 删除索引
DELETE /lib
```

## 7.MuliGet
MuliGet 需要ID,才能实现
```shell
# 批量查询,这是查出全部数据
GET /_mget
{
  "docs":[
    {
      "_index":"lib",
      "_type":"user",
      "_id":1
    },
    {
      "_index":"lib",
      "_type":"user",
      "_id":2
    },
    {
      "_index":"lib",
      "_type":"user",
      "_id":3
    }
    ]
}

# 批量查询,筛选字段
GET /_mget
{
  "docs":[
    {
      "_index":"lib",
      "_type":"user",
      "_id":1,
      "_source":"interests"
    },
    {
      "_index":"lib",
      "_type":"user",
      "_id":2,
      "_source":["interests","age"]
    }]
}

# MuliGet批量查询的是同一个文档时候,可以简写方式
GET /lib/user/_mget
{
  "docs": [
    {
      "_id": 1
    },
    {
      "_type": "user",
      "_id": 2
    }
  ]
}

# 更简单的方式
GET /lib/user/_mget
{
   "ids": ["1","2"]
}
```

## 8.Bulk批量插入
bulk的格式：

```shell
{action:{metadata}}
{requstbody}
```

* action:
	* create：文档不存在时创建
	* update:更新文档
	* index:创建新文档或替换已有文档
	* delete:删除一个文档
* metadata:
	* index
	* _type
	* _id

create 和index的区别:如果数据存在，使用create操作失败，会提示文档已经存在，使用index则可以成功执行。

bulk一次最大处理多少数据量:

　　bulk会把将要处理的数据载入内存中，所以数据量是有限制的，最佳的数据量不是一个确定的数值，它取决于你的硬件，你的文档大小以及复杂性，你的索引以及搜索的负载。一般建议是1000-5000个文档，大小建议是5-15MB，默认不能超过100M，可以在es的配置文件（即$ES_HOME下的config下的elasticsearch.yml）中。


```shell
# 批量删除,delete不需要请求体
{"delete":{"_index":"lib","_type":"user","_id":"1"}}

# 批量添加
POST /lib2/books/_bulk
{"index":{"_id":1}}
{"title":"java","price":55}
{"index":{"_id":2}}
{"title":"html5","price":55}
{"index":{"_id":3}}
{"title":"php","price":35}
{"index":{"_id":4}}
{"title":"python","price":50}

# 批量获取
GET /lib2/books/_mget
{
	"ids": ["1","2","3","4"]
}

# 批量操作
POST /lib2/books/_bulk
{"delete":{"_index":"lib2","_type":"books","_id":4}}
{"create":{"_index":"tt","_type":"ttt","_id":"100"}}
{"name":"lisi"}
{"index":{"_index":"tt","_type":"ttt"}}
{"name":"zhaosi"}
{"update":{"_index":"lib2","_type":"books","_id":"4"}}
{"doc":{"price":58}}
```



## 9.基本查询(Query查询)

### 1.term和terms查询

* term 查询会去倒排索引中寻找确切的tem,它并不知道分词器的存在,这种查询使用keyword,numeric,date,在没有使用分词器的时候使用

* term 查询某个字段里含有某个关键词的文档

* terms:查询某个字段里含有多个关键词的文档

```shell
# term，name含有“zhaoming”
GET /lib3/user/_search/
{
  "query":{
    "term": {"name": "zhaoming"}
  }
}

# terms，name含有“zhou”或者还有"yang"的查出来
GET /lib3/user/_search/
{
  "query":{
    "terms": {
      "name": ["zhou","yang"]
    }
  }
}
```

### 2.form和size分页

form是从0开始的,size是取多少个

```shell
GET /lib3/user/_search/
{
  "version": true,  # 查询的时候如果需要返回version的话
  "from": 0, 
  "size": 1, 
  "query":{
    "terms": {
      "name": ["zhou","lisi"]
    }
  }
}
```



```json
GET article/_count
{
  "query": {
    "match": {
      "title": "金融"
    }
  }
}
```

### 3.match查询

match query知道分词器的存在，会对filed进行分词操作，然后再查询

* match_all:查询所有文档

* match:查询一个字段

* multi_match:查询多个字段
* match_phrase:包含得含有一样的,而且顺序还一样的,可以不用完整
* match_phrase_prefix: 前缀匹配

```shell
# match是知道分词器的存在的,所以会去查询name是lisi的,或者name是zhaoming的(or的关系)
GET /lib3/user/_search
{
  "query":{
    "match": {
      "name": "lisi zhaoming"
    }
  }
}

# 可以指定多个字段
GET /lib3/user/_search
{
    "query":{
        "multi_match": {
            "query": "lvyou",
            "fields": ["interests","name"]
         }
    }
}

# 短语匹配(要完全匹配),包含的关系
GET /lib3/user/_search
{
  "query":{
    "match_phrase": {
      "interests": "lvyou, duanlian "
    }
  }
}

# 前缀匹配
GET /lib3/user/_search
{
  "query":{
    "match_phrase_prefix": {
      "name": "zhao"
    }
  }
}
```

### 4._source控制返回字段

```shell
# 指定返回的字段
GET /lib3/user/_search
{
    "_source": ["address","name"],
    "query": {
        "match": {
            "interests": "changge"
        }
    }
}

# 包含和排除返回字段
GET /lib3/user/_search
{
    "query": {
        "match_all": {}
    },
    "_source": {
          "includes": ["name","address"], # 包含这些字段
          "excludes": ["age","birthday"]  # 排除这些字段
      }
}

# 使用通配符*
GET /lib3/user/_search
{
    "_source": {
          "includes": "addr*", 
          "excludes": ["name","bir*"]        
    },
    "query": {
        "match_all": {}
    }
}
```

### 5.sort排序

```shell
# 指定排序字段
GET /lib3/user/_search
{
  "query":{
    "match_all": {}
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ]
}

# 控制范围
GET /lib3/user/_search
{
  "query":{
    "range": {
      "age": {
        "gte": 20,
        "lte": 38,
        "include_lower":true,   # true包含,false不包含,下限
        "include_upper":true    # true包含,false不包含,上限
      }
    }
  },
  "sort": [
    {
      "age": {
        "order": "asc"
      }
    }
  ]
}
```

### 6.wildcard通配符查询

允许使用通配符* 和 ?来进行查询

*代表0个或多个字符

？代表任意一个字符

```shell
GET /lib3/user/_search
{
    "query": {
        "wildcard": {
             "name": "zhao*"
        }
    }
}

GET /lib3/user/_search
{
  "query": {
    "wildcard": {
      "name": {
        "value": "li?i"
      }
    }
  }
}
```

### 7.fuzzy模糊查询

* value：查询的关键字

* boost：查询的权值，默认值是1.0

* min_similarity:设置匹配的最小相似度，默认值为0.5，对于字符串，取值为0-1(包括0和1);对于数值，取值可能大于1;对于日期型取值为1d,1m等，1d就代表1天

* prefix_length:指明区分词项的共同前缀长度，默认是0

* max_expansions:查询中的词项可以扩展的数目，默认可以无限大

```shell
GET /lib3/user/_search
{
  "query": {
    "fuzzy": {
      "name": {
        "value": "zhaoimng" # zhaoming写反了,也可以查出来
      }
    }
  }
}
```

### 8.高亮搜索

```shell
GET /lib3/user/_search
{
  "query": {
    "fuzzy": {
      "name": {
        "value": "zhaoimng"
      }
    }
  },
  "highlight": {
    "fields": {
      "name":{}
    }
  }
}
```

### 9.Filter查询

是不计算相关性的,同时可以缓存,因此,filter速度要快于query

```shell
# 过滤价格是40的
GET /lib4/items/_search
{
  "query": {
    "bool": {
      "filter": {
        "term":{"price": "40"} 
      }
    }
  }
}

# 过滤价格是25的和40的
GET /lib4/items/_search
{
  "query": {
    "bool": {
      "filter": {
        "terms":{"price": [25,40]} 
      }
    }
  }
}
```

### 10.bool过滤查询
可以实现组合过滤查询

格式：

```shell 
{
    "bool": {
        "must": [],
        "should": [],
        "must_not": []
    }
}
```

must:必须满足的条件---and

should：可以满足也可以不满足的条件--or

must_not:不需要满足的条件--not

```shell
# 翻译过来:要是是价格是250,或者是itemID是:id100123,并且price不能是30
GET /lib4/items/_search
{
  "query": {
    "bool": {
      "should": [
        {"term":{"price":250}},
        {"term":{"itemID":"id100123"}}
      ],
      "must_not": [
        {"term":{"price":30}}
      ]
    }
  }
}
```

过滤,要想明白跟普通查询的过滤有什么不一样

```shell
GET /lib4/items/_search
{
  "query": {
    "bool": {
      "filter": {
        "range": {
          "price": {
            "gte": 40,
            "lte": 250
          }
        }
      }
    }
  }
}
```

获取非空的文档

```shell
GET /lib4/items/_search
{
  "query": {
   "bool": {
     "filter": {
       "exists": {
         "field": "price"
       }
     }
   }
  }
}
```

### 11.聚合查询

```shell
GET /lib4/items/_search
{
  "size":0, # 只显示总和
  "aggs":{
    "price_of_sum":{
      "sum":{
        "field":"price"
      }
    }
  }
}

# 最大值
GET /lib4/items/_search
{
  "aggs":{
    "price_of_sum":{
      "max":{
        "field":"price"
      }
    }
  }
}

# 最小值
GET /lib4/items/_search
{
  "aggs":{
    "price_of_sum":{  # price_of_sum这个名字随便起的
      "min":{
        "field":"price"
      }
    }
  }
}

# 求平均数
GET /lib4/items/_search
{
  "aggs":{
    "price_of_sum":{
      "avg":{
        "field":"price"
      }
    }
  }
}

# 价格有不相同的数
GET /lib4/items/_search
{
  "aggs":{
    "price_of_sum":{
      "cardinality":{
        "field":"price"
      }
    }
  }
}

# 分组
GET /lib4/items/_search
{
  "size": 0, 
  "aggs":{
    "price_of_sum":{
      "terms":{
        "field":"price"
      }
    }
  }
}
```

# 6.API高级

### 1.版本控制

ElasticSearch采用了乐观锁来保证数据的一致性，也就是说，当用户对document进行操作时，并不需要对该document作加锁和解锁的操作，只需要指定要操作的版本即可。当版本号一致时，ElasticSearch会允许该操作顺利执行，而当版本号存在冲突时，ElasticSearch会提示冲突并抛出异常（VersionConflictEngineException异常）。

ElasticSearch的版本号的取值范围为1到2^63-1。

内部版本控制：使用的是_version


外部版本控制：elasticsearch在处理外部版本号时会与对内部版本号的处理有些不同。它不再是检查_version是否与请求中指定的数值_相同_,而是检查当前的_version是否比指定的数值小。如果请求成功，那么外部的版本号就会被存储到文档中的_version中。

为了保持_version与外部版本控制的数据一致
使用version_type=external

### 2.数据类型

核心数据类型（Core datatypes）

```shell
字符型：string，string类型包括
text 和 keyword

text类型被用来索引长文本，在建立索引前会将这些文本进行分词，转化为词的组合，建立索引。允许es来检索这些词语。text类型不能用来排序和聚合。

Keyword类型不需要进行分词，可以被用来检索过滤、排序和聚合。keyword 类型字段只能用本身来进行检索

数字型：long, integer, short, byte, double, float
日期型：date
布尔型：boolean
二进制型：binary
```

复杂数据类型（Complex datatypes）

```shell
数组类型（Array datatype）：数组类型不需要专门指定数组元素的type，例如：
    字符型数组: [ "one", "two" ]
    整型数组：[ 1, 2 ]
    数组型数组：[ 1, [ 2, 3 ]] 等价于[ 1, 2, 3 ]
    对象数组：[ { "name": "Mary", "age": 12 }, { "name": "John", "age": 10 }]
对象类型（Object datatype）：_ object _ 用于单个JSON对象；
嵌套类型（Nested datatype）：_ nested _ 用于JSON数组；
```

地理位置类型（Geo datatypes）

```shell
地理坐标类型（Geo-point datatype）：_ geo_point _ 用于经纬度坐标；
地理形状类型（Geo-Shape datatype）：_ geo_shape _ 用于类似于多边形的复杂形状；
```

特定类型（Specialised datatypes）

```shell
IPv4 类型（IPv4 datatype）：_ ip _ 用于IPv4 地址；
Completion 类型（Completion datatype）：_ completion _提供自动补全建议；
Token count 类型（Token count datatype）：_ token_count _ 用于统计做了标记的字段的index数目，该值会一直增加，不会因为过滤条件而减少。
mapper-murmur3
类型：通过插件，可以通过 _ murmur3 _ 来计算 index 的 hash 值；
附加类型（Attachment datatype）：采用 mapper-attachments
插件，可支持_ attachments _ 索引，例如 Microsoft Office 格式，Open Document 格式，ePub, HTML 等。
```

支持的属性：

```shell
"store":false//是否单独设置此字段的是否存储而从_source字段中分离，默认是false，只能搜索，不能获取值

"index": true//分词，不分词是：false，设置成false，字段将不会被索引

"analyzer":"ik"//指定分词器,默认分词器为standard analyzer

"boost":1.23//字段级别的分数加权，默认值是1.0

"doc_values":false//对not_analyzed字段，默认都是开启，分词字段不能使用，对排序和聚合能提升较大性能，节约内存

"fielddata":{"format":"disabled"}//针对分词字段，参与排序或聚合时能提高性能，不分词字段统一建议使用doc_value

"fields":{"raw":{"type":"string","index":"not_analyzed"}} //可以对一个字段提供多种索引模式，同一个字段的值，一个分词，一个不分词
            
"ignore_above":100 //超过100个字符的文本，将会被忽略，不被索引

"include_in_all":ture//设置是否此字段包含在_all字段中，默认是true，除非index设置成no选项

"index_options":"docs"//4个可选参数docs（索引文档号） ,freqs（文档号+词频），positions（文档号+词频+位置，通常用来距离查询），offsets（文档号+词频+位置+偏移量，通常被使用在高亮字段）分词字段默认是position，其他的默认是docs

"norms":{"enable":true,"loading":"lazy"}//分词字段默认配置，不分词字段：默认{"enable":false}，存储长度因子和索引时boost，建议对需要参与评分字段使用 ，会额外增加内存消耗量

"null_value":"NULL"//设置一些缺失字段的初始化值，只有string可以使用，分词字段的null值也会被分词

"position_increament_gap":0//影响距离查询或近似查询，可以设置在多值字段的数据上火分词字段上，查询时可指定slop间隔，默认值是100

"search_analyzer":"ik"//设置搜索时的分词器，默认跟ananlyzer是一致的，比如index时用standard+ngram，搜索时用standard用来完成自动提示功能

"similarity":"BM25"//默认是TF/IDF算法，指定一个字段评分策略，仅仅对字符串型和分词类型有效

"term_vector":"no"//默认不存储向量信息，支持参数yes（term存储），with_positions（term+位置）,with_offsets（term+偏移量），with_positions_offsets(term+位置+偏移量) 对快速高亮fast vector highlighter能提升性能，但开启又会加大索引体积，不适合大数据量用
```



# 7.先记着

用logstash去同步mysql的的增量,,需要两个字段,第一个是 id,主键,第二个是update_date,  用第二个字段去查询,默认用id去去重



group by 的时候报错了

es进行聚合操作时提示Fielddata is disabled on text fields by default

https://blog.csdn.net/u011403655/article/details/71107415/

```json
# 充值笔数
GET /shouye/rukuan/_search
{
  "aggs": {
    "my_cre": {
      "terms": {
        "field": "orderId"
      }
    }
  }
}

GET /shouye/rukuan/_count

GET /shouye/_mapping

PUT shouye/_mapping/rukuan/
{
  "properties": {
    "orderId": { 
      "type":     "text",
      "fielddata": true
    }
  }
}
```





# 7.springboot整合Elasticsearch

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.lzkj</groupId>
        <artifactId>item2</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>
    <groupId>com.es</groupId>
    <artifactId>es-server</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>es-server</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

```shell
spring.elasticsearch.rest.uris=http://192.168.111.132:9200
```

