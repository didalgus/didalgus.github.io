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

* [ElasticSearch 1/4]({% post_url 2023-08-23-ElasticSearch %}) <span class="series">SERIES 1/4</span>
* **ElasticSearch 2/4 ✓**  <span class="series">SERIES 2/4</span>
* [ElasticSearch 3/4]({% post_url 2023-12-11-ElasticSearch3 %}) <span class="series">SERIES 3/4</span>
* [ElasticSearch 4/4]({% post_url 2024-03-14-ElasticSearch4 %}) <span class="series">SERIES 4/4</span>


## 개요 

ElasticSearch에 Document 생성, 수정, 삭제하는 방법을 알아보겠습니다. 
<br>  
예시는 엘라스틱서치 8.9.1 를 기준으로 작성하였습니다.  
회사 운영중인 엘라스틱 버전이 2.3.0 인지라 2.3.0 버전과 차이가 있는경우 내용 추가하였습니다.  

## Index, Type 

엘라스틱서치 2.3.0 와 8.9.1 버전을 살펴보면서 가장 큰 차이점은 바로 Type 개념이 없어졌다는 점입니다.   
정확히는 엘라스틱서치 6.x 버전부터 타입(Type)이 사라지고 하나의 인덱스 내에 여러유형의 도큐먼트를 저장하는 기능이 제거되었습니다. 
Elasticsearch 7.x 버전 이후부터는 하나의 인덱스 내에 단일 타입의 도큐먼트만을 가질 수 있습니다.
<br>

Type 사용하는 버전의 데이터 구조
![](/assets/article_images/2023-12-07-ElasticSearch2/ElasticSearch2_3.png)


## 데이터 구조 비교 

보통 데이터베이스와 엘라스틱서치의 데이터 구조는 아래와 같이 비교되고 있습니다.  

{:.table.table-key-value-60}
| 관계형 데이터베이스 | 엘라스틱서치 |
|---|---|
| 데이터베이스(Database) | 인덱스(Index) |
| 스키마(Schema) | 매핑(Mapping) |
| 테이블(Table) | 타입(Type) |
| 열(Row) | 도큐먼트(Document) |
| 행(Column) | 필드(Field) |


## Mapping

매핑(Mapping)은 데이터의 저장 형태와 검색 엔진에서 해당 데이터에 어떻게 접근하고 처리하는 지에 대한 명세입니다.   
도큐먼트에 입력 될 데이터의 매핑 형태를 정의 할 때는 Properties 필드에 필드명과 타입등의 옵션을 입력해서 설정합니다.  
매핑 설정시 메소드는 `PUT`을 사용합니다.  

엘라스틱 서치는 NoSQL 와 같이 스키마 프리(Schema Free) 를 지원합니다.  
자동매핑이고, 매핑정보가 없는 경우 ElasticSearch가 입력되는 데이터의 특성에 따라 자동으로 지정해줍니다.  
매핑 정보가 있어도 Json 문서 형식으로 입력하면 매핑정보가 있는경우 존재하는 필드에 맞춰서, 매핑정보가 없는 경우 엘라스틱 서치가 자동으로 지정해줍니다. 
<br>   

인덱싱을 위해서 매핑정보를 선언하겠습니다.  
따로 선언해주는 경우는 keyword 타입등 명확하게 구별되는 특성을 가진 필드를 선언하기 위함입니다.  

