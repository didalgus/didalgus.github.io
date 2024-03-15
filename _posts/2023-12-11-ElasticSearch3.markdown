---
layout: post
title:  "Adding fields to ElasticSearch"
date: 2023-12-11 18:00:00 +0900
abstract: "ElasticSearch"
tags: [ElasticSearch]
image:
toc: true
categories: Tech
last_modified_at: 
published: true
---

* [ElasticSearch 1/4]({% post_url 2023-08-23-ElasticSearch %}) <span class="series">SERIES 1/4</span>
* [ElasticSearch 2/4]({% post_url 2023-12-07-ElasticSearch2 %}) <span class="series">SERIES 2/4</span>
* **ElasticSearch 3/4 ✓**  <span class="series">SERIES 3/4</span>
* [ElasticSearch 4/4]({% post_url 2024-03-14-ElasticSearch4 %}) <span class="series">SERIES 4/4</span>

## 개요 

매핑 정보를 선언해서 사용중인 Index(type) 에 신규 매핑정보를 추가 후 인덱싱을 해보겠습니다.   
바로 운영환경에 반영했다가 발생하는 리스크를 감당할 수 없기에 로컬 엘라스틱 서치에서 시나리오 검증을 하겠습니다.   

<br/>
결론 스포. 운영환경 설정에 script 실행이 비활성되어 있어, 벌크인덱스 스크립트를 개발해서 실행했습니다. (ㅠㅠ)  

동적 매핑 설정은 `confing/elasticsearch.yml` 에서 아래 항목으로 확인 가능합니다.   
따로 설정이 없다면 기본값입니다. 기본값은 비활성 상태입니다.  
index.auto_create_index  

<br >
운영환경과 로컬환경의 버전 및 설정 간극이 크군요. (허허)

{:.table.table-key-value-60}
| 운영환경 | 로컬|
|---|---|
| 2.3.0 | 8.9.1 |

## elasticsearch-8.9.1 
### _update_by_query

[ElasticSearch 2/3]({% post_url 2023-12-07-ElasticSearch2 %})에서 `update` 명령으로는 기존 매핑에 신규 필드를 추가할 수 없는것을 확인하였습니다.  
이런경우 사용하는 명령이 `_update_by_query` 입니다. 

elasticsearch-8.9.1 버전에서 매핑 선언없이 바로 추가해보겠습니다. 

```json
$ curl -X POST "localhost:9200/books/_update_by_query" -H 'Content-Type: application/json' -d '
{
  "script": {
    "inline": "ctx._source.type = params.new_value",
    "params": {
      "new_value": "novel"
    }
  },
  "query": {
    "match_all": {}
  }
}
'
```

응답 내용입니다.  
```json
{
  "took": 133,
  "timed_out": false,
  "total": 2,
  "updated": 2,
  "deleted": 0,
  "batches": 1,
  "version_conflicts": 0,
  "noops": 0,
  "retries": {
    "bulk": 0,
    "search": 0
  },
  "throttled_millis": 0,
  "requests_per_second": -1,
  "throttled_until_millis": 0,
  "failures": []
}
```

전체 2건중 2건 Update 성공하였습니다. 
```json
  "total": 2,
  "updated": 2, 
```

매핑 정보를 볼까요? 
```json
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
          "type" : "text"
        }
      }
    },
... 생략 
```

위처럼 실행하면 type필드는 text로 매핑 적용됩니다. 
keyword 타입으로 사용할 예정입니다. 

매핑선언을 하겠습니다. 기존 인덱스는 삭제하고 다시 생성합니다.  

### create mapping  

매핑선언을 하는 규칙입니다. 
```
PUT camera
{
  "mappings": {
    "properties": {
      "<필드명>":{
        "type": "<필드 타입>"
        … <필드 설정>
      }
      …
    }
  }
}
```

신규 매핑을 생성합니다. 
```
$ curl -X PUT "localhost:9200/books" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "mappings": {
    "properties": {
      "title": { "type": "text" },
      "pages": { "type": "long" }
    }
  }
}'

{"acknowledged":true,"shards_acknowledged":true,"index":"books"}
```

### Insert data 

