---
title: ElasticaSeach 入门
create: 2021-09-11
data: 2021-09-11
update: 2021-09-13
---

## 初识 ElasticSearch

### 了解 ElasticSearch

#### ElasticSearch 的作用

ElasticSearch 是一款非常强大的开源搜索引擎，具备非常多强大功能，可以帮助我们从海量数据中快速找到需要的内容

#### ELK 技术栈

ElasticSearch 结合 Kibana、Logstash、Beats，也就是 Elastic stack（ELK）。被广泛应用在日志数据分析、实时监控等领域。

#### ElasticSearch 和 Lucene

ElasticSearch 底层是基于 Lucene 来实现的。

Lucene 是一个 Java 语言的搜索引擎类库，是 Apache 公司的顶级项目，由 DougCutting 于 1999 年研发。

Lucene 官网地址：https://lucene.apache.org/ 。

Lucene 的优势：易扩展、高性能（基于倒排索引）

Lucene 的缺点：只限于 Java 语言开发、学习曲线陡峭、不支持水平扩展

ElasticSearch 的发展历史：

- 2004 年 Shay Banon 基于 Lucene 开发了 Compass
- 2010 年 Shay Banon 重写了 Compass，取名为 ElasticSearch。

ElasticSearch 官网地址: https://www.elastic.co/cn/

相比与 Lucene ，ElasticSearch 具备下列优势：

+ 支持分布式，可水平扩展
+ 提供 Restful 接口，可被任何语言调用

### 倒排索引

倒排索引的概念是基于 MySQL 这样的正向索引而言的。

#### 正向索引

如果是根据 id 查询，那么直接走索引，查询速度非常快。

但如果是基于 title 做模糊查询，只能是逐行扫描数据，流程如下：

1. 用户搜索数据，条件是 title 符合 `"%手机%"`
2. 逐行获取数据，比如 id 为 1 的数据
3. 判断数据中的 title 是否符合用户搜索条件
4. 如果符合则放入结果集，不符合则丢弃。回到步骤 1

逐行扫描，也就是全表扫描，随着数据量增加，其查询效率也会越来越低。当数据量达到数百万时，就是一场灾难。

#### 倒排索引

倒排索引中有两个非常重要的概念：

- 文档（Document）：用来搜索的数据，其中的每一条数据就是一个文档。例如一个网页、一个商品信息
- 词条（Term）：对文档数据或用户搜索数据，利用某种算法分词，得到的具备含义的词语就是词条。例如：我是中国人，就可以分为：我、是、中国人、中国、国人这样的几个词条

创建倒排索引是对正向索引的一种特殊处理，流程如下：

- 将每一个文档的数据利用算法分词，得到一个个词条
- 创建表，每行数据包括词条、词条所在文档 id、位置等信息
- 因为词条唯一性，可以给词条创建索引，例如 hash 表结构索引

如图：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.5jp2imzxno00.png)

倒排索引的搜索流程如下（以搜索“华为手机”为例）：

1. 用户输入条件 `"华为手机"` 进行搜索。
2. 对用户输入内容分词，得到词条：`华为`、`手机`。
3. 拿着词条在倒排索引中查找，可以得到包含词条的文档 id：1、2、3。
4. 拿着文档 id 到正向索引中查找具体文档。

虽然要先查询倒排索引，再查询倒排索引，但是无论是词条、还是文档 id 都建立了索引，查询速度非常快！无需全表扫描。

#### 正向和倒排

那么为什么一个叫做正向索引，一个叫做倒排索引呢？

- 正向索引是最传统的，根据id索引的方式。但根据词条查询时，必须先逐条获取每个文档，然后判断文档中是否包含所需要的词条，是根据文档找词条的过程。

- 倒排索引则相反，是先找到用户要搜索的词条，根据词条得到保护词条的文档的 id，然后根据 id 获取文档。是根据词条找文档的过程。

正向索引

+ 优点：可以给多个字段创建索引、根据索引字段搜索、排序速度非常快

- 缺点：根据非索引字段，或者索引字段中的部分词条查找时，只能全表扫描。

倒排索引：

- 优点：根据词条搜索、模糊搜索时，速度非常快
- 缺点：只能给词条创建索引，而不是字段、无法根据字段做排序

### ElasticSearch 中的一些概念

ElasticSearch 中有很多独有的概念，与 MySQL 中略有差别，但也有相似之处。

#### 文档和字段

ElasticSearch 是面向文档（Document）存储的，可以是数据库中的一条商品数据，一个订单信息。文档数据会被序列化为 JSON 格式后存储在 ElasticSearch 中：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.lwpnpfcxvds.png)

而 JSON 文档中往往包含很多的字段（Field），类似于数据库中的列。

#### 索引和映射

索引（Index），就是相同类型的文档的集合。例如：

- 所有用户文档，就可以组织在一起，称为用户的索引；
- 所有商品的文档，可以组织在一起，称为商品的索引；
- 所有订单的文档，可以组织在一起，称为订单的索引；

![索引](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/索引.67l5ib0vvgo0.svg)

因此，我们可以把索引当做是数据库中的表。

数据库的表会有约束信息，用来定义表的结构、字段的名称、类型等信息。因此，索引库中就有映射（mapping），是索引中文档的字段约束信息，类似表的结构约束。

#### MySQL 与 ElasticSearch

我们统一的把 MySQL 与 ElasticSearch 的概念做一下对比：

| MySQL  | Elasticsearch | **说明**                                                     |
| ------ | ------------- | ------------------------------------------------------------ |
| Table  | Index         | 索引（index），就是文档的集合，类似数据库的表（table）       |
| Row    | Document      | 文档（Document），就是一条条的数据，类似数据库中的行（Row），文档都是JSON格式 |
| Column | Field         | 字段（Field），就是 JSON 文档中的字段，类似数据库中的列（Column） |
| Schema | Mapping       | Mapping（映射）是索引中文档的约束，例如字段类型约束。类似数据库的表结构（Schema） |
| SQL    | DSL           | DSL是 ElasticSearch 提供的 JSON 风格的请求语句，用来操作 ElasticSearch，实现 CRUD |

- MySQL ：擅长事务类型操作，可以确保数据的安全和一致性

- ElasticSearch：擅长海量数据的搜索、分析、计算

因此在企业中，往往是两者结合使用：

- 对安全性要求较高的写操作，使用 MySQL 实现
- 对查询性能要求较高的搜索需求，使用 ElasticSearch 实现
- 两者再基于某种方式，实现数据的同步，保证一致性

![ElasticSearch](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/ElasticSearch.4koj7qwgqge0.svg)

### 安装 ElasticSearch 、Kibana

#### 创建网络

因为我们还需要部署 Kibana 容器，因此需要让 ElasticSearch 和 Kibana 容器互联。这里先创建一个网络：

```sh
docker network create halo-es-net
```

#### 拉取或加载镜像

```sh
docker pull elasticsearch:7.14.1
```

```sh
docker pull kibana:7.14.1
```

#### 运行（单点）

运行 docker 命令，部署单点 ElasticSearch ：

```sh
docker run -d \
	--name halo-es \
    -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
    -e "discovery.type=single-node" \
    -v es-data:/usr/share/elasticsearch/data \
    -v es-plugins:/usr/share/elasticsearch/plugins \
    --privileged \
    --network halo-es-net \
    -p 9200:9200 \
    -p 9300:9300 \
elasticsearch:7.14.1
```

命令解释：

- `-e "cluster.name=es-docker-cluster"`：设置集群名称
- `-e "http.host=0.0.0.0"`：监听的地址，可以外网访问
- `-e "ES_JAVA_OPTS=-Xms512m -Xmx512m"`：内存大小
- `-e "discovery.type=single-node"`：非集群模式
- `-v es-data:/usr/share/elasticsearch/data`：挂载逻辑卷，绑定 ElasticSearch 的数据目录
- `-v es-logs:/usr/share/elasticsearch/logs`：挂载逻辑卷，绑定 ElasticSearch 的日志目录
- `-v es-plugins:/usr/share/elasticsearch/plugins`：挂载逻辑卷，绑定 ElasticSearch 的插件目录
- `--privileged`：授予逻辑卷访问权
- `--network halo-es-net` ：加入一个名为 halo-es-net 的网络中
- `-p 9200:9200`：端口映射配置

在浏览器中输入：http://halo:9200 即可看到 ElasticSearch 的响应结果：

运行 docker 命令，部署 Kibana

```sh
docker run -d \
--name halo-kibana \
-e ELASTICSEARCH_HOSTS=http://halo-es:9200 \
--network halo-es-net \
-p 5601:5601  \
kibana:7.14.1
```

- `--network es-net` ：加入一个名为es-net的网络中，与 elasticsearch 在同一个网络中
- `-e ELASTICSEARCH_HOSTS=http://halo-es:9200"`：设置 elasticsearch 的地址，因为 kibana 已经与elasticsearch 在一个网络，因此可以用容器名（halo-es）直接访问 elasticsearch
- `-p 5601:5601`：端口映射配置

kibana 启动一般比较慢，需要多等待一会，可以通过命令查看日志：

```sh
docker logs -f kibana
```

在浏览器输入地址访问：http://halo:5601，即可看到结果

### 安装 IK 分词器

ElasticSearch 在创建倒排索引时需要对文档分词；在搜索时，需要对用户输入内容分词。但默认的分词规则对中文处理并不友好。

我们在 Kibana 的 DevTools 中测试：

```json
POST /_analyze
{
  "analyzer": "standard",
  "text": "你好,世界! Hello,World!"
}
```

语法说明：

+ POST：请求方式
+ /_analyze：请求路径，这里省略了 http://halo:9200，有 kibana 帮我们补充
+ 请求参数，JSON 风格：`analyzer`：分词器类型，这里是默认的 standard 分词器；`text`：要分词的内容

```json
{
  "tokens" : [
    {
      "token" : "你",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "<IDEOGRAPHIC>",
      "position" : 0
    },
    {
      "token" : "好",
      "start_offset" : 1,
      "end_offset" : 2,
      "type" : "<IDEOGRAPHIC>",
      "position" : 1
    },
    {
      "token" : "世",
      "start_offset" : 3,
      "end_offset" : 4,
      "type" : "<IDEOGRAPHIC>",
      "position" : 2
    },
    {
      "token" : "界",
      "start_offset" : 4,
      "end_offset" : 5,
      "type" : "<IDEOGRAPHIC>",
      "position" : 3
    },
    {
      "token" : "hello",
      "start_offset" : 7,
      "end_offset" : 12,
      "type" : "<ALPHANUM>",
      "position" : 4
    },
    {
      "token" : "world",
      "start_offset" : 13,
      "end_offset" : 18,
      "type" : "<ALPHANUM>",
      "position" : 5
    }
  ]
}
```

处理中文分词，一般会使用 IK 分词器。https://github.com/medcl/elasticsearch-analysis-ik

#### 在线安装 IK 插件

```sh
# 进入容器内部
docker exec -it elasticsearch /bin/bash

# 在线下载并安装
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.14.1/elasticsearch-analysis-ik-7.14.1.zip

#退出
exit
#重启容器
docker restart elasticsearch
```

#### 离线安装 IK 插件

查看数据卷目录

安装插件需要知道 elasticsearch 的 plugins 目录位置，而我们用了数据卷挂载，因此需要查看 elasticsearch 的数据卷目录，通过下面命令查看:

```sh
docker volume inspect es-plugins
```

显示结果：

```
[
    {
        "CreatedAt": "2021-09-11T12:50:57+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/es-plugins/_data",
        "Name": "es-plugins",
        "Options": null,
        "Scope": "local"
    }
]
```

说明 plugins 目录被挂载到了：`/var/lib/docker/volumes/es-plugins/_data ` 这个目录中。

将 ik 分词器解压缩，重命名为 ik，上传到 es 容器的插件数据卷中后重启容器

```sh
docker restart halo-es
```

#### 测试分词器

IK 分词器包含两种模式：

* `ik_smart` ：最少切分，粗粒度

* `ik_max_word` ：最细切分，细粒度

```json
POST /_analyze
{
  "analyzer": "ik_max_word",
  "text": "你好,我的世界! Hello,World!"
}
```

结果：

```json
{
  "tokens" : [
    {
      "token" : "你好",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "我",
      "start_offset" : 3,
      "end_offset" : 4,
      "type" : "CN_CHAR",
      "position" : 1
    },
    {
      "token" : "的",
      "start_offset" : 4,
      "end_offset" : 5,
      "type" : "CN_CHAR",
      "position" : 2
    },
    {
      "token" : "世界",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 3
    },
    {
      "token" : "hello",
      "start_offset" : 9,
      "end_offset" : 14,
      "type" : "ENGLISH",
      "position" : 4
    },
    {
      "token" : "world",
      "start_offset" : 15,
      "end_offset" : 20,
      "type" : "ENGLISH",
      "position" : 5
    }
  ]
}
```

#### 扩展和停用词词典

随着互联网的发展，“造词运动”也越发的频繁。出现了很多新的词语，在原有的词汇列表中并不存在。

所以我们的词汇也需要不断的更新，IK 分词器提供了扩展词汇的功能。

打开 IK 分词器 config 目录：

```sh
cd /var/lib/docker/volumes/es-plugins/_data/ik/config
```

在 IKAnalyzer.cfg.xml 配置文件内容添加：

```xml
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
        <comment>IK Analyzer 扩展配置</comment>
        <!--用户可以在这里配置自己的扩展字典 -->
        <entry key="ext_dict">ext.dic</entry>
         <!--用户可以在这里配置自己的扩展停止词字典-->
        <entry key="ext_stopwords">stopwort.dic</entry>
        <!--用户可以在这里配置远程扩展字典 -->
        <!-- <entry key="remote_ext_dict">words_location</entry> -->
        <!--用户可以在这里配置远程扩展停止词字典-->
        <!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>
```

新建一个 ext.dic，可以参考 config 目录下复制一个配置文件进行修改

```properties
我的世界
```

stopwort.dic 添加

```properties
的
```

重启 ElasticSearch 

```sh
docker restart es

# 查看 日志
docker logs -f elasticsearch
```

日志中已经成功加载 ext.dic 配置文件

测试

```json
POST /_analyze
{
  "analyzer": "ik_max_word",
  "text": "你好,我的世界! Hello,World!"
}
```

```json
{
  "tokens" : [
    {
      "token" : "你好",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "我的世界",
      "start_offset" : 3,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 1
    },
    {
      "token" : "世界",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 2
    },
    {
      "token" : "hello",
      "start_offset" : 9,
      "end_offset" : 14,
      "type" : "ENGLISH",
      "position" : 3
    },
    {
      "token" : "world",
      "start_offset" : 15,
      "end_offset" : 20,
      "type" : "ENGLISH",
      "position" : 4
    }
  ]
}
```

> 注意当前文件的编码必须是 UTF-8 格式，严禁使用 Windows 记事本编辑

## DSL 索引库操作

索引库就类似数据库表，mapping 映射就类似表的结构。我们要向 es 中存储数据，必须先创建“库”和“表”。

### mapping 映射属性

mapping 是对索引库中文档的约束，常见的mapping属性包括：

- type：字段数据类型，常见的简单类型有：
  - 字符串：text（可分词的文本）、keyword（精确值，例如：品牌、国家、ip 地址）
  - 数值：long、integer、short、byte、double、float、
  - 布尔：boolean
  - 日期：date
  - 对象：object
- index：是否创建索引，默认为 true
- analyzer：使用哪种分词器
- properties：该字段的子字段

例如下面的 JSON 文档：

```json
{
    "age": 21,
    "weight": 52.1,
    "isMarried": false,
    "info": "黑马程序员Java讲师",
    "email": "zy@itcast.cn",
    "score": [99.1, 99.5, 98.9],
    "name": {
        "firstName": "云",
        "lastName": "赵"
    }
}
```

