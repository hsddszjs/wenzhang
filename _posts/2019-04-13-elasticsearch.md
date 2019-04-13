---
layout: post
title: "elasticsearch概要"
description: ""
comments: true
keywords: ""
---

## 倒排索引

![倒排索引1](/assets/images/倒排索引1.png)
![倒排索引2](/assets/images/倒排索引2.png)

## 分词器

```
"我能吞下玻璃而不伤身体" => ["能","吞下","玻璃","而","不","伤","身体"] 
```

如果需要支持中文的全文检索，需要中文分词器，将句子拆分成单词，并去掉stopword(出现频率极高的词如"我")

## 使用文档

https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html

可以搭配kibana