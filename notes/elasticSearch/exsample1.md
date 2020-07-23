**增删改查示例**

##创建索引
```
PUT /test
{
  "settings": {
    "number_of_shards": 1
    , "number_of_replicas": 1
  }
}
```

##更新配置
```
PUT /test/_settings
{
  "number_of_replicas": 0
}
```

#创建索引
```
PUT /test/_doc/1
{
  "name":"dachui",
  "age":20
}
```

##指定id 全量修改
```
PUT /test/_doc/1
{
  "name":"xaiochui"
}
```

##部分修改
```
POST /test/_doc/1/update
{
  "doc":{
    "name":"xiaochui"
  }
}
```

##指定_create防止重复创建,如果已经存在则失败,以下语句执行第二次会报错
```
POST /test/_doc/2/create
{
  "name":"hahhh",
  "age":20
}
```


##不指定id建立索引，es会默认给你生成一个
```
POST /test/_doc/
{
  "name":"hahhh2",
  "age":20
}
```

##删除文档指定id
```
DELETE /test/_doc/1
```


##删除索引
```
DELETE /test
```

#结构化创建索引

##结构化创建索引
```
PUT /test
{
  "settings": {
    "number_of_replicas": 1,
    "number_of_shards": 1
  },
  "mappings": {
    "properties": {
      "name": {
        "type": "text",
        "analyzer": "ik_max_word",
        "search_analyzer": "ik_smart"
      },
      "enname": {
        "type": "text",
        "analyzer": "english"
      },
      "age": {
        "type": "integer"
      }
    }
  }
}
```

##查询所有
```
GET /test/_search
{
  "query": {
    "match_all": {}
  }
}
```

##分页查询 切忌查太多页，分页是在内存中实现，内存消耗巨大
```
GET /test/_search
{
  "query": {
    "match_all": {}
  },
  "from": 0,
  "size": 20
}
```

##条件查询
```
GET /test/_search
{
  "query": {
    "match": {
      "name": "dachui"
    }
  }
}
```


##排序,聚合
```
GET /test/_search
{
  "query": {
    "match": {
      "name": "dachui"
    }
  },
  "sort": [
    {
      "id": {
        "order": "desc"
      }
    }
  ],
  "aggs": {
    "group_by_age": {
      "terms": {
        "field": "age",
        "size": 10
      }
    }
  }
}
```


##英语分词器 根据词原型分词,中文不指定默认用stander，直接每个字分词。IK分词器 可以选用smart和max_word
```
GET /test/_analyze
{
  "field": "name",
  "text": ["i am dachui，living in beijing"]
}

GET /test/_search
{
  "query":{
    "match": {"name":"live"}
  }
}
```