```bash
$ curl -X PUT "localhost:9200/author" -H 'Content-Type: application/json' -d'
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
이름 필드는 text, 성별 필드는 keyword 타입으로 지정하였습니다.   
<br>

매핑 정보를 확인해보겠습니다. 

명령 형식은 아래와 같습니다.   
타입(Type)이 있는 경우에는 `index` 하위에 있는 타입 정보 전부 보여줍니다.  
```
$ <호스트>/<인덱스>/_mapping
```

또는 `type`이 있는 버전에서 `type` 상세 정보를 보고자 할때는 아래와 같이 사용합니다. 
```
$ <호스트>/<인덱스>/<타입>/_mapping
```

인덱스(Index) 하위에 3개의 타입(type) 있는 경우입니다. 

```bash
$ curl -X GET locahost:9200/car
{
  "car_v4": {
    "aliases": {
      "car": {
        
      }
    },
    "mappings": {
      "engine": {
        "properties": {
          "hybrid": {
            "type": "keyword"
          }
        }
      },
      "wheel": {
        "properties": {
          "tireType": {
            "type": "String",
          },
          "wheelDrive": {
            "type": "integer"
          }
        }
      },
      "baseInfo": {
        "properties": {
          "productionDate": {
            "type": "date",
            "format": "strict_date_optional_time||epoch_millis"
          }
        }
      }
    },
    "settings": {
      "index": {
        "number_of_shards": "5",
        "max_result_window": "500000",
        "creation_date": "1616980797015",
        "analysis": {
          "analyzer": {
            "korean_index": {
              "type": "custom",
              "tokenizer": "seunjeon_default_tokenizer"
            }
          },
          "tokenizer": {
            "seunjeon_default_tokenizer": {
              "type": "seunjeon_tokenizer"
            }
          }
        },
        "number_of_replicas": "1",
        "uuid": "L0xQAAABBBEEDDWWGHHC877",
        "version": {
          "created": "2030099"
        }
      }
    },
    "warmers": {
      
    }
  }
}
```

## CURD 

데이터 처리를 위한 명령어로는 GET, PUT, POST, DELETE 가 있습니다.    
아래 표를 보시면 이해가 더 쉽습니다.  

{:.table.table-key-value-60}
| HTTP Method | CRUD | SQL |
|---|---|---|
| GET | Read | Select |
| PUT | Update | Update |
| POST | Create | Insert |
| DELETE | Delete | Delete |



## 실습 환경 

* MacBook Pro 16 (칩 Apple M1 Pro, 메모리 16GB )
* OS Version : Sonoma 14.3.1  
* ElasticSearch Version : [elasticsearch-8.9.1]({% post_url 2023-12-11-ElasticSearch3 %})


엘라스틱 서치 8.9 기준으로 설명합니다.    
운영버전 2.3 과 상이한 경우 부연 설명 추가하였습니다. 

## Insert 

엘라스틱 서치에 데이터를 생성해 보겠습니다.   
POST, PUT 메서드로 같은인덱스, 타입(~ 6.x), 아이디 값을 가진 도큐먼트를 입력하면 기존의 데이터는 삭제, 새로 입력한 데이이터가 생성됩니다. (주의!!)  

```bash
$ curl -XPOST http://localhost:9200/author/_doc/1 -H 'Content-Type: application/json' -d '
{
    "name" : "name-1",
    "gender" : "male"
}'
```

REST API Tool 을 사용해보겠습니다.  
Postman 등 본인에게 익숙할 툴로 이용하세요.   
저는 Visual Code 의 플러그인 `Thunder Client` 를 사용하고 있습니다. 

![](/assets/article_images/2023-12-07-ElasticSearch2/ElasticSearch2_2.png)

CLI 명령에서는 `Content-Type: application/json` 선언한 부분이 Tool 에서는 자동설정되어 있어 편리합니다.  
![](/assets/article_images/2023-12-07-ElasticSearch2/ElasticSearch2_1.png)

도큐먼트 아이디 1,2로 설정하여 여성, 남성 각각 2건 생성하였습니다.  
아이디 없이도 생성 가능합니다.   
아이디 없이 생성하는 경우 엘라스틱 서치가 자동으로 id 값을 부여합니다.  

<br>
POST, PUT 메서드로 처음 생성했을때와 수정하는 경우의 응답값을 살펴보면    
Response 결과값에 "created":true 로 나오고,   
수정하는경우 "created":false 로 나오고 version 필드의 값은 2가 되는것을 확인 할 수 있습니다. 
<br>  

POST 메서드 응답값    

{:.table.table-key-value-60}
| type | Response |
|---|---|
| 최초 생성시  | ```"_version":1, "created":true``` | 
| 변경시 | ```"_version":2, "created":false``` | 




## Select 

생성한 데이터를 검색해보겠습니다.  
"found":true 로 나오면 document 가 있고 데이터를 출력합니다.  

```bash
$ curl http://localhost:9200/author/_doc/2
{"_index":"author","_id":"2","_version":3,"_seq_no":3,"_primary_term":1,"found":true,
    "_source":{
        "name" : "name-2",
        "gender" : "female"
    }
}
```

:exclamation: 엘라스틱서치 2.3 인경우 `인덱스`/`타입` 형태로 구성되어 있습니다.  
이런경우 document 정보를 가져올때 `타입` 도 명시해줘야 검색됩니다.  

biz_user/user 로 구성된 경우입니다.  
user 타입을 제외하면 찾지 못합니다.  

```bash
$ curl localhost:9200/biz_user/_doc/user1
{"_index":"biz_user","_type":"_doc","_id":"user1","found":false}
```

biz_user/user 로 질의하니 데이터가 조회됩니다. 

```bash
$ curl localhost:9200/biz_user/user/user1
{"_index":"biz_user","_type":"user","_id":"user1","_version":1,"found":true,
    "_source":{"userId":"cbcxxxb0-xxxx-xxxx-9c70-00xxxxac1497","regDate":1479434836980}
} 
```


## Update 

문서 업데이트를 해보겠습니다. 

{:.table.table-key-value-60}
| version  | command  |
|---|---|
| 엘라스틱서치 2.3   | PUT <인덱스>/<도큐먼트 타입>/<도큐먼트 id>/_update |
| 엘라스틱서치 5.x 이후 버전  | PUT <인덱스>/<도큐먼트 id>/_update  |




기본 사용법은 아래와 같습니다. 
```
curl -X PUT "localhost:9200/index/type/document_id/_update" -H 'Content-Type: application/json' -d '
{
  "doc": {
    "new_field": "new_value"
  }
}
'
```

id 1인 document 를 update 해보겠습니다. 

id 1 인 도큐먼트를 Update 하기 전 데이터 조회해보겠습니다. 
```bash
$ curl http://localhost:9200/author/_doc/1
{"_index":"author","_id":"1","_version":1,"_seq_no":0,"_primary_term":1,"found":true,"_source":
{
    "name" : "name-1",
    "gender" : "male"
}}
```

나이를 추가하기 위해 아래와 같이 작성했습니다. 
```bash 
$ curl -X PUT 'localhost:9200/author/_doc/1' -H 'Content-Type: application/json' -d '
{
  "doc": {
    "age": "20"
  }
}'
```

추가가 잘되었는지 확인하기위해 재 질의합니다. 
```bash
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
```bash 
$ curl -X POST 'localhost:9200/author/_doc/1' -H 'Content-Type: application/json' -d '
{
  "doc": {
    "age": "21"
  }
}'
{"_index":"author","_id":"1","_version":3,"result":"updated","_shards":{"total":1,"successful":1,"failed":0},"_seq_no":5,"_primary_term":1}
```