对应的每个字段映射（mapping）：

- age：类型为 integer；参与搜索，因此需要 index 为 true；无需分词器
- weight：类型为 float；参与搜索，因此需要 index 为 true；无需分词器
- isMarried：类型为 boolean；参与搜索，因此需要 index 为 true；无需分词器
- info：类型为字符串，需要分词，因此是 text；参与搜索，因此需要 index 为 true；分词器可以用 ik_smart
- email：类型为字符串，但是不需要分词，因此是 keyword；不参与搜索，因此需要 index 为 false；无需分词器
- score：虽然是数组，但是我们只看元素的类型，类型为 float；参与搜索，因此需要 index 为 true；无需分词器
- name：类型为 object，需要定义多个子属性
  - name.firstName：类型为字符串，但是不需要分词，因此是 keyword；参与搜索，因此需要 index 为 true；无需分词器
  - name.lastName：类型为字符串，但是不需要分词，因此是 keyword；参与搜索，因此需要 index 为 true；无需分词器

### 索引库的 CRUD

这里统一使用 Kibana 编写 DSL 的方式来演示。

#### 创建索引库和映射

基本语法：

+ 请求方式：PUT
+ 请求路径：/索引库名，可以自定义
+ 请求参数：mapping 映射

```json
PUT /索引库名称
{
  "mappings": {
    "properties": {
      "字段名":{
        "type": "text",
        "analyzer": "ik_smart"
      },
      "字段名2":{
        "type": "keyword",
        "index": "false"
      },
      "字段名3":{
        "properties": {
          "子字段": {
            "type": "keyword"
          }
        }
      },
      // ...略
    }
  }
}
```

示例：

```json
PUT /heima
{
  "mappings": {
    "properties": {
      "info":{
        "type": "text",
        "analyzer": "ik_smart"
      },
      "email":{
        "type": "keyword",
        "index": "falsae"
      },
      "name":{
        "properties": {
          "firstName": {
            "type": "keyword"
          }
        }
      },
      // ... 略
    }
  }
}
```

#### 查询索引库

基本语法：

- 请求方式：GET

- 请求路径：/索引库名

- 请求参数：无

```
GET /索引库名
```

#### 删除索引库

语法：

- 请求方式：DELETE

- 请求路径：/索引库名

- 请求参数：无

```
DELETE /索引库名
```

#### 修改索引库

倒排索引结构虽然不复杂，但是一旦数据结构改变（比如改变了分词器），就需要重新创建倒排索引，这简直是灾难。因此索引库一旦创建，无法修改 mapping。

虽然无法修改 mapping 中已有的字段，但是却允许添加新的字段到 mapping 中，因为不会对倒排索引产生影响。

语法说明：

```json
PUT /索引库名/_mapping
{
  "properties": {
    "新字段名": {
      "type": "xxxx"
    }
  }
}
```

#### 索引库的 CRUD 小结

- 创建索引库：PUT /索引库名
- 查询索引库：GET /索引库名
- 删除索引库：DELETE /索引库名
- 添加字段：PUT /索引库名/_mapping

## DSL 文档操作

### 新增文档

 语法：

```json
POST /索引库名/_doc/文档id
{
    "字段1": "值1",
    "字段2": "值2",
    "字段3": {
        "子属性1": "值3",
        "子属性2": "值4"
    },
    // ...
}
```

示例：

```json
POST /halo/_doc/1
{
  "info": "黑马程序员Java讲师",
  "email": "zy@itcast.cn",
  "name": {
    "firstName": "云",
    "lastName": "赵"
  }
}
```

### 查询文档

根据 rest 风格，新增是 post，查询应该是 get，不过查询一般都需要条件，这里我们把文档 id 带上。

语法：

```
GET /{索引库名称}/_doc/{id}
```

通过 kibana 查看数据：

```
GET /halo/_doc/1
```

### 删除文档

删除使用 DELETE 请求，同样，需要根据 id 进行删除：

语法：

```
DELETE /{索引库名}/_doc/id值
```

示例：

```
DELETE /halo/_doc/1
```

### 修改文档

修改有两种方式：

- 全量修改：直接覆盖原来的文档
- 增量修改：修改文档中的部分字段

#### 全量修改

全量修改是覆盖原来的文档，其本质是：

- 根据指定的 id 删除文档
- 新增一个相同 id 的文档

> 注意：如果根据 id 删除时，id 不存在，第二步的新增也会执行，也就从修改变成了新增操作了。

语法：

```json
PUT /{索引库名}/_doc/文档id
{
    "字段1": "值1",
    "字段2": "值2",
    // ... 略
}
```

示例：

```json
PUT /halo/_doc/1
{
  "info": "黑马程序员高级Java讲师2",
  "email": "zy@itcast.cn",
  "name": {
    "firstName": "云",
    "lastName": "赵"
  }
}
```

#### 增量修改

增量修改是只修改指定 id 匹配的文档中的部分字段。

语法：

```json
POST /{索引库名}/_update/文档id
{
    "doc": {
         "字段名": "新的值",
    }
}
```

示例：

```
POST /halo/_update/1
{
  "doc": {
    "email": "ZhaoYun@itcast.cn"
  }
}
```

### 文档操作总结

- 创建文档：POST /{索引库名}/_doc/文档id   { JSON 文档 }
- 查询文档：GET /{索引库名}/_doc/文档id
- 删除文档：DELETE /{索引库名}/_doc/文档id
- 修改文档：
  - 全量修改：PUT /{索引库名}/_doc/文档id { JSON 文档 }
  - 增量修改：POST /{索引库名}/_update/文档id { "doc": {字段}}

## Rest Client 索引库操作

ElasticSearch 官方提供了各种不同语言的客户端，用来操作 ElasticSearch。这些客户端的本质就是组装 DSL 语句，通过 http 请求发送给 ElasticSearch。官方文档地址：https://www.elastic.co/guide/en/elasticsearch/client/index.html

其中的 Java Rest Client 又包括两种：

- Java Low Level Rest Client
- Java High Level Rest Client

我们学习的是 Java HighLevel Rest Client 客户端 API

### 创建测试环境

#### 初始化项目

创建数据库，建立数据表

```sql
CREATE TABLE `tb_hotel`  (
    `id` bigint(20) NOT NULL COMMENT '酒店id',
    `name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '酒店名称',
    `address` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '酒店地址',
    `price` int(10) NOT NULL COMMENT '酒店价格',
    `score` int(2) NOT NULL COMMENT '酒店评分',
    `brand` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '酒店品牌',
    `city` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '所在城市',
    `star_name` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL COMMENT '酒店星级，1星到5星，1钻到5钻',
    `business` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL COMMENT '商圈',
    `latitude` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '纬度',
    `longitude` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '经度',
    `pic` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL COMMENT '酒店图片',
    PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Compact;
```

导入数据库数据：[链接](https://github.com/Lanqilu/HaloElasticSearch/blob/master/doc/database/tb_hotel.sql)

初始项目代码：[链接](https://github.com/Lanqilu/HaloElasticSearch/commit/d64b305ccf9ca67b8a18bafee3df7163e7dd8246)

#### mapping 映射分析

创建索引库，最关键的是 mapping 映射，而 mapping 映射要考虑的信息包括：

- 字段名
- 字段数据类型
- 是否参与搜索
- 是否需要分词
- 如果分词，分词器是什么？

其中：

- 字段名、字段数据类型，可以参考数据表结构的名称和类型
- 是否参与搜索要分析业务来判断，例如图片地址，就无需参与搜索
- 是否分词呢要看内容，内容如果是一个整体就无需分词，反之则要分词
- 分词器，我们可以统一使用 ik_max_word

来看下酒店数据的索引库结构：

```json
PUT /hotel
{
  "mappings": {
    "properties": {
      "id": {
        "type": "keyword"
      },
      "name":{
        "type": "text",
        "analyzer": "ik_max_word",
        "copy_to": "all"
      },
      "address":{
        "type": "keyword",
        "index": false
      },
      "price":{
        "type": "integer"
      },
      "score":{
        "type": "integer"
      },
      "brand":{
        "type": "keyword",
        "copy_to": "all"
      },
      "city":{
        "type": "keyword",
        "copy_to": "all"
      },
      "starName":{
        "type": "keyword"
      },
      "business":{
        "type": "keyword"
      },
      "location":{
        "type": "geo_point"
      },
      "pic":{
        "type": "keyword",
        "index": false
      },
      "all":{
        "type": "text",
        "analyzer": "ik_max_word"
      }
    }
  }
}
```

几个特殊字段说明：

- location：地理坐标，里面包含精度、纬度
- all：一个组合字段，其目的是将多字段的值 利用 copy_to 合并，提供给用户搜索

 ES 中支持两种地理坐标数据类型：

•geo_point：由纬度（latitude）和经度（longitude）确定的一个点。例如："32.8752345, 120.2981576"

•geo_shape：有多个geo_point组成的复杂几何图形。例如一条直线，"LINESTRING (-77.03653 38.897676, -77.009051 38.889939)"

字段拷贝可以使用 copy_to 属性将当前字段拷贝到指定字段。示例：

```json
"all": {
  "type": "text",
  "analyzer": "ik_max_word"
},
"brand": {
  "type": "keyword",
  "copy_to": "all"
}
```

#### 初始化 RestClient

在 ElasticSearch 提供的 API 中，与 ElasticSearch 一切交互都封装在一个名为 RestHighLevelClient 的类中，必须先完成这个对象的初始化，建立与 ElasticSearch 的连接。

分为三步：

① 引入 ElasticSearch 的 RestHighLevelClient 依赖：

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
</dependency>
```

② 因为 SpringBoot 默认的 ElasticSearch 版本是 7.6.2，所以我们需要覆盖默认的 ElasticSearch 版本，与 ElasticSearch 版本保持一致

```xml
<properties>
    <java.version>1.8</java.version>
    <elasticsearch.version>7.14.1</elasticsearch.version>
</properties>
```

③ 初始化 RestHighLevelClient：

初始化的代码如下：

```java
RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(HttpHost.create("http://halo:9200")));
```

这里为了单元测试方便，我们创建一个测试类 HotelIndexTest，然后将初始化的代码编写在 `@BeforeEach` 方法中：

```java
public class HotelIndexTest {

    private RestHighLevelClient client;

    @Test
    void testInit() {
        System.out.println("client = " + client);
    }

    @BeforeEach
    void setUp() {
        this.client = new RestHighLevelClient(RestClient.builder(HttpHost.create("http://halo:9200")));
    }

    @AfterEach
    void tearDown() throws IOException {
        this.client.close();
    }
}
```

### 创建索引库

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.zlx2xrhd54w.png)

代码分为三步：

1. 创建 Request 对象。因为是创建索引库的操作，因此 Request 是 CreateIndexRequest
2. 添加请求参数，其实就是 DSL 的 JSON 参数部分。因为 JSON 字符串很长，这里是定义了静态字符串常量 MAPPING_TEMPLATE，让代码看起来更加优雅。
3. 发送请求，`client.indices()` 方法的返回值是 IndicesClient 类型，封装了所有与索引库操作有关的方法。

在 hotel-demo 中的 HotelIndexTest 测试类中，编写单元测试，实现创建索引：

```java
@Test
void createHotelIndex() throws IOException {
    // 1.创建Request对象
    CreateIndexRequest request = new CreateIndexRequest("hotel");
    // 2.准备请求的参数：DSL语句
    request.source(MAPPING_TEMPLATE, XContentType.JSON);
    // 3.发送请求
    client.indices().create(request, RequestOptions.DEFAULT);
}
```

### 删除索引库

删除索引库的 DSL 语句非常简单：

```json
DELETE /hotel
```

与创建索引库相比：

- 请求方式从 PUT 变为 DELTE
- 请求路径不变
- 无请求参数

所以代码的差异，注意体现在 Request 对象上。依然是三步走：

- 创建 Request 对象。这次是 DeleteIndexRequest 对象
- 准备参数。这里是无参
- 发送请求。改用 delete 方法

在 hotel-demo 中的 HotelIndexTest 测试类中，编写单元测试，实现删除索引：

```java
@Test
void testDeleteHotelIndex() throws IOException {
    // 1.创建Request对象
    DeleteIndexRequest request = new DeleteIndexRequest("hotel");
    // 2.发送请求
    client.indices().delete(request, RequestOptions.DEFAULT);
}
```

### 判断索引库是否存在

判断索引库是否存在，本质就是查询，对应的DSL是：

```json
GET /hotel
```

因此与删除的 Java 代码流程是类似的。依然是三步走：

- 创建 Request 对象。这次是 GetIndexRequest 对象
- 准备参数。这里是无参
- 发送请求。改用 exists 方法

```java
@Test
void testExistsHotelIndex() throws IOException {
    // 1.创建Request对象
    GetIndexRequest request = new GetIndexRequest("hotel");
    // 2.发送请求
    boolean exists = client.indices().exists(request, RequestOptions.DEFAULT);
    // 3.输出
    System.err.println(exists ? "索引库已经存在！" : "索引库不存在！");
}
```

### RestAPI 小结

JavaRestClient 操作 ElasticSearch 的流程基本类似。核心是 `client.indices()` 方法来获取索引库的操作对象。

索引库操作的基本步骤：

- 初始化 RestHighLevelClient
- 创建 XxxIndexRequest。Xxx 是Create、Get、Delete
- 准备 DSL（ Create时需要，其它是无参）
- 发送请求。调用 `RestHighLevelClient#indices().xxx()` 方法，xxx 是  create、exists、delete

## Rest Client 文档操作

去数据库查询酒店数据，导入到 hotel 索引库，实现酒店数据的 CRUD。基本步骤如下：

+ 初始化 JavaRestClient
+ 利用 JavaRestClient 新增酒店数据
+ 利用 JavaRestClient 根据id查询酒店数据
+ 利用 JavaRestClient 删除酒店数据
+ 利用 JavaRestClient 修改酒店数据

### 初始化 JavaRestClient

为了与索引库操作分离，我们再次参加一个测试类，做两件事情：

- 初始化 RestHighLevelClient，同上
- 我们的酒店数据在数据库，需要利用 IHotelService 去查询，所以注入这个接口

```java
@SpringBootTest
public class HotelDocumentTest {
    @Autowired
    private IHotelService hotelService;

    private RestHighLevelClient client;

    @BeforeEach
    void setUp() {
        this.client = new RestHighLevelClient(RestClient.builder(HttpHost.create("http://halo:9200")));
    }

    @AfterEach
    void tearDown() throws IOException {
        this.client.close();
    }
}
```

### 新增文档

我们要将数据库的酒店数据查询出来，写入 ElasticSearch 中。

#### 索引库实体类

数据库查询后的结果是一个 Hotel 类型的对象。结构如下：

```java
@Data
@TableName("tb_hotel")
public class Hotel {
    @TableId(type = IdType.INPUT)
    private Long id;
    private String name;
    private String address;
    private Integer price;
    private Integer score;
    private String brand;
    private String city;
    private String starName;
    private String business;
    private String longitude;
    private String latitude;
    private String pic;
}
```

与我们的索引库结构存在差异：

- longitude 和 latitude 需要合并为 location

因此，我们需要定义一个新的类型，与索引库结构吻合：

```java
package cn.itcast.hotel.pojo;

import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class HotelDoc {
    private Long id;
    private String name;
    private String address;
    private Integer price;
    private Integer score;
    private String brand;
    private String city;
    private String starName;
    private String business;
    private String location;
    private String pic;

    public HotelDoc(Hotel hotel) {
        this.id = hotel.getId();
        this.name = hotel.getName();
        this.address = hotel.getAddress();
        this.price = hotel.getPrice();
        this.score = hotel.getScore();
        this.brand = hotel.getBrand();
        this.city = hotel.getCity();
        this.starName = hotel.getStarName();
        this.business = hotel.getBusiness();
        this.location = hotel.getLatitude() + ", " + hotel.getLongitude();
        this.pic = hotel.getPic();
    }
}
```

