---
layout: post
title:  "Elasticsearch同义词配置"
---

# Elasticsearch同义词配置

## 问题背景

​     同义词搜索功能在很多业务场景中都会遇到，如何基于Elasticsearch实现同义词搜索功能?


<figure>
<a><img src="{{site.url}}/images/1.jpg"></a>
</figure>

## 解决方案

1、首先准备一个同义词词库，每行一个同义词词组，假设文件名是syno.dic:

```java
西红柿,番茄,tomato  

马铃薯,土豆  
machine learning, 机器学习  => 机器学习,  machine_learning  
```

把同义词词典放到词库位置(config/analysis)目录下。

2、自定义新的分词器，加入同义词过滤器

```json
DELETE  syno

PUT syno
{
  "settings": {
    "analysis": {
      "filter": {
        "my_synonym_filter": {
          "type": "synonym",
          "expand": true,
          "ignore_case": true,
          "synonyms_path": "analysis/syno.dic"
        }
      },
      "analyzer": {
        "ik_syno": {
          "type": "custom",
          "tokenizer": "ik_smart",
          "filter": [
            "my_synonym_filter"
          ]
        },
        "ik_syno_max": {
          "type": "custom",
          "tokenizer": "ik_max_word",
          "filter": [
            "my_synonym_filter"
          ]
        }
      }
    }
  }
}

```

3、         新建一个索引，索引名为syno，类型为doc，字段title使用自定义分词:  

```json
PUT syno/doc/_mapping
{

    "doc": {
      "properties": {
        "title": {
          "type": "text",
          "analyzer": "ik_syno",
          "search_analyzer": "ik_syno"
        }
      }
    }
}

```

4、命令测试分词效果:  

```json
GET /syno/_analyze
{
  "text":"我爱机器学习",
  "analyzer": "ik_syno"
}
```

5、写入几条测试文档测试知网的同义词搜索效果  

```json
POST syno/doc
{
   "title": "机器学习在电站设备状态分析中的应用"
}

POST syno/doc
{
   "title": "A Machine Learning Algorithm to Predict Severe Sepsis and Septic Shock: Development, Implementation, and Impact on Clinical Practice."
}

POST syno/doc
{
   "title": "小明喜欢吃土豆"
}

POST syno/doc
{
   "title": "小华喜欢吃马铃薯"
}

```



6、测试搜索效果  

```json
GET syno/_search
{
  "query": {
    "match": {
      "title": "机器学习"
    }
  },
  "highlight": {
    "fields": {
      "title": {}
    }
  }
}
```

效果截图如下:

<figure>
<a><img src="{{site.url}}/images/1.jpg"></a>
</figure>

## 总结 

  es同义词搜索的实现首先是准备自定义词典，然后配置同义词filter，再使用同义词filter配置自定义分词器，最后在mapping中使用自定义分词器。

  需要注意的是词典格式问题， 如果是“土豆”和“马铃薯”这种词，词之没有空格，逗号分隔按行配置即可。但是如果是 “machine learning” “机器学习”，单词中存在空格，需要用"_"连接，即把“machine learning”在词典文件中写成“machine_learning”。对于“机器学习”没有分出来一个词的情况，加入自定义词典即可。

##  参考文档
<a href="https://www.elastic.co/guide/cn/elasticsearch/guide/current/synonyms.html">https://www.elastic.co/guide/cn/elasticsearch/guide/current/synonyms.html</a>




