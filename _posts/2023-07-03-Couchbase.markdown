---
layout: post
title:  "Couchbase on Apple MacOS M1 Silicon"
series: 
date: 2023-07-18 15:00:00 +0900
abstract: "Install Couchbase Server on macOS"
tags: [Couchbase, NoSQL]
image:
toc: true
categories: Tech
last_modified_at: 
---



내가 볼려고 정리하는 Couchbase ~! 


![Couchbase logo]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase_logo.png)

- 공식 사이트  https://www.couchbase.com  
- Learn > Blog https://www.couchbase.com/blog/  
- 레퍼런스 ex)집계 함수 https://docs.couchbase.com/server/current/n1ql/n1ql-language-reference/aggregatefun.html
- couchbase java client 2.7.23 https://docs.couchbase.com/sdk-api/couchbase-java-client-2.7.23/com/couchbase/client/java/Bucket.html
- java SDK 2.2 https://docs-archive.couchbase.com/java-sdk/2.2/java-intro.html
- java SDK 3.4 https://docs.couchbase.com/java-sdk/current/hello-world/start-using-sdk.html
- 국내 블로그 https://couchbase.tistory.com/   

- N1QL     
- tutorials https://query-tutorial.couchbase.com/tutorial   
- Get Started https://docs.couchbase.com/server/current/getting-started/try-a-query.html   
- query https://docs.couchbase.com/java-sdk/current/concept-docs/n1ql-query.html   
- SQL++ Language Reference https://docs.couchbase.com/server/current/n1ql/n1ql-language-reference/index.html  
- SQL++ Language Reference https://docs.couchbase.com/server/current/n1ql/n1ql-language-reference/selectclause.html
- N1QL https://www.baeldung.com/n1ql-couchbase  



## 설치 환경

MacBook Pro 16  
MacOS Ventura 13.4.1  
칩 Apple M1 Pro  
메모리 16GB 


## Download

카우치베이스 공홈에서 설치 안내 페이지를 제공합니다. 
https://docs.couchbase.com/server/current/install/macos-install.html


안내 페이지에 있는 [downloads page](https://www.couchbase.com/downloads/) 경로로 이동합니다. 
Server 를 선택하시고요.   
Version은 최신 버전을 선택했습니다.  
os 는 MacOS ARM64 를 선택했습니다. 
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-01.png)

<br/>
CPU 타입이 궁금하시다면 아래와 같이 확인해보시면 됩니다.  
마지막에 arm64가 있군요.  
```bash
$ uname -a       
Darwin AL01599787.local 22.5.0 Darwin Kernel Version 22.5.0: Thu Jun  8 22:22:20 PDT 2023; root:xnu-8796.121.3~7/RELEASE_ARM64_T6000 arm64
```


## Install 

다운로드 받은 설치파일을 실행합니다.  
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-02.png)

Server 답게(?) 1G 용량을 자랑하는군요.  
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-03.png)

인터넷에서 다운로드 한 파일 안내가 나오는군요.  
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-04.png)

설치가 무척 쉽습니다. 이제 설정을 할 차례이군요. 
<br>
인터넷 브라우저 http://127.0.0.1:8091/ui/index.html 에서 진행해 보아요.    
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-05.png)

카우치베이스는 클러스터가 디폴트입니다.  
클러스터의 이름을 적어주세요.  
서비스명을 적어주거나 확장가능성을 고려해서 적어주시면 되겠네요.   
![](/assets/article_images/2023-07-03-Couchbase/couchbase-06.png)  

약관동의 체크, 사용정보 제공은 체크해제 하였습니다.   
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-07.png)

Defaults 설정하는 버튼과 직접 설정하는 버튼이 있습니다.  
직접 설정하는 버튼이 활성되어 있습니다.    

![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-08.png)

직접 설정하는 버튼을 눌른 다음 화면입니다. 기본값으로 진행합니다.   
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-09.png)

설정이 완료되어 서버가 정상 동작하는 화면이 나왔습니다. <br>
버킷이 아직 하나도 없는 상태이군요. 샘플 버킷을 제공하는군요. 설치해볼까요? 
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-10.png)

travel-sample 버킷으로 생성해보겠습니다.  
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-11.png)

travel-sample 버킷 Documents 를 확인해볼까요?   
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-13.png)

travel-sample 버킷 생성후 얼랏 메세지가 발생하였습니다.    
<br>
Warning: approaching low index resident percentage. Indexer RAM percentage on node "127.0.0.1" is 0%, which is under the threshold of 10%.  <br>
경고: 낮은 지수 상주 비율에 근접하고 있습니다. 노드 "127.0.0.1"의 인덱서 RAM 백분율은 0%이며 임계값 10% 미만입니다.  <br>
<br>
관련     
https://www.couchbase.com/forums/t/index-resident-ratio-below-10-with-70-memory-available/34724/2
<br>

![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-12.png)


얼랏 메세지를 확인 못했다면 Logs 메뉴에서도 확인가능합니다.   
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-14.png)

Index 설정값을 변경했습니다.   
![]({{ site.url }}/assets/article_images/2023-07-03-Couchbase/couchbase-16.png)


Index 설정값 변경시 인덱스가 잠시 중지된다는 안내를 보여주는군요.  
Warning: Changing the index memory quota will cause the index processes to be restarted and will make indexes briefly unavailable. Are you sure you wish to continue?  
경고: 인덱스 메모리 할당량을 변경하면 인덱스 프로세스가 다시 시작되고 인덱스를 잠시 사용할 수 없게 됩니다. 계속하시겠습니까?  
![](/assets/article_images/2023-07-03-Couchbase/couchbase-15.png)


## Use 

Couchbase Workbench 사용을 해볼까요?    
Query workbench 사용안내를 https://docs.couchbase.com/server/current/tools/query-workbench.html 에서도 확인할 수 있습니다.   


## Query 


```sql
select meta().id from `travel-sample`.`_default`.`_default` data order by meta().id limit 10 offset 10
select meta().id from `travel-sample`.`inventory`.`airline` data order by meta().id limit 10 offset 0
```

Document ID : airline_18241
```json
  {
    "id": 18241,
    "type": "airline",
    "name": "Royal Airways",
    "nick_name": "willow",
    "iata": "KG",
    "icao": "RAW",
    "callsign": "RAW",
    "country": "United States"
  }
```

```sql
select * from `travel-sample`.`_default`.`_default` data where meta().id = 'airline_18241'
```
전체 Doucumnt 에 항목 추가 
  
```sql
  UPDATE `travel-sample`.`_default`.`_default` data
  SET city = 'Seoul'
  RETURNING data;
```
The current dataset, 95764626 bytes, is too large to display quickly.

1개 Document 에 city value 변경 
```sql
  UPDATE `travel-sample`.`_default`.`_default` data 
  USE KEYS "airline_18241"
  SET city = 'Busan'
  RETURNING data;
```

2개 Document city value 변경 
```sql
  UPDATE `travel-sample`.`_default`.`_default` data 
  USE KEYS ["airline_10", "airline_10123"]
  SET city = 'Gwangju'
  RETURNING data;
```