#### 语法说明

新增文档的 DSL 语句如下：

```json
POST /{索引库名}/_doc/1
{
    "name": "Jack",
    "age": 21
}
```

对应的 Java 代码如下：

```java
@Test
void testIndexDocument() throws IOException {
    // 1.创建request对象
    IndexRequest request = new IndexRequest("indexName").id("1");
    // 2.准备JSON文档
    request.source("{\"name\": \"Jack\", \"age\": 21}", XContentType.JSON);
    // 3.发送请求
    client.index(request, RequestOptions.DEFAULT);
}
```

可以看到与创建索引库类似，同样是三步走：

- 创建 Request 对象
- 准备请求参数，也就是 DSL 中的 JSON 文档
- 发送请求

变化的地方在于，这里直接使用 `client.xxx()` 的 API，不再需要 `client.indices()` 了。

#### 完整代码

我们导入酒店数据，基本流程一致，但是需要考虑几点变化：

- 酒店数据来自于数据库，我们需要先查询出来，得到 Hotel 对象
- Hotel 对象需要转为 HotelDoc对象
- HotelDoc 需要序列化为 JSON 格式

因此，代码整体步骤如下：

- 根据 id 查询酒店数据 Hotel
- 将 Hotel 封装为 HotelDoc
- 将 HotelDoc 序列化为 JSON
- 创建 IndexRequest，指定索引库名和 id
- 准备请求参数，也就是 JSON 文档
- 发送请求

在 hotel-demo 的 HotelDocumentTest 测试类中，编写单元测试：

```java
@Test
void testAddDocument() throws IOException {
    // 1.根据id查询酒店数据
    Hotel hotel = hotelService.getById(61083L);
    // 2.转换为文档类型
    HotelDoc hotelDoc = new HotelDoc(hotel);
    // 3.将HotelDoc转json
    String json = JSON.toJSONString(hotelDoc);

    // 1.准备Request对象
    IndexRequest request = new IndexRequest("hotel").id(hotelDoc.getId().toString());
    // 2.准备Json文档
    request.source(json, XContentType.JSON);
    // 3.发送请求
    client.index(request, RequestOptions.DEFAULT);
}
```

### 查询文档

#### 语法说明

查询的 DSL 语句如下：

```json
GET /hotel/_doc/{id}
```

非常简单，因此代码大概分两步：

- 准备 Request 对象
- 发送请求

不过查询的目的是得到结果，解析为 HotelDoc，因此难点是结果的解析。示例代码如下：

```java
@Test
void testGetDocumentById() throws IOException {
    // 1.创建request对象
    GetRequest request = new GetRequest("indexName", "1");
    // 2.发送请求，得到结果
    GetResponse response = client.get(request, RequestOptions.DEFAULT);
    // 3.解析结果
    String json = response.getSourceAsString();
    System.out.println(json);
}
```

可以看到，结果是一个 JSON，其中文档放在一个 `_source` 属性中，因此解析就是拿到 `_source`，反序列化为 Java 对象即可。

与之前类似，也是三步走：

- 准备 Request 对象。这次是查询，所以是 GetRequest
- 发送请求，得到结果。因为是查询，这里调用 `client.get()` 方法
- 解析结果，就是对 JSON 做反序列化

#### 完整代码

在 hotel-demo 的 HotelDocumentTest 测试类中，编写单元测试：

```java
@Test
void testGetDocumentById() throws IOException {
    // 1.准备Request
    GetRequest request = new GetRequest("hotel", "61083");
    // 2.发送请求，得到响应
    GetResponse response = client.get(request, RequestOptions.DEFAULT);
    // 3.解析响应结果
    String json = response.getSourceAsString();
    HotelDoc hotelDoc = JSON.parseObject(json, HotelDoc.class);
    System.out.println(hotelDoc);
}
```

### 修改文档

#### 语法说明

修改我们讲过两种方式：

- 全量修改：本质是先根据id删除，再新增
- 增量修改：修改文档中的指定字段值

在 RestClient 的 API 中，全量修改与新增的 API 完全一致，判断依据是 ID：

- 如果新增时，ID 已经存在，则修改
- 如果新增时，ID 不存在，则新增

这里不再赘述，我们主要关注增量修改。

```java
@Test
void testUpdateDocumentById() throws IOException {
    // 1.创建request对象
    UpdateRequest request = new UpdateRequest("indexName", "1");
    // 2.准备参数，每2个参数为一对 key value    
    request.doc("age", 18, "name", "Rose");
    // 3.更新文档
    client.update(request, RequestOptions.DEFAULT);
}
```

与之前类似，也是三步走：

- 准备 Request 对象。这次是修改，所以是 UpdateRequest
- 准备参数。也就是 JSON 文档，里面包含要修改的字段
- 更新文档。这里调用 `client.update()` 方法

#### 完整代码

在 hotel-demo 的 HotelDocumentTest 测试类中，编写单元测试：

```java
@Test
void testUpdateDocument() throws IOException {
    // 1.准备Request
    UpdateRequest request = new UpdateRequest("hotel", "61083");
    // 2.准备请求参数
    request.doc(
        "price", "952",
        "starName", "四钻"
    );
    // 3.发送请求
    client.update(request, RequestOptions.DEFAULT);
}
```

### 删除文档

删除的 DSL 为是这样的：

```json
DELETE /hotel/_doc/{id}
```

与查询相比，仅仅是请求方式从 DELETE 变成 GET，可以想象 Java 代码应该依然是三步走：

- 准备 Request 对象，因为是删除，这次是 DeleteRequest 对象。要指定索引库名和 id
- 准备参数，无参
- 发送请求。因为是删除，所以是 `client.delete()` 方法

```java
@Test
void testDeleteDocument() throws IOException {
    // 1.准备Request
    DeleteRequest request = new DeleteRequest("hotel", "61083");
    // 2.发送请求
    client.delete(request, RequestOptions.DEFAULT);
}
```

### 批量导入文档

案例需求：利用 BulkRequest 批量将数据库数据导入到索引库中。

步骤如下：

- 利用 mybatis-plus 查询酒店数据

- 将查询到的酒店数据（Hotel）转换为文档类型数据（HotelDoc）

- 利用 JavaRestClient 中的 BulkRequest 批处理，实现批量新增文档

#### 语法说明

批量处理 BulkRequest，其本质就是将多个普通的 CRUD 请求组合在一起发送。

其中提供了一个 add 方法，用来添加其他请求：

- IndexRequest，也就是新增
- UpdateRequest，也就是修改
- DeleteRequest，也就是删除

因此 Bulk 中添加了多个 IndexRequest，就是批量新增功能了。示例：

```java
@Test
void testBulk() throws IOException {
    // 1.创建Bulk请求
    BulkRequest request = new BulkRequest();
    // 2.添加要批量提交的请求：这里添加了两个新增文档的请求
    request.add(new IndexRequest("hotel")
                .id("101").source("json source", XContentType.JSON));
    request.add(new IndexRequest("hotel")
                .id("102").source("json source2", XContentType.JSON));
    // 3.发起bulk请求
    client.bulk(request, RequestOptions.DEFAULT);
}
```

其实还是三步走：

- 创建 Request 对象。这里是 BulkRequest
- 准备参数。批处理的参数，就是其它 Request 对象，这里就是多个 IndexRequest
- 发起请求。这里是批处理，调用的方法为 `client.bulk()` 方法

我们在导入酒店数据时，将上述代码改造成 for 循环处理即可。

#### 完整代码

在 hotel-demo 的 HotelDocumentTest 测试类中，编写单元测试：

```java
@Test
void testBulkRequest() throws IOException {
    // 批量查询酒店数据
    List<Hotel> hotels = hotelService.list();

    // 1.创建 Request
    BulkRequest request = new BulkRequest();
    // 2.准备参数，添加多个新增的 Request
    for (Hotel hotel : hotels) {
        // 2.1.转换为文档类型 HotelDoc
        HotelDoc hotelDoc = new HotelDoc(hotel);
        // 2.2.创建新增文档的 Request 对象
        request.add(new IndexRequest("hotel")
                    .id(hotelDoc.getId().toString())
                    .source(JSON.toJSONString(hotelDoc), XContentType.JSON));
    }
    // 3.发送请求
    client.bulk(request, RequestOptions.DEFAULT);
}
```

测试，批量查询

```
GET /hotel/_search
```

### Rest Client 文档操作小结

文档操作的基本步骤：

- 初始化 RestHighLevelClient
- 创建 XxxRequest。Xxx 是 Index、Get、Update、Delete、Bulk
- 准备参数（Index、Update、Bulk时需要）
- 发送请求。调用 `RestHighLevelClient#.xxx()` 方法，xxx 是 index、get、update、delete、bulk
- 解析结果（Get时需要）

## DSL 查询文档

ElasticSearch 的查询依然是基于 JSON 风格的 DSL 来实现的。

### DSL 查询分类

ElasticSearch 提供了基于 JSON 的 DSL（[Domain Specific Language](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)）来定义查询。常见的查询类型包括：

- 查询所有：查询出所有数据，一般测试用。例如：match_all

- 全文检索查询：利用分词器对用户输入内容分词，然后去倒排索引库中匹配。例如：
  - match_query
  - multi_match_query
- 精确查询：根据精确词条值查找数据，一般是查找 keyword、数值、日期、boolean 等类型字段。例如：
  - ids
  - range
  - term
- 地理（geo）查询：根据经纬度查询。例如：
  - geo_distance
  - geo_bounding_box
- 复合（compound）查询：复合查询可以将上述各种查询条件组合起来，合并查询条件。例如：
  - bool
  - function_score

查询的语法基本一致：

```json
GET /indexName/_search
{
  "query": {
    "查询类型": {
      "查询条件": "条件值"
    }
  }
}
```

 我们以查询所有为例，其中：