데이터를 2건 입력합니다. 
```
curl -XPOST http://localhost:9200/books/_doc/1 -H 'Content-Type: application/json' -d '
{
    "title" : "title-1",
    "pages" : 250
}'
```
```
curl -XPUT http://localhost:9200/books/_doc/2 -H 'Content-Type: application/json' -d '
{
    "title" : "title-2",
    "pages" : 300
}'
```



### add mapping

기존 매핑 정보에 신규 항목 2개를 추가합니다. 

Elasticsearch 5.0 이후에서는 keyword 타입이 권장되며, not_analyzed는 더 이상 사용되지 않습니다.   
따라서 최신 버전의 Elasticsearch에서는 keyword 타입을 사용하는 것이 좋습니다.

<br>  

사용법  
```
PUT /your_index/_mapping/your_type
{
  "properties": {
    "new_field": {
      "type": "text"
    }
  }
}
```
author, type 필드를 추가합니다.  
```
$ curl -XPUT 'localhost:9200/books/_mapping' -H 'Content-Type: application/json' -d '
{
  "properties": {
    "type": {
      "type": "keyword"
    },
    "author": {
      "type": "text"
    }
  }
}
'

{"acknowledged":true}
```


추가한 매핑정보를 확인합니다.  
```
curl http://localhost:9200/books\?pretty\=true
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


### bulk API 

사용법 
```
curl -X POST "localhost:9200/your_index/your_type/_update_by_query" -H 'Content-Type: application/json' -d '
{
  "script": {
    "inline": "ctx._source.new_field = params.new_value",
    "params": {
      "new_value": "new_value"
    }
  },
  "query": {
    "match_all": {}
  }
}
'
```


`_update_by_query` 를 실행하기 전 document 정보를 확인해보았습니다.   
매핑 선언은 되어 있는 상태지만 값을 입력하기 전입니다.   
매핑정보는 있어도 값이 없으면 나오지 않는것을 확인하였습니다.   

curl http://localhost:9200/books/_doc/1

{
  "_index": "books",
  "_id": "1",
  "_version": 1,
  "_seq_no": 0,
  "_primary_term": 1,
  "found": true,
  "_source": {
    "title": "title-1",
    "pages": 250
  }
}


신규 생성한 필드 2개중 type 을 먼저 데이터 입력하겠습니다.  
아래 스크립트는 동일한 value 를 일괄 입력할때 유용합니다. 

```
$ curl -X POST "localhost:9200/books/_update_by_query" -H 'Content-Type: application/json' -d '
{
  "script": {
    "inline": "ctx._source.type = params.new_value",
    "params": {
      "new_value": "novel"
    }
  },
  "query": {
    "match_all": {}
  }
}
'

{
  "took": 34,
  "timed_out": false,
  "total": 2,
  "updated": 2,
  "deleted": 0,
  "batches": 1,
  "version_conflicts": 0,
  "noops": 0,
  "retries": {
    "bulk": 0,
    "search": 0
  },
  "throttled_millis": 0,
  "requests_per_second": -1,
  "throttled_until_millis": 0,
  "failures": []
}
```
"total": 2, "updated": 2 : 
전체 2건에서 2건 업데이트 되었습니다. 


Document id 1를 확인해보겠습니다.  
"type": "novel" 값이 추가되었네요. 
```
$ curl http://localhost:9200/books/_doc/1
{
  "_index": "books",
  "_id": "1",
  "_version": 2,
  "_seq_no": 2,
  "_primary_term": 1,
  "found": true,
  "_source": {
    "pages": 250,
    "title": "title-1",
    "type": "novel"
  }
}
```

두번째 항목 `author` 데이터값도 일괄 입력할까요?

```
$ curl -X POST "localhost:9200/books/_update_by_query" -H 'Content-Type: application/json' -d '
{
  "script": {
    "inline": "ctx._source.author = params.new_value",
    "params": {
      "new_value": "author_basic"
    }
  },
  "query": {
    "match_all": {}
  }
}
'