:cactus: 그렇습니다.
PUT(update) 메서드는 기존 필드에 새로운 필드를 추가해주는게 아니라. 
문서 전체 내용을 변경하는 명령이였습니다.  
(엘라스틱 버전 8.9에서 POST, PUT 동일하게 동작하는것을 학인하였습니다.)

기존 필드에 신규 필드를 추가하려면 Elasticsearch 에서 제공하는 script 기능을 이용하거나 (설정 확인필요),   
개발 스크립트를 작성하여 기존 필드 데이터를 가져와서 더한 후 update 하도록 작성하여 실행해줘야 합니다.   
관련 내용은 [ElasticSearch 3/4]({% post_url 2023-12-11-ElasticSearch3 %}) 에서 이어서 작성하였습니다.  

## Delete Document


Document 를 삭제해보겠습니다.   
"successful":1 로 보아 1건이 성공적으로 삭제되었군요.  

```bash
$ curl -XDELETE localhost:9200/author/_doc/2
{"_index":"author","_id":"2","_version":4,"result":"deleted","_shards":{"total":1,"successful":1,"failed":0},"_seq_no":6,"_primary_term":1}
```



## Select Index

특정 인덱스의 정보를 확인해보겠습니다.   
실습환경에 인덱스(index) `author` 에 도큐먼스 2건 생성 후 1건은 수정, 1건은 삭제하였습니다.  

현재 author 인덱스에 남아있는 Doc 은 `hits.total.value = 1` 로 1건 나오네요.  
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
_source 항목에서 1건 Ducument 확인 할수 있습니다. 



## Delete Index 

인덱스를 삭제해보겠습니다.  
실습환경에서 삭제 테스트용 Index 를 생성 후 조회합니다. 

```bash 
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

인덱스를 삭제합니다. 
```bash
$ curl -XDELETE localhost:9200/books
{"acknowledged":true} 
```
응답값 `"acknowledged":true` 으로 보아 성공적으로 삭제되었습니다.  



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