- 查询类型为 match_all
- 没有查询条件

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  }
}
```

其它查询无非就是查询类型、查询条件的变化。

### 全文检索查询

#### 使用场景

全文检索查询的基本流程如下：

- 对用户搜索的内容做分词，得到词条
- 根据词条去倒排索引库中匹配，得到文档 id
- 根据文档 id 找到文档，返回给用户

比较常用的场景包括：

- 商城的输入框搜索
- 百度输入框搜索

因为是拿着词条去匹配，因此参与搜索的字段也必须是可分词的 text 类型的字段。

#### 基本语法

常见的全文检索查询包括：

- match 查询：单字段查询
- multi_match 查询：多字段查询，任意一个字段符合条件就算符合查询条件

match 查询语法如下：

```json
GET /indexName/_search
{
  "query": {
    "match": {
      "FIELD": "TEXT"
    }
  }
}
```

mulit_match 语法如下：

```json
GET /indexName/_search
{
  "query": {
    "multi_match": {
      "query": "TEXT",
      "fields": ["FIELD1", " FIELD12"]
    }
  }
}
```

#### 使用示例

match 查询示例：

```json
GET /hotel/_search
{
  "query": {
    "match": {
      "all": "如家外滩"
    }
  }
}
```

multi_match 查询示例：

```json
GET /hotel/_search
{
  "query": {
    "multi_match": {
      "query": "如家外滩",
      "fields": ["brand","name","business"]
    }
  }
}
```

可以看到，两种查询结果是一样的，为什么？

因为我们将 brand、name、business 值都利用 copy_to 复制到了 all 字段中。因此你根据三个字段搜索，和根据 all 字段搜索效果当然一样了。

但是，搜索字段越多，对查询性能影响越大，因此建议采用 copy_to，然后单字段查询的方式。

match 和 multi_match 的区别是什么？

- match：根据一个字段查询
- multi_match：根据多个字段查询，参与查询字段越多，查询性能越差

### 精准查询

精确查询一般是查找 keyword、数值、日期、boolean 等类型字段。所以不会对搜索条件分词。常见的有：

- term：根据词条精确值查询
- range：根据值的范围查询

#### term 查询

因为精确查询的字段搜是不分词的字段，因此查询的条件也必须是**不分词**的词条。查询时，用户输入的内容跟自动值完全匹配时才认为符合条件。如果用户输入的内容过多，反而搜索不到数据。

语法说明：

```json
GET /indexName/_search
{
  "query": {
    "term": {
      "FIELD": {
        "value": "VALUE"
      }
    }
  }
}
```

示例：

当我搜索的是精确词条时，能正确查询出结果：

```json
GET /hotel/_search
{
  "query": {
    "term": {
      "city": {
        "value": "上海"
      }
    }
  }
}
```

但是，当我搜索的内容不是词条，而是多个词语形成的短语时，反而搜索不到：

```json
GET /hotel/_search
{
  "query": {
    "term": {
      "city": {
        "value": "上海杭州"
      }
    }
  }
}
```

#### range 查询

范围查询，一般应用在对数值类型做范围过滤的时候。比如做价格范围过滤。

基本语法：

```json
GET /indexName/_search
{
  "query": {
    "range": {
      "FIELD": {
        "gte": 10,
        "lte": 20
      }
    }
  }
}
```

+ gte 代表大于等于，gt 则代表大于
+ lte 代表小于等于，lt 则代表小于

示例：

```json
GET /hotel/_search
{
  "query": {
    "range": {
      "price": {
        "gte": 1000,
        "lte": 3000
      }
    }
  }
}
```

#### 精准查询小结

精确查询常见的有哪些？

- term 查询：根据词条精确匹配，一般搜索 keyword 类型、数值类型、布尔类型、日期类型字段
- range 查询：根据数值范围查询，可以是数值、日期的范围

### 地理坐标查询

所谓的地理坐标查询，其实就是根据经纬度查询，[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-queries.html)

常见的使用场景包括：

- 携程：搜索我附近的酒店
- 滴滴：搜索我附近的出租车
- 微信：搜索我附近的人

#### 矩形范围查询

矩形范围查询，也就是 geo_bounding_box 查询，查询坐标落在某个矩形范围的所有文档：

查询时，需要指定矩形的**左上**、**右下**两个点的坐标，然后画出一个矩形，落在该矩形内的都是符合条件的点。

语法如下：

```json
GET /indexName/_search
{
  "query": {
    "geo_bounding_box": {
      "FIELD": {
        "top_left": {
          "lat": 31.1,
          "lon": 121.5
        },
        "bottom_right": {
          "lat": 30.9,
          "lon": 121.7
        }
      }
    }
  }
}
```

#### 附近查询

附近查询，也叫做距离查询（geo_distance）：查询到指定中心点小于某个距离值的所有文档。

换句话来说，在地图上找一个点作为圆心，以指定距离为半径，画一个圆，落在圆内的坐标都算符合条件：

语法说明：

```json
GET /indexName/_search
{
  "query": {
    "geo_distance": {
      "distance": "15km",
      "FIELD": "31.21,121.5"
    }
  }
}
```

我们先搜索陆家嘴附近 15km 的酒店：

```json
GET /hotel/_search
{
  "query": {
    "geo_distance": {
      "distance": "2km",
      "location": "31.21,121.5"
    }
  }
}
```

### 复合查询

复合（compound）查询：复合查询可以将其它简单查询组合起来，实现更复杂的搜索逻辑。常见的有两种：

- fuction score：算分函数查询，可以控制文档相关性算分，控制文档排名
- bool query：布尔查询，利用逻辑关系组合多个其它的查询，实现复杂搜索

#### 相关性算分

当我们利用 match 查询时，文档结果会根据与搜索词条的关联度打分（_score），返回结果时按照分值降序排列。

例如，我们搜索 "虹桥如家"，结果如下：

```json
[
  {
    "_score" : 17.850193,
    "_source" : {
      "name" : "虹桥如家酒店真不错",
    }
  },
  {
    "_score" : 12.259849,
    "_source" : {
      "name" : "外滩如家酒店真不错",
    }
  },
  {
    "_score" : 11.91091,
    "_source" : {
      "name" : "迪士尼如家酒店真不错",
    }
  }
]
```

在 ElasticSearch 中，早期使用的打分算法是 [TF-IDF 算法](https://www.ruanyifeng.com/blog/2013/03/tf-idf.html)，在后来的 5.1 版本升级中，ElasticSearch 将算法改进为 [BM25 算法](https://www.jianshu.com/p/1e498888f505)

TF-IDF 算法有一各缺陷，就是词条频率越高，文档得分也会越高，单个词条对文档影响较大。而 BM25 则会让单个词条的算分有一个上限，曲线更加平滑：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.32gad6dlyzu0.png)

#### 算分函数查询

根据相关度打分是比较合理的需求，但合理的不一定是产品经理需要的。

以百度为例，你搜索的结果中，并不是相关度越高排名越靠前，而是谁掏的钱多排名就越靠前。要想认为控制相关性算分，就需要利用 ElasticSearch 中的 function score 查询了。

语法说明：

![算分函数查询](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/算分函数查询.epggagjz1ts.svg)

function score 查询中包含四部分内容：

- 原始查询条件：query 部分，基于这个条件搜索文档，并且基于 BM25 算法给文档打分，原始算分（query score)
- 过滤条件：filter 部分，符合该条件的文档才会重新算分
- 算分函数：符合 filter 条件的文档要根据这个函数做运算，得到的函数算分（function score），有四种函数
  - weight：函数结果是常量
  - field_value_factor：以文档中的某个字段值作为函数结果
  - random_score：以随机数作为函数结果
  - script_score：自定义算分函数算法
- 运算模式：算分函数的结果、原始查询的相关性算分，两者之间的运算方式，包括：
  - multiply：相乘
  - replace：用 function score 替换 query score
  - 其它，例如：sum、avg、max、min

function score 的运行流程如下：

- 根据原始条件查询搜索文档，并且计算相关性算分，称为原始算分（query score）
- 根据过滤条件，过滤文档
- 符合**过滤条件**的文档，基于算分函数运算，得到函数算分（function score）
- 将原始算分（query score）和函数算分（function score）基于运算模式做运算，得到最终结果，作为相关性算分。

因此，其中的关键点是：

- 过滤条件：决定哪些文档的算分被修改
- 算分函数：决定函数算分的算法
- 运算模式：决定最终算分结果

示例

需求：给“如家”这个品牌的酒店排名靠前一些。翻译一下这个需求，转换为之前说的四个要点：

- 原始条件：不确定，可以任意变化
- 过滤条件：brand = "如家"
- 算分函数：可以简单粗暴，直接给固定的算分结果，weight
- 运算模式：比如求和

因此最终的 DSL 语句如下：

```json
GET /hotel/_search
{
  "query": {
    "function_score": {
      "query": {
        "match": {
          "all": "外滩"
        }
      },
      "functions": [
        {
          "filter": {
            "term": {
              "brand": "如家"
            }
          },
          "weight": 10
        }
      ],
      "boost_mode": "sum"
    }
  }
}
```

#### 布尔查询

布尔查询是一个或多个查询子句的组合，每一个子句就是一个子查询。子查询的组合方式有：

- must：必须匹配每个子查询，类似“与”
- should：选择性匹配子查询，类似“或”
- must_not：必须不匹配，**不参与算分**，类似“非”
- filter：必须匹配，不参与算分

比如在搜索酒店时，除了关键字搜索外，我们还可能根据品牌、价格、城市等字段做过滤。

每一个不同的字段，其查询的条件、方式都不一样，必须是多个不同的查询，而要组合这些查询，就必须用 bool 查询了。

需要注意的是，搜索时，参与打分的字段越多，查询的性能也越差。因此这种多条件查询时，建议这样做：

- 搜索框的关键字搜索，是全文检索查询，使用 must 查询，参与算分
- 其它过滤条件，采用 filter 查询。不参与算分

语法示例：

```json
GET /hotel/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "city": "上海"
          }
        }
      ],
      "should": [
        {
          "term": {
            "brand": "皇冠假日"
          }
        },
        {
          "term": {
            "brand": "华美达"
          }
        }
      ],
      "must_not": [
        {
          "range": {
            "price": {
              "lte": 500
            }
          }
        }
      ],
      "filter": [
        {
          "range": {
            "score": {
              "gte": 45
            }
          }
        }
      ]
    }
  }
}
```

案例示例：

需求：搜索名字包含“如家”，价格不高于 400，在坐标 31.21,121.5 周围 10km 范围内的酒店。

分析：

- 名称搜索，属于全文检索查询，应该参与算分。放到 must 中
- 价格不高于 400，用 range 查询，属于过滤条件，不参与算分。放到 must_not 中
- 周围 10km 范围内，用 geo_distance 查询，属于过滤条件，不参与算分。放到 filter 中

```json
GET /hotel/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "如家"
          }
        }
      ],
      "must_not": [
        {
          "range": {
            "price": {
              "gt": 400
            }
          }
        }
      ],
      "filter": [
        {
          "geo_distance": {
            "distance": "10km",
            "location": {
              "lat": 31.21,
              "lon": 121.5
            }
          }
        }
      ]
    }
  }
}
```

bool 查询有几种逻辑关系？

- must：必须匹配的条件，可以理解为“与”
- should：选择性匹配的条件，可以理解为“或”
- must_not：必须不匹配的条件，不参与打分
- filter：必须匹配的条件，不参与打分

## DSL 搜索结果处理

搜索的结果可以按照用户指定的方式去处理或展示。

### 排序

ElasticSearch默认是根据相关度算分（_score）来排序，但是也支持自定义方式对搜索[结果排序](https://www.elastic.co/guide/en/elasticsearch/reference/current/sort-search-results.html)。可以排序字段类型有：keyword 类型、数值类型、地理坐标类型、日期类型等。

#### 普通字段排序

keyword、数值、日期类型排序的语法基本一致。

**语法**：

```json
GET /indexName/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "FIELD": "desc"  // 排序字段、排序方式ASC、DESC
    }
  ]
}
```

排序条件是一个数组，也就是可以写多个排序条件。按照声明的顺序，当第一个条件相等时，再按照第二个条件排序，以此类推

**示例**：

需求描述：酒店数据按照用户评价（score）降序排序，评价相同的按照价格（price）升序排序

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "score": "desc"
    },
    {
      "price": "asc"
    }
  ]
}
```

#### 地理坐标排序

地理坐标排序略有不同。

**语法说明**：

```json
GET /indexName/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "_geo_distance" : {
          "FIELD" : "纬度，经度", // 文档中geo_point类型的字段名、目标坐标点
          "order" : "asc", // 排序方式
          "unit" : "km" // 排序的距离单位
      }
    }
  ]
}
```

这个查询的含义是：

- 指定一个坐标，作为目标点
- 计算每一个文档中，指定字段（必须是 geo_point 类型）的坐标到目标点的距离是多少
- 根据距离排序

**示例：**

需求描述：实现对酒店数据按照到你的位置坐标的距离升序排序

提示：获取经纬度的方式：https://lbs.amap.com/demo/jsapi-v2/example/map/click-to-get-lnglat/

假设我的位置是：31.034661，121.612282，寻找我周围距离最近的酒店。

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "_geo_distance": {
        "location": {
          "lat": 31.034661,
          "lon": 121.612282
        },
        "order": "asc",
        "unit": "km"
      }
    }
  ]
}
```

### 分页

ElasticSearch 默认情况下只返回 top10 的数据。而如果要查询更多数据就需要修改分页参数了。ElasticSearch中通过修改 from、size 参数来控制要返回的分页结果：

- from：从第几个文档开始
- size：总共查询几个文档

类似于 MySQL 中的 `limit ?, ?`

#### 基本的分页

分页的基本语法如下：

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "price": {
        "order": "asc"
      }
    }
  ],
  "from": 0,
  "size": 5
}
```

#### 深度分页问题

现在，我要查询 990~1000 的数据，查询逻辑要这么写：

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "from": 990, // 分页开始的位置，默认为0
  "size": 10, // 期望获取的文档总数
  "sort": [
    {"price": "asc"}
  ]
}
```

这里是查询 990 开始的数据，也就是 第 990~1000 条 数据。

不过，ElasticSearch 内部分页时，必须先查询 0~1000 条，然后截取其中的 990 ~ 1000 的这 10 条：

查询 top 1000，如果 ElasticSearch 是单点模式，这并无太大影响。

但是 ElasticSearch 将来一定是集群，例如我集群有 5 个节点，我要查询 top 1000 的数据，并不是每个节点查询 200 条就可以了。

因为节点 A 的 top 200，在另一个节点可能排到 10000 名以外了。

因此要想获取整个集群的 top 1000，必须先查询出每个节点的 top 1000，汇总结果后，重新排名，重新截取 top 1000。

那如果我要查询 9900~10000 的数据呢？是不是要先查询 top 10000呢？那每个节点都要查询 10000 条？汇总到内存中？

当查询分页深度较大时，汇总数据过多，对内存和 CPU 会产生非常大的压力，因此 ElasticSearch 会禁止 from + size 超过 10000 的请求。

针对深度分页，ElasticSearch 提供了两种解决方案，[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/paginate-search-results.html)：

- search after：分页时需要排序，原理是从上一次的排序值开始，查询下一页数据。官方推荐使用的方式。
- scroll：原理将排序后的文档 id 形成快照，保存在内存。官方已经不推荐使用。

#### 分页小结

分页查询的常见实现方案以及优缺点：

- `from + size`：
  - 优点：支持随机翻页
  - 缺点：深度分页问题，默认查询上限（from + size）是 10000
  - 场景：百度、京东、谷歌、淘宝这样的随机翻页搜索
- `after search`：
  - 优点：没有查询上限（单次查询的 size 不超过 10000）
  - 缺点：只能向后逐页查询，不支持随机翻页
  - 场景：没有随机翻页需求的搜索，例如手机向下滚动翻页

- `scroll`：
  - 优点：没有查询上限（单次查询的 size 不超过 10000）
  - 缺点：会有额外内存消耗，并且搜索结果是非实时的
  - 场景：海量数据的获取和迁移。从 ES 7.1开始不推荐，建议用 after search 方案。

### 高亮

高亮显示的实现分为两步：

- 给文档中的所有关键字都添加一个标签，例如 `<em>` 标签
- 页面给 `<em>` 标签编写 CSS 样式

高亮的语法：

```json
GET /hotel/_search
{
  "query": {
    "match": {
      "FIELD": "TEXT"
    }
  },
  "highlight": {
    "fields": {
      "FIELD": {
        "pre_tags": "<em>",
        "post_tags": "</em>"
      }
    }
  }
}
```

**注意：**

- 高亮是对关键字高亮，因此搜索条件必须带有关键字，而不能是范围这样的查询。
- 默认情况下，高亮的字段，必须与搜索指定的字段一致，否则无法高亮
- 如果要对非搜索字段高亮，则需要添加一个属性： `"require_field_match": "false"`

示例：

```json
GET /hotel/_search
{
  "query": {
    "match": {
      "all": "如家"
    }
  },
  "highlight": {
    "fields": {
      "name": {
        "require_field_match": "false"
      }
    }
  }
}
```

### 搜索结果处理小结

查询的 DSL 是一个大的 JSON 对象，包含下列属性：

- query：查询条件
- from 和 size：分页条件
- sort：排序条件
- highlight：高亮条件

```json
GET /hotel/_search
{
  "query": {
    "match": {
      "name": "如家"
    }
  },
  "from": 0, // 分页开始的位置
  "size": 20, // 期望获取的文档总数
  "sort": [ 
    {  "price": "asc" }, // 普通排序
    {
      "_geo_distance" : { // 距离排序
          "location" : "31.040699,121.618075", 
          "order" : "asc",
          "unit" : "km"
      }
    }
  ],
  "highlight": {
    "fields": { // 高亮字段
      "name": {
        "pre_tags": "<em>",  // 用来标记高亮字段的前置标签
        "post_tags": "</em>" // 用来标记高亮字段的后置标签
      }
    }
  }
}
```

## Rest Client 查询文档

文档的查询同样适用 RestHighLevelClient 对象，基本步骤包括：

- 准备 Request 对象
- 准备请求参数
- 发起请求
- 解析响应

### 快速入门

我们以 match_all 查询为例

#### 发起查询请求

```java
@Test
void testMatchAll() throws IOException {
    // 1.准备Request
    SearchRequest request = new SearchRequest("hotel");
    // 2.组织DSL参数
    request.source().query(QueryBuilders.matchAllQuery());
    // 3.发送请求，得到响应结果
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // ...解析响应结果
}
```

代码解读：

- 第一步，创建 `SearchRequest` 对象，指定索引库名

- 第二步，利用 `request.source()` 构建 DSL，DSL 中可以包含查询、分页、排序、高亮等
  - `query()`：代表查询条件，利用 `QueryBuilders.matchAllQuery()` 构建一个 match_all 查询的 DSL
- 第三步，利用 `client.search()` 发送请求，得到响应

这里关键的 API 有两个：

+ 一个是 `request.source()`，其中包含了查询、排序、分页、高亮等所有功能。
+ 另一个是 `QueryBuilders`，其中包含 match、term、function_score、bool 等各种查询：

#### 解析响应

ElasticSearch 返回的结果是一个 JSON 字符串，结构包含：

```json
{
   "took" : 0,
   "timed_out" : false,
   "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "info" : "Java讲师", 	"name" : "赵云",
       }
      },
      // ...
    ]
  }
}
```

- `hits`：命中的结果
  - `total`：总条数，其中的 value 是具体的总条数值
  - `max_score`：所有结果中得分最高的文档的相关性算分
  - `hits`：搜索结果的文档数组，其中的每个文档都是一个 JSON 对象
    - `_source`：文档中的原始数据，也是 JSON 对象

因此，我们解析响应结果，就是逐层解析 JSON 字符串，流程如下：

```java
@Test
void testMatchAll() throws IOException {
    // ... 略
    // 4.解析结果
    SearchHits searchHits = response.getHits();
    // 4.1.查询的总条数
    long total = searchHits.getTotalHits().value;
    // 4.2.查询的结果数组
    SearchHit[] hits = searchHits.getHits();
    for (SearchHit hit : hits) {
        // 4.3.得到source
        String json = hit.getSourceAsString();
        // 4.4.打印
        System.out.println(json);
    }
}
```

- `SearchHits`：通过 `response.getHits()` 获取，就是 JSON 中的最外层的hits，代表命中的结果
  - `SearchHits#getTotalHits().value`：获取总条数信息
  - `SearchHits#getHits()`：获取 SearchHit 数组，也就是文档数组
    - `SearchHit#getSourceAsString()`：获取文档结果中的_source，也就是原始的 JSON 文档数据

#### 完整代码

```java
    @Test
    void testMatchAll() throws IOException {
        // 1.准备 Request
        SearchRequest request = new SearchRequest("hotel");
        // 2.组织 DSL 参数
        request.source().query(QueryBuilders.matchAllQuery());
        // 3.发送请求，得到响应结果
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 4.解析结果
        SearchHits searchHits = response.getHits();
        // 4.1.查询的总条数
        long total = searchHits.getTotalHits().value;
        System.err.println("total = " + total);
        // 4.2.查询的结果数组
        SearchHit[] hits = searchHits.getHits();
        for (SearchHit hit : hits) {
            // 4.3.得到source
            String json = hit.getSourceAsString();
            // 反序列化
            HotelDoc hotelDoc = JSON.parseObject(json, HotelDoc.class);
            // 4.4.打印
            System.out.println(hotelDoc);
        }
    }
```

