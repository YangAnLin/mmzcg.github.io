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
vm.max_map_count=262144 # 修改完需要重启才能生效
```

```properties
-Xms1g
-Xmx1g
这两个值要一致
```

3. 
4. 




