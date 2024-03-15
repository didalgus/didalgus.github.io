---
layout: post
title:  "Update Document in ElasticSearch"
date: 2024-03-14 18:40:00 +0900
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
* [ElasticSearch 3/4]({% post_url 2023-12-11-ElasticSearch3 %}) <span class="series">SERIES 3/4</span>
* **ElasticSearch 4/4 ✓**  <span class="series">SERIES 4/4</span>


운영환경에서 Document를 업데이트 해야 하는 이슈가 종종 있어 `나를 위해` 정리합니다.  
터미널이 익숙해서 터미널에서 진행하였습니다.  

## goal 

특정 id를 가진 Document 내 필드(Field)중에 1개 필드 값을 변경하려고 합니다.   

| AS-IS | TO-BE  |
| --- | --- |
| serviceOpenStatus = 0  |  serviceOpenStatus = 1   |


## 운영 환경 

* OS Version : CentOS Linux release 7.4 
* ElasticSearch Version : 2.3.0 


## mapping 

serviceOpenStatus의 필드 데이터 타입을 확인하기 위해 매핑정보를 확인합니다.  

실제 운영 데이터의 필드가 많아 예시용으로 간단하게 줄였습니다.     
serviceOpenStatus의 데이터 타입은 `long` 이군요. 
```
$ curl localhost:9200/willow/car/_mapping

{
  "willow": {
    "mappings": {
      "car": {
        "properties": {
          "approveDate": {
            "type": "date",
            "format": "strict_date_optional_time||epoch_millis"
          },
          "approveStatus": {
            "type": "integer"
          },
          "asRequestId": {
            "type": "string"
          },
          "serviceOpenStatus": {
            "type": "long"
          }
        }
      }
    }
  }
}
```

## Select 

변경하고자 하는 Document를 조회합니다.  
`_version` 은 49, 
`serviceOpenStatus` 의 값은 0 입니다. 
```
$ curl localhost:9200/willow/car/0023637E9830
{
  "_index": "willow",
  "_type": "car",
  "_id": "0023637E9830",
  "_version": 49,
  "found": true,
  "_source": {
    "approveDate": 1636507544404,
    "approveStatus": 2,
    "asRequestId": "",
    "serviceOpenStatus": 0
  }
}
```
<br/>

Update 시 사용할 `_source`데이터를 가져옵니다.   
```
$ curl localhost:9200/willow/car/0023637E9830/_source
{
  "approveDate": 1636507544404,
  "approveStatus": 2,
  "asRequestId": "",
  "serviceOpenStatus": 0
}
```


## Update 

지난 포스팅에서 POST 와 PUT 이 동일하게 동작하는것을 알게되었습니다. 

POST, PUT 메서드는 동일 Document 값을 변경하게되면 `_version` 값은 1 올리고, `_source`의 Data는 삭제하고 신규 생성합니다.   
그래서 특정필드 1개만 변경하고자 할때에도 기존 데이터도 함께 포함해야 합니다.  
<br />
serviceOpenStatus 의 값을 1로 변경한 후 POST 실행합니다.  
```
$ curl -X POST "localhost:9200/willow/car/0023637E9830" -d'
{"approveDate": 1636507544404,"approveStatus": 2,"asRequestId": "","serviceOpenStatus":1}'
```



## Select 

변경한 내용이 적용되었는지 확인합니다. 

`_version` 은 50, 
`serviceOpenStatus` 의 값은 1로 바뀌었네요. 
```
$ curl localhost:9200/willow/car/0023637E9830
{
  "_index": "willow",
  "_type": "car",
  "_id": "0023637E9830",
  "_version": 50,
  "found": true,
  "_source": {
    "approveDate": 1636507544404,
    "approveStatus": 2,
    "asRequestId": "",
    "serviceOpenStatus": 1
  }
}
```


