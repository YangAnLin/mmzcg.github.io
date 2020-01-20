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

# 5.API使用

查询`lib`索引的两种用法

```shell
GET lib
GET /lib/_settings
```

查询所有索引

```shell
GET /_all/_settings
```

添加索引`lib2`

```shell
PUT /lib2
```

添加索引lib3,请别指定字段类型

```json
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

添加数据

```json
# 指定ID用PUT
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

查询文档

```shell
GET /lib/user/1
```

查询索引下的所有文档

```json
GET /tt/ttt/_search
```

查看文档中的指定字段

```shell
GET /lib/user/1?_source=age,about
```

覆盖方式更新文档

```json
PUT /lib/user/1
{
    "first_name":"Jame",
    "last_name":"Smith",
    "age":"36",
    "about":"I like to collect rock albums",
    "interests":["music"]
}
```

只是更新文档

```json
POST /lib/user/1/_update
{
  "doc":{
     "age":"37"
  }
}
```

删除

```json
# 删除文档
DELETE /lib/user/1 

# 删除索引
DELETE /lib
```

MuliGet批量查询

```json
GET /_mget
{
  "docs":[{
      "_index":"lib",
      "_type":"user",
      "_id":1,
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
```

MuliGet批量查询,筛选字段

```json
GET /_mget
{
  "docs":[{
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

```

MuliGet批量查询的是同一个文档时候,可以jianxie

```json
GET /lib/user/_mget
{
  "docs":[
    {"_id":1},
    {"_type":"user","_id":2}
    ]
}
```

Bulk批量插入

```json
POST /lib2/books/_bulk
{"index":{"_id":1}}
{"title":"java","price":55}
{"index":{"_id":2}}
{"title":"html5","price":55}
{"index":{"_id":3}}
{"title":"php","price":35}
{"index":{"_id":4}}
{"title":"python","price":50}
```

* index 创建新文档或者替换已有文档
* create 文档不存在的时候创建

term和terms查询

term 查询会去倒排索引中寻找确切的tem,它并不知道分词器的存在,这种查询使用keyword,numeric,date

```json
GET /lib3/user/_search/
{
  "query":{
    "terms": {
      "name": [
        "zhou"
      ]
    }
  }
}

GET /lib3/user/_search/
{
  "query":{
    "term": {
      "name": "zhaoming"
    }
  }
}
```

form和size分页

form是从0开始的,size是取多少个

```json
GET /lib3/user/_search/
{
  "version": true,  # 查询的时候如果需要返回version的话
  "from": 0, 
  "size": 1, 
  "query":{
    "terms": {
      "name": [
        "zhou",
        "lisi"
      ]
    }
  }
}
```

查询数量

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

match是知道分词器的存在的

```json
GET /lib3/user/_search
{
  "query":{
    "match": {
      "name": "lisi zhaoming"
    }
  }
}
```

短语匹配(要完全匹配)

```json
GET /lib3/user/_search
{
  "query":{
    "match_phrase": {
      "interests": "lvyou, duanlian "
    }
  }
}
```

前缀匹配

```json
GET /lib3/user/_search
{
  "query":{
    "match_phrase_prefix": {
      "name": "zhao"
    }
  }
}
```

排序

```json
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
```

范围,这里应该是相等的

```json
GET /lib3/user/_search
{
  "query": {
    "range": {
      "birthday": {
        "gte": "1999-10-12",
        "lte": "2290-01-01"
      }
    }
  }
}
```

```json
GET /lib3/user/_search
{
  "query":{
    "range": {
      "age": {
        "gte": 20,
        "lte": 38,
        "include_lower":false,   # true包含,false不包含,下限
        "include_upper":true     # 上限
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

通配符查询

```json
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

模糊查询(zhaoming写反了,也可以查出来)

```json
GET /lib3/user/_search
{
  "query": {
    "fuzzy": {
      "name": {
        "value": "zhaoimng"
      }
    }
  }
}
```

高亮搜索

```json
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

### Filter查询,是不计算相关性的,同事可以缓存,因此,filter速度要快于query

### bool查询,是可以嵌套的

```json
GET /lib4/items/_search
{
  "query": {
    "bool": {
      "filter": {
        "term":{"price": "40"} # 过滤价格是40的
      }
    }
  }
}

GET /lib4/items/_search
{
  "query": {
    "bool": {
      "filter": {
        "terms":{"price": [250,40]}
      }
    }
  }
}

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

```json
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

```json
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

### 聚合查询,比方求最大值,最小值

```json
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

# 求基数
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





### 复合查询







# 6.先记着

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