#### 快速入门小结

查询的基本步骤是：

1. 创建 SearchRequest 对象
2. 准备 `Request.source()`，也就是 DSL。
   + QueryBuilders 来构建查询条件
   + 传入 `Request.source()` 的 `query()` 方法
3. 发送请求，得到结果
4. 解析结果（参考 JSON 结果，从外到内，逐层解析）

### match 查询

全文检索的 match 和 multi_match 查询与 match_all 的 API 基本一致。差别是查询条件，也就是 query 的部分。

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  }
}

GET /hotel/_search
{
  "query": {
    "match": {
      "all": "如家"
    }
  }
}

GET /hotel/_search
{
  "query": {
    "multi_match": {
      "query": "如家",
      "fields": ["brand", "name"]
    }
  }
}
```

因此，Java 代码上的差异主要是 `request.source().query()` 中的参数了。同样是利用 QueryBuilders 提供的方法：

```java
// 单字段查询
QueryBuilders.matchQuery("all", "如家");
// 多字段查询
QueryBuilders.multiMatchQuery("如家", "name", "business");
```

而结果解析代码则完全一致，可以抽取并共享。

完整代码如下：

```java
@Test
void testMatch() throws IOException {
    // 1.准备Request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    request.source().query(QueryBuilders.matchQuery("all", "如家"));
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 4.解析响应
    handleResponse(response);
}
```

> IDEA 代码抽取 Ctrl + Alt + M

### 精确查询

精确查询主要是两者：

- term：词条精确匹配
- range：范围查询

与之前的查询相比，差异同样在查询条件，其它都一样。

```json
GET /hotel/_search
{
  "query": {
    "term": {
      "city": "杭州"
    }
  }
}
GET /hotel/_search
{
  "query": {
    "range": {
      "price": { "gte": 100, "lte": 150 }
    }
  }
}
```

查询条件构造的 API 如下：

```java
// 词条查询
QueryBuilders.termQuery("city", "杭州"); 
// 范围查询
QueryBuilders.rangeQuery("price").gte(100).lte(150);
```

### 布尔查询

布尔查询是用 must、must_not、filter 等方式组合其它查询，代码示例如下：

```java
// 创建布尔查询
BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
// 添加must条件
boolQuery.must(QueryBuilders.termQuery("city", "杭州")); 
// 添加filter条件
boolQuery.filter(QueryBuilders.rangeQuery("price").lte(250));
```

```json
GET /hotel/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "term": { "city": "杭州" }
        }
      ],
      "filter": [
        {
          "range": {
            "price": { "lte": 250 }
          }
        }
      ]
    }
  }
}
```

可以看到，API 与其它查询的差别同样是在查询条件的构建，QueryBuilders，结果解析等其他代码完全不变。

示例代码：

```java
@Test
void testBool() throws IOException {
    // 1.准备Request
    SearchRequest request = new SearchRequest("hotel");
    
    // 2.准备DSL
    // 2.1.准备BooleanQuery
    BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
    // 2.2.添加term
    boolQuery.must(QueryBuilders.termQuery("city", "上海"));
    // 2.3.添加range
    boolQuery.filter(QueryBuilders.rangeQuery("price").lte(250));
    
    request.source().query(boolQuery);
    
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    
    // 4.解析响应
    handleResponse(response);
}
```



## Rest Client 搜索结果处理

### 排序与分页

搜索结果的排序和分页是与 query 同级的参数，因此同样是使用 `request.source()` 来设置。

对应的 API 如下：

```java
// 查询 
request.source().query(QueryBuilders.matchAllQuery());
// 排序
request.source().sort("price", SortOrder.ASC);
// 分页
request.source().from(0).size(5);
```

```json
GET /indexName/_search
{
  "query": {
    "match_all": {}
  },
  "from": 0,
  "size": 5, 
  "sort": [
    {
      "FIELD": "desc"  
    },
  ]
}
```

代码示例：

```java
@Test
void testPageAndSort() throws IOException {
    // 页码，每页大小
    int page = 1, size = 5;
    // 1.准备Request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    // 2.1.query
    request.source().query(QueryBuilders.matchAllQuery());
    // 2.2.排序 sort
    request.source().sort("price", SortOrder.ASC);
    // 2.3.分页 from、size
    request.source().from((page - 1) * size).size(size);
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 4.解析响应
    handleResponse(response);
}
```

### 高亮

高亮的代码与之前代码差异较大，有两点：

- 查询的 DSL：其中除了查询条件，还需要添加高亮条件，同样是与 query 同级。
- 结果解析：结果除了要解析 _source 文档数据，还要解析高亮结果

#### 高亮请求构建

```json
GET /hotel/_search
{
  "query": {
    "match": {
      "all": "如家"
    }
  },
  "highlight": {
    "fields": {
      "name": {
        "require_field_match": "false"
      }
    }
  }
}
```

高亮请求的构建 API 如下：

```java
request.source().highlighter(new HighlightBuilder().field("name").requireFieldMatch(false));
```

上述代码省略了查询条件部分，但是大家不要忘了：高亮查询必须使用全文检索查询，并且要有搜索关键字，将来才可以对关键字高亮。

示例代码如下：

```java
@Test
void testHighlight() throws IOException {
    // 1.准备Request
    SearchRequest request = new SearchRequest("hotel");
    // 2.准备DSL
    // 2.1.query
    request.source().query(QueryBuilders.matchQuery("all", "如家"));
    // 2.2.高亮
    request.source().highlighter(new HighlightBuilder().field("name").requireFieldMatch(false));
    // 3.发送请求
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 4.解析响应
    handleResponse(response);
}
```

#### 高亮结果解析

高亮的结果与查询的文档结果默认是分离的，并不在一起。

```json
{
  "_index" : "hotel",
  "_type" : "_doc",
  "_id" : "339952837",
  "_score" : 2.8947515,
  "_source" : {
    "id" : 339952837,
    "name" : "如家酒店(北京良乡西路店)",
    "price" : 159,
    "score" : 46,
    "brand" : "如家",
    "city" : "北京",
    "location" : "39.73167, 116.132482",
    "pic" : "t0.jpg"
  },
  "highlight" : {
    "name" : [
      "<em>如家</em>酒店(北京良乡西路店)",
    ]
  }
}
```

因此解析高亮的代码需要额外处理：

```java
// 获取source
HotelDoc hotelDoc = JSON.parseObject(hit.getSourceAsString(), HotelDoc.class);
// 处理高亮
Map<String, HighlightField> highlightFields = hit.getHighlightFields();
if (!CollectionUtils.isEmpty(highlightFields)) {
    // 获取高亮字段结果
    HighlightField highlightField = highlightFields.get("name");
    if (highlightField != null) {
        // 取出高亮结果数组中的第一个，就是酒店名称
        String name = highlightField.getFragments()[0].string();
        hotelDoc.setName(name);
    }
}
```

代码解读：

- 第一步：从结果中获取 source。`hit.getSourceAsString()`，这部分是非高亮结果，JSON 字符串。还需要反序列为 HotelDoc 对象
- 第二步：获取高亮结果。`hit.getHighlightFields()`，返回值是一个 Map，key 是高亮字段名称，值是 HighlightField 对象，代表高亮值
- 第三步：从 Map 中根据高亮字段名称，获取高亮字段值对象HighlightField
- 第四步：从HighlightField 中获取 Fragments，并且转为字符串。这部分就是真正的高亮字符串了
- 第五步：用高亮的结果替换 HotelDoc 中的非高亮结果

## 酒店搜索案例

下面，我们通过酒店搜索案例来实战演练下之前学习的知识。

我们实现四部分功能：

- 酒店搜索和分页
- 酒店结果过滤
- 我周边的酒店
- 酒店竞价排名

启动 hotel-demo 项目，其默认端口是 8089，访问 http://localhost:8090，就能看到项目页面了。

### 酒店搜索和分页

案例需求：实现黑马旅游的酒店搜索功能，完成关键字搜索和分页

#### 需求分析

- 请求方式：POST
- 请求路径：/hotel/list
- 请求参数：JSON 对象，包含4个字段：
  - key：搜索关键字
  - page：页码
  - size：每页大小
  - sortBy：排序，目前暂不实现
- 返回值：分页查询，需要返回分页结果 PageResult，包含两个属性：
  - `total`：总条数
  - `List<HotelDoc>`：当前页的数据

因此，我们实现业务的流程如下：

- 步骤一：定义实体类，接收请求参数的 JSON 对象
- 步骤二：编写 controller，接收页面的请求
- 步骤三：编写业务实现，利用 RestHighLevelClient 实现搜索、分页

#### 定义实体类

实体类有两个，一个是前端的请求参数实体，一个是服务端应该返回的响应结果实体。

① 请求参数，前端请求的 JSON 结构如下：

```json
{
    "key": "搜索关键字",
    "page": 1,
    "size": 3,
    "sortBy": "default"
}
```

因此，我们在 `cn.itcast.hotel.pojo` 包下定义一个实体类：

```java
package cn.itcast.hotel.pojo;

import lombok.Data;

@Data
public class RequestParams {
    private String key;
    private Integer page;
    private Integer size;
    private String sortBy;
}
```

② 返回值，分页查询，需要返回分页结果 PageResult，包含两个属性：

- `total` ：总条数
- `List<HotelDoc>` ：当前页的数据

因此，我们在 `cn.itcast.hotel.pojo` 中定义返回结果：

```java
package cn.itcast.hotel.pojo;

import lombok.Data;

import java.util.List;

@Data
public class PageResult {
    private Long total;
    private List<HotelDoc> hotels;

    public PageResult() {
    }

    public PageResult(Long total, List<HotelDoc> hotels) {
        this.total = total;
        this.hotels = hotels;
    }
}
```

#### 定义 controller

定义一个 HotelController，声明查询接口，满足下列要求：

- 请求方式：Post
- 请求路径：/hotel/list
- 请求参数：对象，类型为 RequestParam
- 返回值：PageResult，包含两个属性
  - `Long total`：总条数
  - `List<HotelDoc> hotels`：酒店数据

因此，我们在 `cn.itcast.hotel.web` 中定义 HotelController：

```java
@RestController
@RequestMapping("/hotel")
public class HotelController {

    @Autowired
    private IHotelService hotelService;
	// 搜索酒店数据
    @PostMapping("/list")
    public PageResult search(@RequestBody RequestParams params){
        return hotelService.search(params);
    }
}
```

#### 实现搜索业务

我们在 controller 调用了 IHotelService，并没有实现该方法，因此下面我们就在 IHotelService 中定义方法，并且去实现业务逻辑。

① 在 `cn.itcast.hotel.service` 中的 `IHotelService` 接口中定义一个方法：

```java
/**
 * 根据关键字搜索酒店信息
 * @param params 请求参数对象，包含用户输入的关键字 
 * @return 酒店文档列表
 */
PageResult search(RequestParams params);
```

② 实现搜索业务，肯定离不开 RestHighLevelClient，我们需要把它注册到 Spring 中作为一个 Bean。在 `cn.itcast.hotel` 中的 `HotelDemoApplication` 中声明这个 Bean：

```java
@Bean
public RestHighLevelClient client() {
    return new RestHighLevelClient(RestClient.builder(HttpHost.create("http://halo:9200")));
}
```

③ 在 `cn.itcast.hotel.service.impl` 中的 `HotelService` 中实现 search 方法：

```java
@Autowired
private RestHighLevelClient client;

@Override
public PageResult search(RequestParams params) {
    try {
        // 1.准备Request
        SearchRequest request = new SearchRequest("hotel");
        // 2.准备DSL
        // 2.1.query
        String key = params.getKey();
        if (key == null || "".equals(key)) {
            request.source().query(QueryBuilders.matchAllQuery());
        } else {
            request.source().query(QueryBuilders.matchQuery("all", key));
        }

        // 2.2.分页
        int page = params.getPage();
        int size = params.getSize();
        request.source().from((page - 1) * size).size(size);

        // 3.发送请求
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 4.解析响应
        return handleResponse(response);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}

// 结果解析
private PageResult handleResponse(SearchResponse response) {
    // 4.解析响应
    SearchHits searchHits = response.getHits();
    // 4.1.获取总条数
    long total = searchHits.getTotalHits().value;
    // 4.2.文档数组
    SearchHit[] hits = searchHits.getHits();
    // 4.3.遍历
    List<HotelDoc> hotels = new ArrayList<>();
    for (SearchHit hit : hits) {
        // 获取文档source
        String json = hit.getSourceAsString();
        // 反序列化
        HotelDoc hotelDoc = JSON.parseObject(json, HotelDoc.class);
        // 放入集合
        hotels.add(hotelDoc);
    }
    // 4.4.封装返回
    return new PageResult(total, hotels);
}
```

### 酒店结果过滤

需求：添加品牌、城市、星级、价格等过滤功能

#### 需求分析

包含的过滤条件有：

- brand：品牌值
- city：城市
- minPrice~maxPrice：价格范围
- starName：星级

我们需要做两件事情：

- 修改请求参数的对象 RequestParams，接收上述参数
- 修改业务逻辑，在搜索条件之外，添加一些过滤条件

#### 修改实体类

修改在 `cn.itcast.hotel.pojo` 包下的实体类 RequestParams：

```java
@Data
public class RequestParams {
    private String key;
    private Integer page;
    private Integer size;
    private String sortBy;
    // 下面是新增的过滤条件参数
    private String city;
    private String brand;
    private String starName;
    private Integer minPrice;
    private Integer maxPrice;
}
```

#### 修改搜索业务

在 HotelService 的 search 方法中，只有一个地方需要修改：`requet.source().query( ... )` 其中的查询条件。

在之前的业务中，只有 match 查询，根据关键字搜索，现在要添加条件过滤，包括：

- 品牌过滤：是 keyword 类型，用 term 查询
- 星级过滤：是 keyword 类型，用 term 查询
- 价格过滤：是数值类型，用 range 查询
- 城市过滤：是 keyword 类型，用 term 查询

多个查询条件组合，肯定是 boolean 查询来组合：

- 关键字搜索放到 must 中，参与算分
- 其它过滤条件放到 filter 中，不参与算分

因为条件构建的逻辑比较复杂，这里封装为一个函数，getBoolQueryBuilder 的代码如下：

```java
private BoolQueryBuilder getBoolQueryBuilder(RequestParams params) {
    BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
    String key = params.getKey();
    if (key == null || "".equals(key)) {
        boolQuery.must(QueryBuilders.matchAllQuery());
    } else {
        boolQuery.must(QueryBuilders.matchQuery("all", key));
    }
    // 条件过滤
    // 城市条件
    if (params.getCity() != null && !params.getCity().equals("")) {
        boolQuery.filter(QueryBuilders.termQuery("city", params.getCity()));
    }
    // 品牌条件
    if (params.getBrand() != null && !params.getBrand().equals("")) {
        boolQuery.filter(QueryBuilders.termQuery("brand", params.getBrand()));
    }
    // 星级
    if (params.getStarName() != null && !params.getStarName().equals("")) {
        boolQuery.filter(QueryBuilders.termQuery("starName", params.getStarName()));
    }
    // 价格
    if (params.getMinPrice() != null && params.getMaxPrice() != null) {
        boolQuery.filter(QueryBuilders.rangeQuery("price")
                         .gte(params.getMinPrice()).lte(params.getMaxPrice()));
    }
    return boolQuery;
}
```

### 我周边的酒店

需求：我附近的酒店

#### 需求分析

在酒店列表页的右侧，有一个小地图，点击地图的定位按钮，地图会找到你所在的位置，并且，在前端会发起查询请求，将你的坐标发送到服务端。

我们要做的事情就是基于这个 location 坐标，然后按照距离对周围酒店排序。实现思路如下：

- 修改 RequestParams 参数，接收 location 字段
- 修改 search 方法业务逻辑，如果 location 有值，添加根据 geo_distance 排序的功能

#### 修改实体类

修改在 `cn.itcast.hotel.pojo` 包下的实体类 RequestParams：

```java
package cn.itcast.hotel.pojo;

import lombok.Data;

@Data
public class RequestParams {
    private String key;
    private Integer page;
    private Integer size;
    private String sortBy;
    private String city;
    private String brand;
    private String starName;
    private Integer minPrice;
    private Integer maxPrice;
    // 我当前的地理坐标
    private String location;
}
```

#### 距离排序 API

我们以前学习过排序功能，包括两种：

- 普通字段排序
- 地理坐标排序

我们只讲了普通字段排序对应的 Java 写法。地理坐标排序只学过 DSL 语法，如下：

```json
GET /indexName/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "price": "asc"  
    },
    {
      "_geo_distance" : {
          "FIELD" : "纬度，经度",
          "order" : "asc",
          "unit" : "km"
      }
    }
  ]
}
```

对应 Java 代码

```java
// 价格排序
request.source().sort("price", SortOrder.ASC);
// 距离排序
request.source().sort(SortBuilders.geoDistanceSort("location", new GeoPoint("31.21, 121.5"))
                      .order(SortOrder.ASC).unit(DistanceUnit.KILOMETERS));