{"took":36,"timed_out":false,"total":2,"updated":2,"deleted":0,"batches":1,"version_conflicts":0,"noops":0,"retries":{"bulk":0,"search":0},"throttled_millis":0,"requests_per_second":-1.0,"throttled_until_millis":0,"failures":[]}
```




## ElasticSearch 2.3.0 


## mapping 

Elasticsearch 2.3 에서는 keyword 타입이 도입되지 않았습니다.   
keyword 타입은 Elasticsearch 5.0 버전에서 소개되었습니다.  
이전 버전인 Elasticsearch 2.x에서는 not_analyzed로 문자열을 정확한 일치를 위해 사용했으며, string 타입이나 다른 타입을 사용하여 분석되지 않는 필드를 정의했습니다.

Elasticsearch 에서 선언이 가능한 문자열 타입에는 text, keyword 두 가지가 있습니다.   
2.x 버전 이전에 문자열은 string 이라는 하나의 타입만 있었고 텍스트 분석 여부, 즉 애널라이저 적용을 할 것인지 아닌지를 구분하는 설정이 있었습니다.   
5.0 버전 부터는 텍스트 분석의 적용 여부를 text 타입과 keyword 타입으로 구분을 합니다.   
* [Elastic 가이드북 : 7.2.1 문자열 - text, keyword](https://esbook.kimjmin.net/07-settings-and-mappings/7.2-mappings/7.2.1) 


매핑정보를 확인합니다.

```
$ curl http://localhost:9200/AA/camera/_mapping?pretty
{
  "AA" : {
    "mappings" : {
      "camera" : {
        "properties" : {
...
          "Email" : {
            "type" : "string",
            "index" : "not_analyzed"
          },
          "Mobile" : {
            "type" : "string",
            "index" : "not_analyzed"
          },
```
이 매핑에서 "index": "not_analyzed"은 Elasticsearch 2.x에서 사용되었던 구문으로, 해당 필드가 분석되지 않고 정확한 일치를 위한 keyword 타입으로 사용되는 것을 나타냅니다.

따라서, 이 경우에는 termQuery를 사용하는 것이 더 적절합니다. 
termQuery는 정확한 값을 검색하며, 분석기에 의해 처리되지 않은 경우에 사용됩니다. 
matchQuery는 텍스트를 분석하여 일치하는 문서를 찾기 위한 용도로 사용되므로, 
분석되지 않은 상태의 정확한 값에 대해서는 termQuery를 권장합니다.


매핑 정보에 신규 항목을 추가합니다. 
```
$ curl -X PUT 'localhost:9200/AA/camera/_mapping' -H 'Content-Type: application/json' -d'
{
    "properties": {
      "encodingType": {
        "type" : "string",
        "index" : "not_analyzed"
      },
      "encodingUpdateDate": {
        "type": "long"
      }
    }
}'

{"acknowledged":true} 
```


신규 항목을 잘 추가되었는지 확인합니다. 

```
$ curl http://localhost:9200/AA/camera/_mapping?pretty

응답생략
```

### _update_by_query

사용법 
```
curl -X POST "localhost:9200/your_index/your_type/_update_by_query" -H 'Content-Type: application/json' -d '
{
  "script": {
    "inline": "ctx._source.new_field = params.new_value",
    "params": {
      "new_value": "new_value"
    }
  },
  "query": {
    "match_all": {}
  }
}
'
```

`_update_by_query` 를 실행합니다.  
```
$ curl -X POST "localhost:9200/AA/camera/_update_by_query" -H 'Content-Type: application/json' -d '
{
  "script": {
    "inline": "ctx._source.encodingType = params.new_value",
    "params": {
      "new_value": "h264"
    }
  },
  "query": {
    "match_all": {}
  }
}'

{
  "error": {
    "root_cause": [
      {
        "type": "script_exception",
        "reason": "scripts of type [inline], operation [update] and lang [groovy] are disabled"
      }
    ],
    "type": "script_exception",
    "reason": "scripts of type [inline], operation [update] and lang [groovy] are disabled"
  },
  "status": 500
}
```
에러가 나는군요. 

inline 를 source 로 바꾸어도 에러가 납니다 
```
$ curl -X POST 'localhost:9200/AA/camera/_update_by_query' -H 'Content-Type: application/json' -d '
{
  "script": {
    "source": "ctx._source.encodingType = params.new_value",
    "params": {
      "new_value": "h264"
    }
  },
  "query": {
    "match_all": {}
  }
}'

{
  "error": {
    "root_cause": [
      {
        "type": "script_parse_exception",
        "reason": "expected one of [inline], [file] or [id] fields, but found none"
      }
    ],
    "type": "script_parse_exception",
    "reason": "expected one of [inline], [file] or [id] fields, but found none"
  },
  "status": 500
}
```

보안 관련하여 script 실행이 비활성화된 경우였습니다.  
설정을 변경 하게 되면 엘라스틱 재구동 해야 합니다. 
결국... 개발 스크립트를 작성하여 실행하여 업데이트 하였습니다.   



## 특정 필드가 없는 Doc 찾기

```
➜  ~ curl -XGET 'localhost:9200/cam/camera/_search' -H 'Content-Type: application/json' -d '
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
    "hits":{
        "total":2412,
        "max_score":null,
        "hits":[
            {
                "_index":"AA","_type":"camera","_id":"CCCC","_score":null
```
bool: 불(bool) 쿼리는 내부의 질의로 다른 쿼리를 포함시켜 사용합니다. 쿼리를 조건문인 불 조합(bolean combination)으로 적용해서 최종 검색 결과를 나타냅니다.
- must_not: 특정 필드가 지정된 값을 가지고 있지 않은 문서를 찾기 위한 쿼리입니다.
- exists: 특정 필드가 존재하는지 여부를 확인하는 쿼리입니다.
- should : 반드시 해당 될 필요는 없지만 해당된다면 더 높은 스코어를 가집니다. OR 조건과 유사합니다. 
- field: 확인할 필드의 이름을 지정합니다. 위의 예제에서는 "A" 필드입니다.

## Pagging

Query DSL을 사용하여 페이징 쿼리를 적용하였는데 중복 이슈가 발생하였습니다.  
똑똑한 ChatGPT! 

```
Elasticsearch 2.x에서 sort 옵션이 없는 경우 페이징 호출 시 중복 데이터가 호출되는 문제는 Elasticsearch의 동작과 직접적으로 연관된 문제일 수 있습니다.   
Elasticsearch의 검색은 기본적으로 스코어(score)에 따라 결과를 반환하며, 스코어가 동일한 문서들은 정렬이 무작위로 이루어질 수 있습니다.   
따라서 페이징을 구현할 때 sort 옵션을 사용하지 않으면 동일한 스코어를 가진 문서들이 서로 다른 페이지에서 중복해서 나타날 수 있습니다.  

이 문제를 해결하기 위해서는 페이징 요청에 대해 명시적인 정렬을 지정하는 것이 좋습니다.   
Elasticsearch의 검색 결과를 정렬하는 데에는 sort 옵션을 사용할 수 있습니다. 
sort 옵션을 사용하여 특정 필드를 기준으로 정렬하면, 페이징 호출 시에도 예측 가능한 순서로 문서가 반환됩니다.  
```

Sort 옵션을 넣어주었습니다. 중복 발생 이슈 해결!  

```
{
  "from" : 0,
  "size" : 1000,
  "query" : {
    "bool" : {
      "must" : [ {
        "range" : {
          "statusDate" : {
            "from" : 0,
            "to" : 1702461662256,
            "include_lower" : true,
            "include_upper" : true
          }
        }
      }, {
        "wildcard" : {
          "serialNo" : "*AAA*"
        }
      } ]
    }
  },
  "sort" : [ {
    "statusDate" : {
      "order" : "desc",
      "missing" : "_last"
    }
  }, {
    "serialNo" : {
      "order" : "desc"
    }
  } ]
}

```




## 참고 문서


* [Elasticsearch Guide 2.3 : Index](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/index.html)
* [Elasticsearch Guide : Update By Query API](https://www.elastic.co/guide/en/elasticsearch/reference/2.3/docs-update-by-query.html)

* [Elastic 가이드북 : Index](https://esbook.kimjmin.net/)
* [Elastic 가이드북 : 7.2.1 문자열 - text, keyword](https://esbook.kimjmin.net/07-settings-and-mappings/7.2-mappings/7.2.1) 
* [Elastic 가이드북 : 7.2 매핑 - Mappings](https://esbook.kimjmin.net/07-settings-and-mappings/7.2-mappings) 
* [Elastic 가이드북 : 4.3 벌크 API - _bulk API](https://esbook.kimjmin.net/04-data/4.3-_bulk) 