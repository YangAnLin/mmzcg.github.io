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

# 6.先记着

用logstash去同步mysql的的增量,,需要两个字段,第一个是 id,主键,第二个是update_date,  用第二个字段去查询,默认用id去去重