---
layout: post
title:  "Document APIs in ElasticSearch"
date: 2023-12-07 18:00:00 +0900
abstract: "ElasticSearch"
tags: [ElasticSearch]
image:
toc: true
categories: Tech
last_modified_at: 
published: true
---

* [ElasticSearch 1/3]({% post_url 2023-08-23-ElasticSearch %}) <span class="series">SERIES 1/3</span>
* **ElasticSearch 2/3 ✓**  <span class="series">SERIES 2/3</span>
* [ElasticSearch 3/3]({% post_url 2023-12-11-ElasticSearch3 %}) <span class="series">SERIES 3/3</span>

## 개요 

ElasticSearch에 Document 생성, 수정, 삭제하는 방법을 알아보겠습니다. 
<br>  
예시는 엘라스틱서치 8.9.1 를 기준으로 작성하였습니다.  
회사 운영중인 엘라스틱 버전이 2.3.0 인지라 종종 2.3.0 버전과 차이가 있는 내용도 추가하였습니다.  


## Mappgin 

인덱싱을 위해서 매핑정보를 선언하겠습니다.  
자동매핑인경우 ElasticSearch 가 입력되는 데이터의 특성에 따라 자동으로 지정해줍니다.  
따로 선언해주는 경우는 keyword 타입등 명확하게 구별되는 특성을 가진 필드를 선언하기 위함입니다.  

```
curl -X PUT "localhost:9200/author" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "gender": { "type": "keyword" }
    }
  }
}
'
```
이름 필드는 text, 셩별은 keyword 타입으로 지정하였습니다.   


## Insert 

데이터를 생성합니다.  
```
curl -XPUT http://localhost:9200/author/_doc/1 -H 'Content-Type: application/json' -d '
{
    "name" : "name-1",
    "gender" : "male"
}'
```

여성, 남성 각각 2건 생성하였습니다.  
```
curl -XPUT http://localhost:9200/author/_doc/2 -H 'Content-Type: application/json' -d '
{
    "name" : "name-2",
    "gender" : "female"
}'
```

## Select 

생성한 데이터를 검색해보겠습니다.  
"found":true 로 나오면 document 가 있고 데이터를 출력합니다.  

```
$ curl http://localhost:9200/author/_doc/2
{"_index":"author","_id":"2","_version":3,"_seq_no":3,"_primary_term":1,"found":true,
    "_source":{
        "name" : "name-2",
        "gender" : "female"
    }
}
```

엘라스틱서치 2.3 인경우 <인덱스>/<타입> 형태로 구성되어 있습니다.  
이런경우 document 정보를 가져올때 <타입> 도 명시해줘야 검색됩니다.  

biz_user/user 로 구성된 경우입니다.  
user 타입을 제외하면 찾지 못합니다.  
```
$ curl localhost:9200/biz_user/_doc/user1
{"_index":"biz_user","_type":"_doc","_id":"user1","found":false}
```

biz_user/user 로 질의하니 데이터가 조회됩니다.  
```
$ curl localhost:9200/biz_user/user/user1
{"_index":"biz_user","_type":"user","_id":"user1","_version":1,"found":true,
    "_source":{"userId":"cbcxxxb0-xxxx-xxxx-9c70-00xxxxac1497","regDate":1479434836980}
} 
```



## Update 

문서 업데이트를 해보겠습니다. 

엘라스틱서치 2.3  
```
POST <인덱스>/<도큐먼트 타입>/<도큐먼트 id>/_update 
```

엘라스틱서치 5.x 이후 버전   
```
POST <인덱스>/<도큐먼트 타입>/<도큐먼트 id>/_update 
```

기본 사용법은 아래와 같습니다. 
```
curl -X POST "localhost:9200/your_index/your_type/your_document_id/_update" -H 'Content-Type: application/json' -d '
{
  "doc": {
    "new_field": "new_value"
  }
}
'
```

id 1인 document 를 update 해보겠습니다. 
```
curl http://localhost:9200/author/_doc/1
{"_index":"author","_id":"1","_version":1,"_seq_no":0,"_primary_term":1,"found":true,"_source":
{
    "name" : "name-1",
    "gender" : "male"
}}
```

나이를 추가하려고 아래와 같이 작성했습니다. 
```
curl -X POST 'localhost:9200/author/_doc/1' -H 'Content-Type: application/json' -d '
{
  "doc": {
    "age": "20"
  }
}'
```

추가가 잘되었는지 확인하기위해 재 질의합니다. 
```
$ curl http://localhost:9200/author/_doc/1
{"_index":"author","_id":"1","_version":2,"_seq_no":4,"_primary_term":1,"found":true,"_source":
{
  "doc": {
    "age": "20"
  }
}}
```

헉!! name, gender 필드는 사라지고 age 필드가 있군요!   
그렇다면 age 필드 값을 수정해보겠습니다.   
```
curl -X POST 'localhost:9200/author/_doc/1' -H 'Content-Type: application/json' -d '
{
  "doc": {
    "age": "21"
  }
}'
{"_index":"author","_id":"1","_version":3,"result":"updated","_shards":{"total":1,"successful":1,"failed":0},"_seq_no":5,"_primary_term":1}
```