```

#### 添加距离排序

在 `cn.itcast.hotel.service.impl` 的 `HotelService` 的 `search` 方法中，添加一个排序功能：

```java
@Override
public PageResult search(RequestParams params) {
    try {
        // 1.准备Request
        SearchRequest request = new SearchRequest("hotel");
        // 2.准备DSL
        // 2.1.query
        // 构建 boolQuery
        BoolQueryBuilder boolQuery = getBoolQueryBuilder(params);
        request.source().query(boolQuery);

        // 2.2.分页
        int page = params.getPage();
        int size = params.getSize();
        request.source().from((page - 1) * size).size(size);

        // 排序
        String location = params.getLocation();
        if (location != null && !location.equals("")) {
            request.source().sort(SortBuilders
                                  .geoDistanceSort("location", new GeoPoint(location))
                                  .order(SortOrder.ASC)
                                  .unit(DistanceUnit.KILOMETERS));
        }

        // 3.发送请求
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 4.解析响应
        return handleResponse(response);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}
```

#### 排序距离显示

排序完成后，页面还要获取我附近每个酒店的具体**距离**值，这个值在响应结果中是独立的：

因此，我们在结果解析阶段，除了解析 source 部分以外，还要得到 sort 部分，也就是排序的距离，然后放到响应结果中。

我们要做两件事：

- 修改 HotelDoc，添加排序距离字段，用于页面显示
- 修改 HotelService 类中的 handleResponse 方法，添加对 sort 值的获取

① 修改HotelDoc类，添加距离字段

```java
package cn.itcast.hotel.pojo;

import lombok.Data;
import lombok.NoArgsConstructor;


@Data
@NoArgsConstructor
public class HotelDoc {
    private Long id;
    private String name;
    private String address;
    private Integer price;
    private Integer score;
    private String brand;
    private String city;
    private String starName;
    private String business;
    private String location;
    private String pic;
    // 排序时的 距离值
    private Object distance;

    public HotelDoc(Hotel hotel) {
        this.id = hotel.getId();
        this.name = hotel.getName();
        this.address = hotel.getAddress();
        this.price = hotel.getPrice();
        this.score = hotel.getScore();
        this.brand = hotel.getBrand();
        this.city = hotel.getCity();
        this.starName = hotel.getStarName();
        this.business = hotel.getBusiness();
        this.location = hotel.getLatitude() + ", " + hotel.getLongitude();
        this.pic = hotel.getPic();
    }
}
```

② 修改 HotelService 中的 handleResponse 方法

```java
// 结果解析
private PageResult handleResponse(SearchResponse response) {
    // 4.解析响应
    SearchHits searchHits = response.getHits();
    // 4.1.获取总条数
    long total = searchHits.getTotalHits().value;
    // 4.2.文档数组
    SearchHit[] hits = searchHits.getHits();
    // 4.3.遍历
    List<HotelDoc> hotels = new ArrayList<>();
    for (SearchHit hit : hits) {
        // 获取文档source
        String json = hit.getSourceAsString();
        // 反序列化
        HotelDoc hotelDoc = JSON.parseObject(json, HotelDoc.class);
        // 获取排序值 - location
        Object[] sortValues = hit.getSortValues();
        if (sortValues.length > 0) {
            Object sortValue = sortValues[0];
            hotelDoc.setDistance(sortValue);
        }

        // 放入集合
        hotels.add(hotelDoc);
    }
    // 4.4.封装返回
    return new PageResult(total, hotels);
}
```

### 酒店竞价排名

需求：让指定的酒店在搜索结果中排名置顶

#### 需求分析

要让指定酒店在搜索结果中排名置顶，页面会给指定的酒店添加**广告**标记。

我们之前学习过的 function_score 查询可以影响算分，算分高了，自然排名也就高了。而 function_score 包含 3 个要素：

- 过滤条件：哪些文档要加分
- 算分函数：如何计算 function score
- 加权方式：function score 与 query score 如何运算

这里的需求是：让**指定酒店**排名靠前。因此我们需要给这些酒店添加一个标记，这样在过滤条件中就可以根据这个标记来判断，是否要提高算分。

比如，我们给酒店添加一个字段：isAD，Boolean 类型：

- true：是广告
- false：不是广告

这样 function_score 包含 3 个要素就很好确定了：

- 过滤条件：判断 isAD 是否为 true
- 算分函数：我们可以用最简单暴力的 weight，固定加权值
- 加权方式：可以用默认的相乘，大大提高算分

因此，业务的实现步骤包括：

1. 给 HotelDoc 类添加 isAD 字段，Boolean 类型

2. 挑选几个你喜欢的酒店，给它的文档数据添加 isAD 字段，值为 true

3. 修改 search方法，添加 function score 功能，给 isAD 值为 true 的酒店增加权重

#### 修改 HotelDoc 实体

给 `cn.itcast.hotel.pojo` 包下的 HotelDoc 类添加 isAD 字段：

```java
@Data
@NoArgsConstructor
public class HotelDoc {
    private Long id;
    private String name;
    private String address;
    private Integer price;
    private Integer score;
    private String brand;
    private String city;
    private String starName;
    private String business;
    private String location;
    private String pic;
    // 排序时的距离值
    private Object distance;
    private Boolean isAD;

    public HotelDoc(Hotel hotel) {
        this.id = hotel.getId();
        this.name = hotel.getName();
        this.address = hotel.getAddress();
        this.price = hotel.getPrice();
        this.score = hotel.getScore();
        this.brand = hotel.getBrand();
        this.city = hotel.getCity();
        this.starName = hotel.getStarName();
        this.business = hotel.getBusiness();
        this.location = hotel.getLatitude() + ", " + hotel.getLongitude();
        this.pic = hotel.getPic();
    }
}
```

#### 添加广告标记

用 DSL 添加酒店广告标记

```java
POST /hotel/_update/36934
{
  "doc": {
    "isAD": true
  }
}
```

#### 添加算分函数查询

接下来我们就要修改查询条件了。之前是用的 boolean 查询，现在要改成 function_socre 查询。

function_score 查询结构如下：

```json
GET /hotel/_search
{
  "query": {
    "function_score": {
      "query": {
        "match": {
          "name": "外滩"
        }
      },
      "functions": [ 
        {
          "filter": {
            "term": {
              "brand": "如家"
            }
          },
          "weight": 5
        }
      ]
    }
  }
}
```

对应的 JavaAPI 如下

```java
FunctionScoreQueryBuilder functionScoreQueryBuilder = 
    QueryBuilders.functionScoreQuery(
        QueryBuilders.matchQuery("name", "外滩"),
        new FunctionScoreQueryBuilder.FilterFunctionBuilder[]{
            new FunctionScoreQueryBuilder.FilterFunctionBuilder(
                QueryBuilders.termQuery("brand", "如家"), 
                ScoreFunctionBuilders.weightFactorFunction(5)
            )
        }
	);
sourceBuilder.query(functionScoreQueryBuilder);
```

我们可以将之前写的 boolean 查询作为**原始查询**条件放到 query 中，接下来就是添加过滤条件、算分函数、加权模式了。所以原来的代码依然可以沿用。

修改 `cn.itcast.hotel.service.impl` 包下的 `HotelService` 类中的 `getQueryBuilder` 方法，添加算分函数查询：

```java
private FunctionScoreQueryBuilder getQueryBuilder(RequestParams params) {
    BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
    String key = params.getKey();
    if (key == null || "".equals(key)) {
        boolQuery.must(QueryBuilders.matchAllQuery());
    } else {
        boolQuery.must(QueryBuilders.matchQuery("all", key));
    }
    // 条件过滤
    // 城市条件
    if (params.getCity() != null && !params.getCity().equals("")) {
        boolQuery.filter(QueryBuilders.termQuery("city", params.getCity()));
    }
    // 品牌条件
    if (params.getBrand() != null && !params.getBrand().equals("")) {
        boolQuery.filter(QueryBuilders.termQuery("brand", params.getBrand()));
    }
    // 星级
    if (params.getStarName() != null && !params.getStarName().equals("")) {
        boolQuery.filter(QueryBuilders.termQuery("starName", params.getStarName()));
    }
    // 价格
    if (params.getMinPrice() != null && params.getMaxPrice() != null) {
        boolQuery.filter(QueryBuilders.rangeQuery("price")
                         .gte(params.getMinPrice()).lte(params.getMaxPrice()));
    }

    // 算分控制
    FunctionScoreQueryBuilder functionScoreQueryBuilder =
        QueryBuilders.functionScoreQuery(
        // 原始查询，相关性算分
        boolQuery,
        // function score
        new FunctionScoreQueryBuilder.FilterFunctionBuilder[]{
            // 一个 function score 元素
            new FunctionScoreQueryBuilder.FilterFunctionBuilder(
                // 过滤条件
                QueryBuilders.termQuery("isAD", true),
                // 算分函数
                ScoreFunctionBuilders.weightFactorFunction(10)
            )
        });

    return functionScoreQueryBuilder;
}
```

## 数据聚合

[聚合](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)（aggregation） 可以让我们极其方便的实现对数据的统计、分析、运算。例如：

- 什么品牌的手机最受欢迎？
- 这些手机的平均价格、最高价格、最低价格？
- 这些手机每月的销售情况如何？

实现这些统计功能的比数据库的 SQL 要方便的多，而且查询速度非常快，可以实现近实时搜索效果。

### 聚合的种类

聚合常见的有三类：

- 桶（Bucket）聚合：用来对文档做分组
  - TermAggregation：按照文档字段值分组，例如按照品牌值分组、按照国家分组
  - Date Histogram：按照日期阶梯分组，例如一周为一组，或者一月为一组

- 度量（Metric）聚合：用以计算一些值，比如：最大值、最小值、平均值等
  - Avg：求平均值
  - Max：求最大值
  - Min：求最小值
  - Stats：同时求 max、min、avg、sum 等
- 管道（pipeline）聚合：其它聚合的结果为基础做聚合

> **注意：**参加聚合的字段必须是 keyword、日期、数值、布尔类型

### DSL 实现聚合

现在，我们要统计所有数据中的酒店品牌有几种，其实就是按照品牌对数据分组。

此时可以根据酒店品牌的名称做聚合，也就是 Bucket 聚合。

#### Bucket 聚合语法

语法如下：

```json
GET /hotel/_search
{
  "size": 0,  // 设置 size 为 0，结果中不包含文档，只包含聚合结果
  "aggs": { // 定义聚合
    "brandAgg": { //给聚合起个名字
      "terms": { // 聚合的类型，按照品牌值聚合，所以选择term
        "field": "brand", // 参与聚合的字段
        "size": 5 // 希望获取的聚合结果数量
      }
    }
  }
}
```

结果如下：

```json
{
  "took" : 36,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 201,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [ ]
  },
  "aggregations" : {
    "brandAgg" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 96,
      "buckets" : [
        {
          "key" : "7天酒店",
          "doc_count" : 30
        },
        {
          "key" : "如家",
          "doc_count" : 30
        },
        {
          "key" : "皇冠假日",
          "doc_count" : 17
        },
        {
          "key" : "速8",
          "doc_count" : 15
        },
        {
          "key" : "万怡",
          "doc_count" : 13
        }
      ]
    }
  }
}
```

#### 聚合结果排序

默认情况下，Bucket 聚合会统计 Bucket 内的文档数量，记为 `_count`，并且按照 `_count` 降序排序。

我们可以指定 order 属性，自定义聚合的排序方式：

```json
GET /hotel/_search
{
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "order": {
          "_count": "asc" // 按照 _count 升序排列
        }, 
        "size": 5
      }
    }
  }
}
```

#### 限定聚合范围

默认情况下，Bucket 聚合是对索引库的所有文档做聚合，但真实场景下，用户会输入搜索条件，因此聚合必须是对搜索结果聚合。那么聚合必须添加限定条件。

我们可以限定要聚合的文档范围，只要添加 query 条件即可：

```json
GET /hotel/_search
{
  "query": {
    "range": {
      "price": {
        "lte": 200 // 只对200元以下的文档聚合
      }
    }
  }, 
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 5
      }
    }
  }
}
```

#### Metric 聚合语法

现在我们需要对桶内的酒店做运算，获取每个品牌的用户评分的 min、max、avg 等值。

这就要用到 Metric 聚合了，例如 stats 聚合：就可以获取 min、max、avg 等结果。

语法如下：

```json
GET /hotel/_search
{
  "size": 0, 
  "aggs": {
    "brandAgg": { 
      "terms": { 
        "field": "brand", 
        "size": 5
      },
      "aggs": { // 是brands聚合的子聚合，也就是分组后对每组分别计算
        "score_stats": { // 聚合名称
          "stats": { // 聚合类型，这里stats可以计算min、max、avg等
            "field": "score" // 聚合字段，这里是score
          }
        }
      }
    }
  }
}
```

这次的 score_stats 聚合是在 brandAgg 的聚合内部嵌套的子聚合。因为我们需要在每个桶分别计算。

另外，我们还可以给聚合结果做个排序：

```json
GET /hotel/_search
{
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 5,
        "order": {
          "scoreAgg.avg": "desc"
        }
      },
      "aggs": {
        "scoreAgg": {
          "stats": {
            "field": "score"
          }
        }
      }
    }
  }
}
```

#### DSL 实现聚合小结

aggs 代表聚合，与 query 同级，此时 query 的作用是？

- 限定聚合的的文档范围

聚合必须的三要素：

- 聚合名称
- 聚合类型
- 聚合字段

聚合可配置属性有：

- size：指定聚合结果数量
- order：指定聚合结果排序方式
- field：指定聚合字段

### Rest Client 实现聚合

#### API 语法

聚合条件与 query 条件同级别，因此需要使用 `request.source()` 来指定聚合条件。

聚合条件的语法：

```java
request.source().size(0);
request.source().aggregation(
    AggregationBuilders
    .terms("brand_agg")
    .field("brand")
    .size(20)
);
```

聚合的结果也与查询结果不同，API 也比较特殊。不过同样是 JSON 逐层解析：

```java
// 4. 解析结果
// 4.1 获取 aggregations
Aggregations aggregations = response.getAggregations();
// 4.2 根据名称获取聚合结果
Terms brandTerms = aggregations.get("brandAgg");
// 4.3 获取 buckets 并遍历
for (Terms.Bucket bucket : brandTerms.getBuckets()) {
    // 获取 key
    String key = bucket.getKeyAsString();
    System.out.println(key);
}
```

#### 业务需求

需求：搜索页面的品牌、城市等信息不应该是在页面写死，而是通过聚合索引库中的酒店数据得来的

分析：目前，页面的城市列表、星级列表、品牌列表都是写死的，并不会随着搜索结果的变化而变化。但是用户搜索条件改变时，搜索结果会跟着变化。

例如：用户搜索“东方明珠”，那搜索的酒店肯定是在上海东方明珠附近，因此，城市只能是上海，此时城市列表中就不应该显示北京、深圳、杭州这些信息了。也就是说，搜索结果中包含哪些城市，页面就应该列出哪些城市；搜索结果中包含哪些品牌，页面就应该列出哪些品牌。

如何得知搜索结果中包含哪些品牌？如何得知搜索结果中包含哪些城市？

使用聚合功能，利用 Bucket 聚合，对搜索结果中的文档基于品牌分组、基于城市分组，就能得知包含哪些品牌、哪些城市了。

因为是对搜索结果聚合，因此聚合是限定范围的聚合，也就是说聚合的限定条件跟搜索文档的条件一致。

返回结果是一个 Map 结构：

- key 是字符串，城市、星级、品牌、价格
- value 是集合，例如多个城市的名称

#### 业务实现

在 `cn.itcast.hotel.web` 包的 `HotelController` 中添加一个方法，遵循下面的要求：

- 请求方式：`POST`
- 请求路径：`/hotel/filters`
- 请求参数：`RequestParams`，与搜索文档的参数一致
- 返回值类型：`Map<String, List<String>>`

代码：

```java
@PostMapping("filters")
public Map<String, List<String>> getFilters(@RequestBody RequestParams params){
    return hotelService.getFilters(params);
}
```

这里调用了 IHotelService 中的 getFilters 方法，尚未实现。

在 `cn.itcast.hotel.service.IHotelService` 中定义新方法：

```java
Map<String, List<String>> filters(RequestParams params);
```

在 `cn.itcast.hotel.service.impl.HotelService` 中实现该方法：

```java
@Override
public Map<String, List<String>> getFilters(RequestParams params) {
    try {
        // 1. 准备 request
        SearchRequest request = new SearchRequest("hotel");

        // 2. 准备 DSL
        // query
        FunctionScoreQueryBuilder query = getQueryBuilder(params);
        request.source().highlighter(new HighlightBuilder().field("name").requireFieldMatch(false));
        request.source().query(query);
        // 2.1 设置 size = 0
        request.source().size(0);
        // 2.2 聚合
        HashMap<String, String> items = new HashMap<>();
        items.put("brand", "品牌");
        items.put("city", "城市");
        items.put("starName", "星级");
        for (String item : items.keySet()) {
            request.source().aggregation(AggregationBuilders
                                         .terms(item + "Agg")
                                         .field(item)
                                         .size(100));
        }
        // 3. 发出请求
        SearchResponse response = null;

        response = client.search(request, RequestOptions.DEFAULT);


        // 4. 解析结果
        // 4.1 获取 aggregations
        Aggregations aggregations = response.getAggregations();

        HashMap<String, List<String>> itemListHashMap = new HashMap<>();

        for (String item : items.keySet()) {
            // 4.2 根据名称获取聚合结果
            Terms brandTerms = aggregations.get(item + "Agg");
            // 4.3 获取 buckets 并遍历
            ArrayList<String> itemList = new ArrayList<>();
            for (Terms.Bucket bucket : brandTerms.getBuckets()) {
                // 获取 key
                itemList.add(bucket.getKeyAsString());
            }
            itemListHashMap.put(item, itemList);
        }
        return itemListHashMap;
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}
```

## 自动补全

当用户在搜索框输入字符时，我们应该提示出与该字符有关的搜索项，这种根据用户输入的字母，提示完整词条的功能，就是自动补全了。

因为需要根据拼音字母来推断，因此要用到拼音分词功能。

### 拼音分词器

要实现根据字母做补全，就必须对文档按照拼音分词。在 GitHub 上有 ElasticSearch的拼音分词插件。地址：https://github.com/medcl/elasticsearch-analysis-pinyin

安装方式与 IK 分词器一样，分三步：

1. 解压
2. 上传到虚拟机中，ElasticSearch 的 plugin 目录
3. 重启 ElasticSearch
4. 测试

详细安装步骤可以参考 IK 分词器的安装过程。

测试用法如下：

```json
POST /_analyze
{
  "text": "如家酒店还不错",
  "analyzer": "pinyin"
}
```

结果如下：

```json
{
  "tokens" : [
    {
      "token" : "ru",
      "start_offset" : 0,
      "end_offset" : 0,
      "type" : "word",
      "position" : 0
    },
    {
      "token" : "rjjdhbc",
      "start_offset" : 0,
      "end_offset" : 0,
      "type" : "word",
      "position" : 0
    },
    {
      "token" : "jia",
      "start_offset" : 0,
      "end_offset" : 0,
      "type" : "word",
      "position" : 1
    },
    {
      "token" : "jiu",
      "start_offset" : 0,
      "end_offset" : 0,
      "type" : "word",
      "position" : 2
    },
    {
      "token" : "dian",
      "start_offset" : 0,
      "end_offset" : 0,
      "type" : "word",
      "position" : 3
    },
    {
      "token" : "hai",
      "start_offset" : 0,
      "end_offset" : 0,
      "type" : "word",
      "position" : 4
    },
    {
      "token" : "bu",
      "start_offset" : 0,
      "end_offset" : 0,
      "type" : "word",
      "position" : 5
    },
    {
      "token" : "cuo",
      "start_offset" : 0,
      "end_offset" : 0,
      "type" : "word",
      "position" : 6
    }
  ]
}
```

### 自定义分词器

默认的拼音分词器会将每个汉字单独分为拼音，而我们希望的是每个词条形成一组拼音，需要对拼音分词器做个性化定制，形成自定义分词器。

ElasticSearch 中分词器（analyzer）的组成包含三部分：

- character filters：在 tokenizer 之前对文本进行处理。例如删除字符、替换字符
- tokenizer：将文本按照一定的规则切割成词条（term）。例如 keyword，就是不分词；还有 ik_smart
- tokenizer filter：将 tokenizer 输出的词条做进一步处理。例如大小写转换、同义词处理、拼音处理等

声明自定义分词器的语法如下：

```json
PUT /test
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "tokenizer": "ik_max_word",
          "filter": "py"
        }
      },
      "filter": {
        "py": {
          "type": "pinyin",
          "keep_full_pinyin": false,
          "keep_joined_full_pinyin": true,
          "keep_original": true,
          "limit_first_letter_length": 16,
          "remove_duplicated_term": true,
          "none_chinese_pinyin_tokenize": false
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "name": {
        "type": "text",
        "analyzer": "my_analyzer",
        "search_analyzer": "ik_smart"
      }
    }
  }
}
```

总结：

如何使用拼音分词器？

- 下载 pinyin 分词器

- 解压并放到 ElasticSearch 的 plugin 目录

- 重启即可

如何自定义分词器？

- 创建索引库时，在 settings 中配置，可以包含三部分：character filter、tokenizer、filter


拼音分词器注意事项？

- 为了避免搜索到同音字，搜索时不要使用拼音分词器

### 自动补全查询

ElasticSearch 提供了 [Completion Suggester](https://www.elastic.co/guide/en/elasticsearch/reference/7.6/search-suggesters.html) 查询来实现自动补全功能。这个查询会匹配以用户输入内容开头的词条并返回。为了提高补全查询的效率，对于文档中字段的类型有一些约束：

- 参与补全查询的字段必须是 completion 类型。

- 字段的内容一般是用来补全的多个词条形成的数组。

比如，一个这样的索引库：

```json
PUT /test2
{
  "mappings": {
    "properties": {
      "title":{
        "type": "completion"
      }
    }
  }
}
```

然后插入下面的数据：

```json
POST /test2/_doc
{
  "title": ["Sony", "WH-1000XM3"]
}
POST /test2/_doc
{
  "title": ["SK-II", "PITERA"]
}
POST /test2/_doc
{
  "title": ["Nintendo", "switch"]
}
```

查询的 DSL 语句如下：

```json
POST /test2/_search
{
  "suggest": {
    "title_suggest": {
      "text": "s", 
      "completion": {
        "field": "title", 
        "skip_duplicates": true, 
        "size": 10 
      }
    }
  }
}
```

### 实现酒店搜索框自动补全

现在，我们的 hotel 索引库还没有设置拼音分词器，需要修改索引库中的配置。但是我们知道索引库是无法修改的，只能删除然后重新创建。

另外，我们需要添加一个字段，用来做自动补全，将 brand、suggestion、city 等都放进去，作为自动补全的提示。

因此，总结一下，我们需要做的事情包括：

1. 修改 hotel 索引库结构，设置自定义拼音分词器

2. 修改索引库的 name、all 字段，使用自定义分词器

3. 索引库添加一个新字段 suggestion，类型为 completion 类型，使用自定义的分词器

4. 给 HotelDoc 类添加 suggestion 字段，内容包含 brand、business

5. 重新导入数据到 hotel 库

#### 修改酒店映射结构

代码如下：

```json
// 酒店数据索引库
PUT /hotel
{
  "settings": {
    "analysis": {
      "analyzer": {
        "text_anlyzer": {
          "tokenizer": "ik_max_word",
          "filter": "py"
        },
        "completion_analyzer": {
          "tokenizer": "keyword",
          "filter": "py"
        }
      },
      "filter": {
        "py": {
          "type": "pinyin",
          "keep_full_pinyin": false,
          "keep_joined_full_pinyin": true,
          "keep_original": true,
          "limit_first_letter_length": 16,
          "remove_duplicated_term": true,
          "none_chinese_pinyin_tokenize": false
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "id":{
        "type": "keyword"
      },
      "name":{
        "type": "text",
        "analyzer": "text_anlyzer",
        "search_analyzer": "ik_smart",
        "copy_to": "all"
      },
      "address":{
        "type": "keyword",
        "index": false
      },
      "price":{
        "type": "integer"
      },
      "score":{
        "type": "integer"
      },
      "brand":{
        "type": "keyword",
        "copy_to": "all"
      },
      "city":{
        "type": "keyword"
      },
      "starName":{
        "type": "keyword"
      },
      "business":{
        "type": "keyword",
        "copy_to": "all"
      },
      "location":{
        "type": "geo_point"
      },
      "pic":{
        "type": "keyword",
        "index": false
      },
      "all":{
        "type": "text",
        "analyzer": "text_anlyzer",
        "search_analyzer": "ik_smart"
      },
      "suggestion":{
          "type": "completion",
          "analyzer": "completion_analyzer"
      }
    }
  }
}
```

#### 修改 HotelDoc 实体

HotelDoc 中要添加一个字段，用来做自动补全，内容可以是酒店品牌、城市、商圈等信息。按照自动补全字段的要求，最好是这些字段的数组。

因此我们在 HotelDoc 中添加一个 suggestion 字段，类型为 `List<String>`，然后将 brand、city、business 等信息放到里面。

代码如下：

```java
@Data
@NoArgsConstructor
public class HotelDoc {
    private Long id;
    private String name;
    private String address;
    private Integer price;
    private Integer score;
    private String brand;
    private String city;
    private String starName;
    private String business;
    private String location;
    private String pic;
    private Object distance;
    private Boolean isAD;
    private List<String> suggestion;

    public HotelDoc(Hotel hotel) {
        this.id = hotel.getId();
        this.name = hotel.getName();
        this.address = hotel.getAddress();
        this.price = hotel.getPrice();
        this.score = hotel.getScore();
        this.brand = hotel.getBrand();
        this.city = hotel.getCity();
        this.starName = hotel.getStarName();
        this.business = hotel.getBusiness();
        this.location = hotel.getLatitude() + ", " + hotel.getLongitude();
        this.pic = hotel.getPic();
        // 组装suggestion
        if (this.business.contains("/")) {
            // business有多个值，需要切割
            String[] arr = this.business.split("/");
            // 添加元素
            this.suggestion = new ArrayList<>();
            this.suggestion.add(this.brand);
            Collections.addAll(this.suggestion, arr);
        } else {
            this.suggestion = Arrays.asList(this.brand, this.business);
        }
    }
}
```

#### 重新导入并测试

重新执行之前编写的导入数据功能 `testBulkRequest()`，并搜索测试

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  }
}
```

可以看到新的酒店数据中包含了 suggestion，接下来测试自动补全功能

```json
GET /hotel/_search
{
  "suggest": {
    "suggestions": {
      "text": "sd",
      "completion": {
        "field": "suggestion",
        "skip_duplicates": true, 
        "size": 10 
      }
    }
  }
}
```

#### 自动补全查询的 Java API

```java
// 1.准备请求
SearchRequest request = new SearchRequest("hotel");
// 2.请求参数
request.source().suggest(new SuggestBuilder().addSuggestion(
    "mySuggestion",
    SuggestBuilders
    .completionSuggestion("title")
    .prefix("h")
    .skipDuplicates(true)
    .size(10)
));
// 3.发送请求
client.search(request, RequestOptions.DEFAULT);
```

而自动补全的结果也比较特殊，解析的代码如下：

```java
// 4.处理结果
Suggest suggest = response.getSuggest();
// 4.1.根据名称获取补全结果
CompletionSuggestion suggestion = suggest.getSuggestion("mySuggestion");
// 4.2.获取options并遍历
for (CompletionSuggestion.Entry.Option option : suggestion.getOptions()) {
    // 4.3.获取一个option中的text，也就是补全的词条
    String text = option.getText().string();
    System.out.println(text);
}
```

#### 实现搜索框自动补全

在 `cn.itcast.hotel.web` 包下的 `HotelController` 中添加新接口，接收新的请求：

```java
@GetMapping("suggestion")
public List<String> getSuggestions(@RequestParam("key") String prefix) {
    return hotelService.getSuggestions(prefix);
}
```

在 `cn.itcast.hotel.service` 包下的 `IhotelService` 中添加方法：

```java
List<String> getSuggestions(String prefix);
```

在 `cn.itcast.hotel.service.impl.HotelService` 中实现该方法：

```java
@Override
public List<String> getSuggestions(String prefix) {
    try {
        // 1.准备请求
        SearchRequest request = new SearchRequest("hotel");
        // 2.请求参数
        request.source().suggest(new SuggestBuilder().addSuggestion(
            "suggestions",
            SuggestBuilders
            .completionSuggestion("suggestion")
            .prefix(prefix)
            .skipDuplicates(true)
            .size(10)
        ));
        // 3.发送请求
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 4.处理结果
        Suggest suggest = response.getSuggest();
        // 4.1.根据名称获取补全结果
        CompletionSuggestion suggestion = suggest.getSuggestion("suggestions");
        // 4.2.获取options并遍历
        ArrayList<String> result = new ArrayList<>();
        for (CompletionSuggestion.Entry.Option option : suggestion.getOptions()) {
            // 4.3.获取一个option中的text，也就是补全的词条
            String text = option.getText().string();
            result.add(text);
        }
        return result;
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}
```

## 数据同步

ElasticSearch 中的酒店数据来自于 MySQL 数据库，因此 MySQL 数据发生改变时，ElasticSearch 也必须跟着改变，这个就是 ElasticSearch 与 MySQL 之间的数据同步。

### 思路分析

常见的数据同步方案有三种：

- 同步调用
- 异步通知
- 监听 binlog

#### 同步调用

![同步调用](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/同步调用.i2rj0d3cpwg.svg)

基本步骤如下：

- hotel-demo 对外提供接口，用来修改 ElasticSearch 中的数据
- 酒店管理服务在完成数据库操作后，直接调用 hotel-demo 提供的接口

#### 异步通知

![异步通知](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/异步通知.1p2ptlc5nrs0.svg)

流程如下：

- hotel-admin 对 MySQL 数据库数据完成增、删、改后，发送 MQ 消息
- hotel-demo 监听 MQ，接收到消息后完成 ElasticSearch 数据修改

#### 监听 binlog

![监听binlog](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/监听binlog.1n6hcbad4pb4.svg)

流程如下：

- 给 MySQL 开启 binlog 功能
- MySQL 完成增、删、改操作都会记录在 binlog 中
- hotel-demo 基于 canal 监听 binlog 变化，实时更新 ElasticSearch 中的内容

#### 不同数据同步方案优缺点

方式一：同步调用

- 优点：实现简单，粗暴
- 缺点：业务耦合度高

方式二：异步通知

- 优点：低耦合，实现难度一般
- 缺点：依赖 MQ 的可靠性

方式三：监听 binlog

- 优点：完全解除服务间耦合
- 缺点：开启 binlog 增加数据库负担、实现复杂度高

### 实现数据同步

#### 基于 MQ 的实现思路

利用提供的 hotel-admin 项目作为酒店管理的微服务。当酒店数据发生增、删、改时，要求对 ElasticSearch 中数据也要完成相同操作。

步骤：

- 导入 hotel-admin 项目，启动并测试酒店数据的 CRUD

- 声明 exchange、queue、RoutingKey

- 在 hotel-admin 中的增、删、改业务中完成消息发送

- 在 hotel-demo 中完成消息监听，并更新 ElasticSearch 中数据

- 启动并测试数据同步功能

### 导入 demo

代码链接：[GitHub](https://github.com/Lanqilu/HaloElasticSearch/commit/b9d7c724b44d6ea8e307ac5d54778bba635bd314)

运行后，访问 http://localhost:8099

### 声明交换机、队列

MQ 结构如图：

![MQ结构](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/MQ结构.lv1ecla6gvk.svg)

#### 引入依赖并修改配置文件 

在 hotel-admin、hotel-demo 中引入 rabbitmq 的依赖：

```xml
<!--amqp-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

```yaml
spring:
  rabbitmq:
    host: rabbitmq
    port: 5672
    username: halo
    password: halo
    virtual-host: /
```

#### 声明交换机、队列

在 hotel-admin 和 hotel-demo 中的 `cn.itcast.hotel.constatnts` 包下新建一个类 `MqConstants`：

```java
public class MqConstants {
    /**
     * 交换机
     */
    public final static String HOTEL_EXCHANGE = "hotel.topic";
    /**
     * 监听新增和修改的队列
     */
    public final static String HOTEL_INSERT_QUEUE = "hotel.insert.queue";
    /**
     * 监听删除的队列
     */
    public final static String HOTEL_DELETE_QUEUE = "hotel.delete.queue";
    /**
     * 新增或修改的RoutingKey
     */
    public final static String HOTEL_INSERT_KEY = "hotel.insert";
    /**
     * 删除的RoutingKey
     */
    public final static String HOTEL_DELETE_KEY = "hotel.delete";
}
```

在 hotel-demo 中，定义配置类，声明队列、交换机：

```java
@Configuration
public class MqConfig {
    @Bean
    public TopicExchange topicExchange(){
        return new TopicExchange(MqConstants.HOTEL_EXCHANGE, true, false);
    }

    @Bean
    public Queue insertQueue(){
        return new Queue(MqConstants.HOTEL_INSERT_QUEUE, true);
    }

    @Bean
    public Queue deleteQueue(){
        return new Queue(MqConstants.HOTEL_DELETE_QUEUE, true);
    }

    @Bean
    public Binding insertQueueBinding(){
        return BindingBuilder.bind(insertQueue()).to(topicExchange()).with(MqConstants.HOTEL_INSERT_KEY);
    }

    @Bean
    public Binding deleteQueueBinding(){
        return BindingBuilder.bind(deleteQueue()).to(topicExchange()).with(MqConstants.HOTEL_DELETE_KEY);
    }
}
```

### 发送 MQ 消息

在 hotel-admin 中的增、删、改业务中分别发送 MQ 消息：

```java
@Autowired
private RabbitTemplate rabbitTemplate;

@PostMapping
public void saveHotel(@RequestBody Hotel hotel){
    hotelService.save(hotel);
    rabbitTemplate.convertAndSend(MqConstants.HOTEL_EXCHANGE,MqConstants.HOTEL_INSERT_KEY,hotel.getId());
}

@PutMapping()
public void updateById(@RequestBody Hotel hotel){
    if (hotel.getId() == null) {
        throw new InvalidParameterException("id不能为空");
    }
    hotelService.updateById(hotel);
    rabbitTemplate.convertAndSend(MqConstants.HOTEL_EXCHANGE,MqConstants.HOTEL_INSERT_KEY,hotel.getId());
}

@DeleteMapping("/{id}")
public void deleteById(@PathVariable("id") Long id) {
    hotelService.removeById(id);
    rabbitTemplate.convertAndSend(MqConstants.HOTEL_EXCHANGE,MqConstants.HOTEL_DELETE_KEY,id);
}
```

### 接收 MQ 消息

hotel-demo 接收到 MQ 消息要做的事情包括：

- 新增消息：根据传递的 hotel 的 id 查询 hotel 信息，然后新增一条数据到索引库
- 删除消息：根据传递的 hotel 的 id 删除索引库中的一条数据

首先在 hotel-demo 的 `cn.itcast.hotel.service` 包下的 `IHotelService` 中新增新增、删除业务

```java
void deleteById(Long id);

void insertById(Long id);
```

给 hotel-demo 中的 `cn.itcast.hotel.service.impl` 包下的 HotelService 中实现业务：

```java
@Override
public void deleteById(Long id) {
    try {
        // 1.准备Request
        DeleteRequest request = new DeleteRequest("hotel", id.toString());
        // 2.发送请求
        client.delete(request, RequestOptions.DEFAULT);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}

@Override
public void insertById(Long id) {
    try {
        // 0.根据id查询酒店数据
        Hotel hotel = getById(id);
        // 转换为文档类型
        HotelDoc hotelDoc = new HotelDoc(hotel);

        // 1.准备Request对象
        IndexRequest request = new IndexRequest("hotel").id(hotel.getId().toString());
        // 2.准备Json文档
        request.source(JSON.toJSONString(hotelDoc), XContentType.JSON);
        // 3.发送请求
        client.index(request, RequestOptions.DEFAULT);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}
```

编写监听器，在 hotel-demo 中的 `cn.itcast.hotel.mq` 包新增一个类：

```java
@Component
public class HotelListener {

    @Autowired
    private IHotelService hotelService;

    /**
     * 监听酒店新增或修改的业务
     * @param id 酒店id
     */
    @RabbitListener(queues = MqConstants.HOTEL_INSERT_QUEUE)
    public void listenHotelInsertOrUpdate(Long id){
        hotelService.insertById(id);
    }

    /**
     * 监听酒店删除的业务
     * @param id 酒店id
     */
    @RabbitListener(queues = MqConstants.HOTEL_DELETE_QUEUE)
    public void listenHotelDelete(Long id){
        hotelService.deleteById(id);
    }
}
```

## ElasticSearch 集群

单机的 ElasticSearch 做数据存储，必然面临两个问题：海量数据存储问题、单点故障问题。

- 海量数据存储问题：将索引库从逻辑上拆分为 N 个分片（shard），存储到多个节点
- 单点故障问题：将分片数据在不同节点备份（replica）

ES 集群相关概念:

* 集群（cluster）：一组拥有共同的 cluster name 的 节点。
* 节点（node)   ：集群中的一个 Elasticearch 实例
* 分片（shard）：索引可以被拆分为不同的部分进行存储，称为分片。在集群环境下，一个索引的不同分片可以拆分到不同的节点中

解决问题：数据量太大，单点存储量有限的问题。

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.4pwzs1lq4540.png)

此处，我们把数据分成 3 片：shard0、shard1、shard2

* 主分片（Primary shard）：相对于副本分片的定义。

* 副本分片（Replica shard）每个主分片可以有一个或者多个副本，数据和主分片一样。

数据备份可以保证高可用，但是每个分片备份一份，所需要的节点数量就会翻一倍，成本实在是太高了！

为了在高可用和成本间寻求平衡，我们可以这样做：

- 首先对数据分片，存储到不同节点
- 然后对每个分片进行备份，放到对方节点，完成互相备份

这样可以大大减少所需要的服务节点数量，如图，我们以 3 分片，每个分片备份一份为例：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.3jlk46u66q60.png)

现在，每个分片都有 1 个备份，存储在 3 个节点：

- node0：保存了分片 0 和 1
- node1：保存了分片 0 和 2
- node2：保存了分片 1 和 2

### 部署 ElasticSearch 集群

我们会在单机上利用 docker 容器运行多个 ElasticSearch 实例来模拟 ElasticSearch 集群。不过生产环境推荐大家每一台服务节点仅部署一个 ElasticSearch 的实例。

部署 ElasticSearch 集群可以直接使用 docker-compose 来完成，但这要求你的 Linux 虚拟机至少有 4G 的内存空间

#### 创建 ElasticSearch 集群

首先编写一个 docker-compose 文件，内容如下：

```sh
version: '2.2'
services:
  es01:
    image: elasticsearch:7.12.1
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: elasticsearch:7.12.1
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data02:/usr/share/elasticsearch/data
    ports:
      - 9201:9200
    networks:
      - elastic
  es03:
    image: elasticsearch:7.12.1
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic
    ports:
      - 9202:9200
volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```

ElasticSearch  运行需要修改一些 Linux 系统权限，修改 `/etc/sysctl.conf` 文件

```sh
vi /etc/sysctl.conf
```

添加下面的内容：

```sh
vm.max_map_count=262144
```

然后执行命令，让配置生效：

```sh
sysctl -p
```

通过 docker-compose 启动集群：

```sh
docker-compose up -d
```

#### 集群状态监控

kibana 可以监控 ElasticSearch  集群，不过新版本需要依赖 ElasticSearch  的 x-pack 功能，配置比较复杂。

这里推荐使用 cerebro 来监控 ElasticSearch  集群状态，官方网址：https://github.com/lmenezes/cerebro

双击其中的 cerebro.bat 文件即可启动服务。访问 http://localhost:9000 即可进入管理界面：

输入你的 ElasticSearch 的任意节点的地址和端口，点击 connect 即可

#### 创建索引库

利用 kibana 的 DevTools 创建索引库，在 DevTools 中输入指令：

```json
PUT /test
{
  "settings": {
    "number_of_shards": 3, // 分片数量
    "number_of_replicas": 1 // 副本数量
  },
  "mappings": {
    "properties": {
      // mapping映射定义 ...
    }
  }
}
```

或利用 cerebro 创建索引库

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.29xen9tqfmvw.png)

查看分片效果，回到首页，即可查看索引库分片效果：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.2jn8zaht2280.png)

### 集群脑裂问题

#### 集群职责划分

ElasticSearch 中集群节点有不同的职责划分：

| 节点类型         | 配置参数                                       | 默认值 | 节点职责                                                     |
| ---------------- | ---------------------------------------------- | ------ | ------------------------------------------------------------ |
| master  eligible | node.master                                    | true   | 备选主节点：主节点可以管理和记录集群状态、决定分片在哪个节点、处理创建和删除索引库的请求 |
| data             | node.data                                      | true   | 数据节点：存储数据、搜索、聚合、CRUD                         |
| ingest           | node.ingest                                    | true   | 数据存储之前的预处理                                         |
| coordinating     | 上面 3 个参数都为 false 则为 coordinating 节点 | 无     | 路由请求到其它节点  合并其它节点处理的结果，返回给用户       |

默认情况下，集群中的任何一个节点都同时具备上述四种角色。

但是真实的集群一定要将集群职责分离：

- master 节点：对 CPU 要求高，但是内存要求低
- data 节点：对 CPU 和内存要求都高
- coordinating 节点：对网络带宽、CPU 要求高

职责分离可以让我们根据不同节点的需求分配不同的硬件去部署。而且避免业务之间的互相干扰。

一个典型的 ElasticSearch 集群职责划分如图：

![ES集群](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/ES集群.2rrl7g78cxs0.svg)

#### 脑裂问题

脑裂是因为集群中的节点失联导致的。

例如一个集群中，主节点与其它节点失联，

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.6ry0qp2sxiw0.png)

此时 node2 和 node3 认为 node1 宕机，就会重新选主：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.5qon8j1rbak0.png)

当 node3 当选后，集群继续对外提供服务，node2 和 node3 自成集群，node1 自成集群，两个集群数据不同步，出现数据差异。

当网络恢复后，因为集群中有两个 master 节点，集群状态的不一致，出现脑裂的情况：

![image](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/image.wmlcqz00rls.png)

解决脑裂的方案是，要求选票超过 ( eligible节点数量 + 1 ）/ 2 才能当选为主，因此 eligible 节点数量最好是奇数。对应配置项是 discovery.zen.minimum_master_nodes，在 ElasticSearch 7.0 以后，已经成为默认配置，因此一般不会发生脑裂问题

例如：3 个节点形成的集群，选票必须超过 （3 + 1） / 2 ，也就是 2 票。node3 得到 node2 和 node3 的选票，当选为主。node1 只有自己 1 票，没有当选。集群中依然只有 1 个主节点，没有出现脑裂。

### 集群分布式存储

当新增文档时，应该保存到不同分片，保证数据均衡，那么 coordinating node 如何确定数据该存储到哪个分片呢？

#### 分片存储测试

在一个节点中加入数据，后可以通过 explain 命令查询

```json
POST /test/_search
{
  "explain": true,
  "query": {
    "match_all": {}
  }
}
```

发现数据在不同的节点中，形成分片存储

#### 分片存储原理

ElasticSearch 会通过 hash 算法来计算文档应该存储到哪个分片：`shard = hash(_routing) % number_of_shards`

说明：

- `_routing` 默认是文档的id
- 算法与分片数量有关，因此索引库一旦创建，分片数量不能修改！

新增文档的流程如下：

![ES集群](https://cdn.jsdelivr.net/gh/halo-blog/cdn-blog-img-e@master/ES集群.akvhs79fwq0.svg)

解读：

- 新增一个 id=1 的文档
- 对 id 做 hash 运算，假如得到的是 2，则应该存储到 P-2
- P-2 的主分片在 node3 节点，将数据路由到 node3
- 保存文档
- 同步给 P-2 的副本 R-2，在 node2 节点
- 返回结果给 coordinating-node 节点

#### 集群分布式查询

ElasticSearch 的查询分成两个阶段：

- scatter phase：分散阶段，coordinating node 会把请求分发到每一个分片

- gather phase：聚集阶段，coordinating node 汇总 data node 的搜索结果，并处理为最终结果集返回给用户

#### 集群故障转移

集群的 master 节点会监控集群中的节点状态，如果发现有节点宕机，会立即将宕机节点的分片数据迁移到其它节点，确保数据安全，这个叫做故障转移。

+ 假如，node1 发生了故障
+ 宕机后的第一件事，需要重新选主，例如选中了 node2，
+ node2 成为主节点后，会检测集群监控状态，发现：shard-1、shard-0 没有副本节点。因此需要将 node1 上的数据迁移到 node2、node3
+ 但 node1 恢复，此时 node1 不在是主节点，但数据会重新平衡

