그렇습니다.
update 는 기존 필드에 새로운 필드를 추가해주는게 아니라.  
문서 전체 내용을 변경하는 명령이였습니다.  

기존 필드에 신규 필드를 추가하려면 Elasticsearch 에서 제공하는 script 기능을 이용하거나 (설정 확인필요),   
개발 스크립트를 작성하여 기존 필드 데이터를 가져와서 더한 후 update 하도록 작성하여 실행해줘야 합니다. 
관련 내용은 [ElasticSearch 3/3]({% post_url 2023-12-11-ElasticSearch3 %}) 에서 이어서 작성하였습니다.  

## Delete Document


Document 를 삭제해보겠습니다. 
"successful":1 로 보아 1건이 성공적으로 삭제되었군요.  
```
$ curl -XDELETE localhost:9200/author/_doc/2
{"_index":"author","_id":"2","_version":4,"result":"deleted","_shards":{"total":1,"successful":1,"failed":0},"_seq_no":6,"_primary_term":1}
```



## Select Index

index author 에 2건 생성 후 1건은 수정, 1건은 삭제하였습니다.  

현재 author 인덱스에 남아있는 Doc 은 hits.total.value = 1 로 1건 나오네요.  
```
        "_source": {
          "doc": {
            "age": "21"
          }
        }
```
1건의 문서는 _source 항목에서 확인 할수 있습니다. 

전체 질의, 응답 화면 
```
$ curl http://localhost:9200/author/_search\?q\=\*
{
  "took": 12,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "author",
        "_id": "1",
        "_score": 1,
        "_source": {
          "doc": {
            "age": "21"
          }
        }
      }
    ]
  }
}
```








## Delete Index 

삭제 테스트용 Index 를 생성 후 조회합니다. 
```
$ curl http://localhost:9200/books\?pretty\=true
{
  "books" : {
    "aliases" : { },
    "mappings" : {
      "properties" : {
        "author" : {
          "type" : "text"
        },
        "pages" : {
          "type" : "long"
        },
        "title" : {
          "type" : "text"
        },
        "type" : {
          "type" : "keyword"
        }
      }
    },
    "settings" : {
      "index" : {
        "routing" : {
          "allocation" : {
            "include" : {
              "_tier_preference" : "data_content"
            }
          }
        },
        "number_of_shards" : "1",
        "provided_name" : "books",
        "creation_date" : "1701933028243",
        "number_of_replicas" : "0",
        "uuid" : "tpktJo0qQQiWty4APqEzNg",
        "version" : {
          "created" : "8090199"
        }
      }
    }
  }
}
```

인덱스 삭제합니다. 
```
$ curl -XDELETE localhost:9200/books
{"acknowledged":true} 
```


## Search 


검색 API 호출시 default 10건입니다. 


ElasticSearch 2.3 기준으로 타입이 있는 경우 조회하는 경우입니다.  

URL 호출 시 인덱스/타입,  aa/bb 형태로 질의하는 경우입니다. 

```
$ curl -X GET 'http://localhost:9200/aa/bb/_search?q=*' -H 'Content-Type: application/json'
{
  "took": 3,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 37496,
    "max_score": 1,
    "hits": [
      {
        "_index": "aa",
        "_type": "bb",
        "_id": "001",
        "_score": 1,
        "_source": {
          "Id": "001",
          "date": "1702287321276"
        }
      }
    ]
  }
}
```




* took : 검색에 소요된 시간, 밀리 초 (1/1000) , ex) "took":3 // 0.003초 
* timed_out : 검색이 수행되는 동안 기다리는 제한 시간, 밀리 초 (ex time_out=300 이면 검색을 시작하고 3초후에 검색을 강제종료)

<br>  
Query DSL 를 사용하여 AA 인덱스, camera 타입, encodingType 필드가 없는 doc 리스트 출력하는 경우입니다. 
```
$  curl -XGET 'http://localhost:9200/AA/camera/_search' -H 'Content-Type: application/json' -d '
{
"from" : 0,
"size" : 10,
"query" : {
    "bool" : {
    "must" : [ {
        "range" : {
        "sellStatusDate" : {
            "from" : 0,
            "to" : 1701682000732,
            "include_lower" : true,
            "include_upper" : true
        }
        }
    }, {
        "bool": {
            "must_not": {
                "exists": {
                "field": "encodingType"
                }
            }
        }
    } ]
    }
},
"sort" : [ {
    "sellStatusDate" : {
    "order" : "desc",
    "missing" : "_last"
    }
}, {
    "serialNo" : {
    "order" : "desc"
    }
} ]
}'

{
    "took":12,
    "timed_out":false,
    "_shards":{
        "total":5,
        "successful":5,
        "failed":0
    },
    "hits": {
        "total":100,
        "max_score":null,
        "hits":[
            {
                "_index":
                "toastcam_v4",
                "_type":"camera",
                "_id":"BBBB",
                "_score":null,
                "_source":{
                    "cameraId":"AAAAAA",
                    "serialNo":"BBBB",
                    "serialNoReal":"BBBB",
                    "vendorCode":"WILLOW",
...
```
