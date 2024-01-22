---
layout: post
title:  "ElasticSearch"
date: 2023-08-23 16:30:00 +0900
abstract: "ElasticSearch"
tags: [ElasticSearch]
image:
toc: true
categories: Tech
last_modified_at: 
published: true
---


* **ElasticSearch 1/3 ✓**  <span class="series">SERIES 1/3</span>  
* [ElasticSearch 2/3]({% post_url 2023-12-07-ElasticSearch2 %}) <span class="series">SERIES 2/3</span>
* [ElasticSearch 3/3]({% post_url 2023-12-11-ElasticSearch3 %}) <span class="series">SERIES 3/3</span>


엘라스틱서치는 세이베논(Shay Banon)이 아파치 루씬을 이용하여 개발했으며 오픈소스 프로젝트로 운영하고 있습니다.   
분산환경 병렬 처리와 실시간 검색을 지원하고 확장성이 뛰어납니다.   

![ElasticSearch logo](/assets/article_images/2023-08-23-ElasticSearch/ElasticSearch-logo.png)


## 엘라스틱서치 특징   

* 아파치 루씬 기반 
* 분산 시스템 (Distributed)  
* 전문검색 (Full Text Search)
* 점수화를 이용한 정렬 
* 실시간 검색 (Real Time)
* 다양한 플러그인 
* 빅데이터 플랫폼(AWS, AZURE, Hadoop)과의 연동  
* RESTFul/HTTP API  
* 스키마 프리 (Schema Free)
* 자바 가상머신이 설치된 모든 OS에서 사용 (JAVA)

### 버전 

1.1.x 이하 JAVA 6 이상   
1.2.x 부터 JAVA 7 이상 


## 아파치 루씬

엘라스틱 서치의 기반인 아파치 루씬을 알아보겠습니다.  
아파치 루씬 (Apache Lucene) 은 데이터를 검색하는데 가장 널리 사용되는 자바로 개발된 라이브러리 입니다.   

2000년 3월 더그 커팅 (Doug Cutting) 개발자가 최초 버전을 공개하였습니다.  
자바 언어로 개발되었습니다.  

* 사용자 위치정보(geolocation)
* 다국어 검색
* 철자 수정 기능(Did you mean suggestion) 
* 자동완성
* 미리보기 기능 


## JSON 

JSON (Javascript Object Notation) 웹에서 자료를 주고받을 때 사용하는 경량 데이터 형식입니다.  

데이터 용량이 획기적으로 줄었고 따라서 전송 및 처리 속도도 빨라졌습니다.   
또한 사람이 읽고 쓰기에도 쉬운 형식입니다.  

* javascript 구문 형식 사용  
    - 기본자료형 : 수(정수, 실수), 문자열("), 참/거짓 (true/false)
    -  집합 자료형 : 객체(비순서화된 SET `{`, `}`), 배열(순서화된 SET `[`, `]`)
* XML 에 비교하여 데이터 데이터 용량 경량
* 전송, 처리 속도 향상 
* 사람과 기계 둘다 분석 및 처리에도 용이함 

```json
{
  "name" : "hong gi-dong",
  "age" : 18,
  "hobby" : ["music", "book"],
  "friend" : {
      "유관순" : {"name" : "hong gi-dong", "age" : 18},
      "이순신" : {"name" : "hong gi-dong", "age" : 40}
  }
}
```

## 구성

엘라스틱 서치는 Cluster 방식으로 구성합니다. 

물리서버 1대에서 엘라스틱서치 프로세스를 여러개 띄워 Cluster 구축하거나, 여러 물리서버에 노드를 각각 띄워 Cluster 구축 가능합니다.  

<br>

엘라스틱 서치는 젠 디스커버리(Zen Discovery)를 비롯한 다양한 디스커버리를 내장하고 있기 때문에 별도의 분산 시스템 관리자(ex. 솔라의 주키퍼)가 필요하지 않아 분산 환경을 쉽게 구축할 수 있습니다.  

젠 디스커버리(Zen Discovery) 기능은 멀티캐스트와 유니캐스트 방식이 있습니다. 

멀티캐스트 방식은 접근할 수 있는 모든 네트워크의 노드를 자동으로 검색하고 같은 클러스터명을 가진 노드끼리 바로 바인딩 하는 기능입니다. 
기본적으로 멀티캐스트 방식이 활성화 되어 있습니다.   
하지만 멀티캐스트는 의도하지 않는 노드와의 바인딩이 일어날 가능성도 있고 네트워킹의 불안정감 때문에 공식 엘라스틱 서치 운영그룹에서도 멀티캐스트보다는 유니캐스의 사용을 권장하고 있습니다. 

유니캐스트 방식은 접근할 네트워크 주소를 지정해서 지정한 노드 사이에서만 바인딩을 실행하는 방식입니다.  

confing/elasticsearch.yml 파일에 유니캐스트 방식 설정하는 예시입니다. 

```ini
discovery.zen.ping.multicast.enabled: false 
discovery.zen.ping.unicast.hosts: ["10.000.000.1", "10..000.000.2"]
discovery.zen.minimum_master_nodes: 2
```

엘라스틱 서치는 하나 이상의 노드로 구성됩니다.  
`노드(node)`란 실행된 하나의 엘라스틱 서치 프로세스를 입니다.   
`클러스터(cluster)`란 각 노드가 연결된 전체 시스템 입니다.   

여러대의 서버가 하나의 클러스터를 구성 할 수 있으며, 반대로 하나의 물리 서버에 여러개의 클러스터가 존재 할 수 있습니다.   
confing/elasticsearch.yml 파일에 cluster.name 을 서로 다른 이름으로 설정한 노드 두개를 실행하면 바인딩 되지 않고 각각 다른 클러스터를 구성하게 됩니다. 
여러대의 서버인 경우 엘라스틱 서치의 버전은 반드시 같아야 합니다.  


`마스터 노드(Maste node)` 는 전체 클러스터의 상태에 대한 메타 정보를 관리하는 노드입니다. 기존 마스터 노드가 종료되는 경우 새로운 마스터 노드가 선출됩니다. 
confing/elasticsearch.yml 설정 파일에서 node.master 속성이 true 노드는 마스터 노드로 선출 될 수 있습니다.   
node.master 속성이 false 인경우 마스터 노드의 후보로 선정되지 않습니다.  

`데이터 노드(Data node)` 는 색인된 데이터를 실제로 저장하는 노드입니다. node.data 속성이 false 인경우 해당 노드는 데이터를 저장하지 않습니다.  

엘라스틱 서치 시스템을 구성할 때 보통 마스터 노드는 명령수행을 전담하는 창구 기능을 하고, 데이터 노드는 http 통신 기능을 막아 놓아 REST API 접근을 차단하고 데이터를 저장하는 역할만을 전담하도록 설정하는 것이 일반적입니다.  

색인된 데이터를 구성하는 샤드(Shard)와 복제본(Replica)이 있습니다. 

`샤드(Shard)` 는 아파치 루씬에서 사용되는 메커니즘으로 데이터 검색을 위해 구분되는 최소의 단위 인스턴스입니다.  
색인된 데이터는 여러개의 샤드에 분할되어 저장되는데, 기본적으로 인덱스당 5개의 샤드와 5개의 복사본으로 분리됩니다.  
사용자는 인덱스 단위로 데이터를 처리하고 샤드는 엘라스틱 서치가 직접 노드로 분산시키는 작업을 합니다.  

처음에 데이터가 색인되는 저장 공간을 최초 샤드(Primary Shard) 라고 합니다.  
최초 샤드에 데이이터가 색인되면 동일한 최초 샤드 수 만큼 복사본(Replica)을 생성합니다. 
복사본을 사용하는 이유는 데이터 손실(Fail Over)을 방지하는 목적과, 최초 샤드가 유실되는 경우 복사본을 최초 샤드로 승격 시켜 시스템의 무결성을 유지하기 위해서 입니다. 
또 성능향상을 목적으로 최최 샤드와 복사본을 동시에 검색해서 더 빠르게 데이터를 찾을 수 있습니다.  

기본적으로 같은 데이터의 블록의 최초 샤드와 복사본은 서로 다른 노드에 저장합니다.  
이렇게 함으로써 한 노드가 실패해도 둘 중 하나가 생존 상태에 있도록 합니다. 
실행중인 노드가 하나인 경우 복사본은 생성되지 않고 최초 샤드만 존재하게 되는데 장애가 발생하면 데이터 무결성을 보장 할 수 없으므로 노드는 2개 이상 유지 하는 것이 좋습니다.  


이미 생성된 인덱스의 샤드 설정은 변경 할 수 없습니다. 
데이터를 색인하기 전에 샤드와 복사본 개수를 설정해줘야 합니다.  

기본설정은 confing/elasticsearch.yml 설정 파일에서 설정할 수 있습니다. 

```ini
index.number_of_shards: 5
index.number_of_replicas: 1 
```

색인된 데이터의 크기가 5GB 이고 해당 인덱스가 5개의 샤드와 1쌍의 복사본으로 구성된 경우, 1개의 샤드의 용량은 1GB 이며 전체 합은 최초 샤드와 복사본을 합한 10개의 샤드, 총 10GB 가 됩니다. 





## 용어 

* 색인(indexing) : 검색할 데이터를 검색 할 수 있는 구조로 변경하기 위해 원본 문서를 변환하여 저장하는 일련의 과정
* 색인(index) : 색인 과정을 거친 결과물 또는 저장된 데이터 공간
* 검색(searching) : 색인에 들어 있는 토큰을 기준으로 해당하는 토큰이 포함된 문서를 찾는 과정 
* 질의(query) : 사용자가 원하는 결과를 출력하기 위해서 검색 시 입력하는 검색어 또는 검색 조건 


## 환경 


* MacBook Pro 16  
* MacOS Ventura 13.4.1  
* 칩 Apple M1 Pro  
* 메모리 16GB 
* elasticsearch-8.9.1

## 설치

로컬 환경에 ElasticSearch 를 설치해보겠습니다. 

https://www.elastic.co/kr/downloads/elasticsearch 설치하고자 하는 환경에 맞는 OS를 선택합니다.  

![Elasticsearch logo](/assets/article_images/2023-08-23-ElasticSearch/ElasticSearch-001.png)

https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.9.1-darwin-x86_64.tar.gz


Mac M1 AArch64 아키텍처 파일을 다운받아 풀어주겠습니다.  

```bash
apps $ tar -xzvf elasticsearch-8.9.1-darwin-aarch64.tar.gz
```

압축 해제된 디렉토리를 확인합니다. 

```bash
$ ll ~/apps/elasticsearch-8.9.1
total 4384
-rw-r--r--@  1 tree  willow   3.8K  8 10 14:01 LICENSE.txt
-rw-r--r--@  1 tree  willow   2.1M  8 10 14:03 NOTICE.txt
-rw-r--r--@  1 tree  willow   7.9K  8 10 14:01 README.asciidoc
drwxr-xr-x@ 24 tree  willow   768B  8 10 14:05 bin
drwxr-xr-x@ 11 tree  willow   352B  8 10 14:05 config
drwxr-xr-x@  3 tree  willow    96B  8 10 14:05 jdk.app
drwxr-xr-x@ 42 tree  willow   1.3K  8 10 14:05 lib
drwxr-xr-x@  2 tree  willow    64B  8 10 14:03 logs
drwxr-xr-x@ 75 tree  willow   2.3K  8 10 14:05 modules
drwxr-xr-x@  2 tree  willow    64B  8 10 14:03 plugins
```

엘라스틱서치를 구동합니다.  

```bash
$ ~/apps/elasticsearch-8.9.1/bin/elasticsearch -d -p es.pid
```

* -d : 백그라운드 실행
* -p <파일명>  : 프로세스 아이디를 <파일명> 에 저장 


로그를 살펴볼까요? 

```bash
$ ll ~/apps/elasticsearch-8.9.1/bin/elasticsearch/logs/
total 424
-rw-r--r--@ 1 tree  willow    25K  8 23 18:12 elasticsearch.log
-rw-r--r--@ 1 tree  willow     0B  8 23 18:12 elasticsearch_audit.json
-rw-r--r--@ 1 tree  willow     0B  8 23 18:12 elasticsearch_deprecation.json
-rw-r--r--@ 1 tree  willow     0B  8 23 18:12 elasticsearch_index_indexing_slowlog.json
-rw-r--r--@ 1 tree  willow     0B  8 23 18:12 elasticsearch_index_search_slowlog.json
-rw-r--r--@ 1 tree  willow    81K  8 23 18:12 elasticsearch_server.json
-rw-r--r--@ 1 tree  willow    41K  8 23 18:12 gc.log
-rw-r--r--@ 1 tree  willow   2.3K  8 23 18:12 gc.log.00
-rw-r--r--@ 1 tree  willow   2.3K  8 23 18:12 gc.log.01
```

* elasticsearch_index_indexing_slowlog.json : 색인이 일정 시간 이상 소요됐을때 느린 속도로 실행된 내용을 기록하는 로그파일 
* elasticsearch_index_search_slowlog.json : 검색이 일정 시간 이상 소요됐을때 느린 속도로 실행된 내용을 기록하는 로그파일 



구동 로그입니다.  
```bash
$ vi elasticsearch.log

[2023-08-23T18:12:27,853][INFO ][o.e.n.Node               ] [didalgus] JVM home [/elasticsearch-8.9.1/jdk.app/Contents/Home], using bundled JDK [true]
...
[2023-08-23T18:12:33,793][INFO ][o.e.n.Node               ] [didalgus] initialized
[2023-08-23T18:12:33,793][INFO ][o.e.n.Node               ] [didalgus] starting ...
[2023-08-23T18:12:33,863][INFO ][o.e.t.TransportService   ] [didalgus] publish_address {127.0.0.1:9300}, bound_addresses {[::1]:9300}, {127.0.0.1:9300}
```


### 중지

구동중인 elasticsearch PID 를 찾습니다.
```bash
$ ps -ef | grep elasticsearch
user 30017     1 17  2022 ?        96-19:32:08 /home/user/apps/jdk8/bin/java -Xms256m -Xmx15g -Djava.awt.headless=true 
-XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly -XX:+HeapDumpOnOutOfMemoryError 
-XX:+DisableExplicitGC -Dfile.encoding=UTF-8 -Djna.nosys=true 
-Des.path.home=/home/user/apps/elasticsearch-2.3.0 
-cp /home/user/apps/elasticsearch-2.3.0/lib/elasticsearch-2.3.0.jar:/home/user/apps/elasticsearch-2.3.0/lib/* org.elasticsearch.bootstrap.Elasticsearch start 
-p /home/user/elasticsearch/pid/elasticsearch.pid 
-Djava.library.path=/usr/local/lib -d -Des.security.manager.enabled=false 
-Des.default.path.home=/home/user/apps/elasticsearch-2.3.0 
-Des.default.path.logs=/home/user/elasticsearch/log 
-Des.default.path.data=/home/user/elasticsearch/data 
-Des.default.path.conf=/home/user/apps/elasticsearch-2.3.0/config
```

* `-X*` : 자바가 실행되는 메모리를 설정
* `-Xms256m` : 최소 메모리 256M
* `-Xmx1g` : 최대 메모리 1 GB 


PID 를 kill 명령으로 프로세스 종료합니다.  
```bash
  kill 94506
```


# Configuration

엘라스틱 서치 환경 설정 방법은 크게 2가지입니다. 

### yml 

bin/elasticsearch.in.sh 파일과 confing/elasticsearch.yml 를 옵션을 수정하는 방법이 있습니다.

```bash
$ vi ~/elasticsearch-2.3.0/bin/elasticsearch.in.sh

ES_CLASSPATH="$ES_HOME/lib/elasticsearch-2.3.0.jar:$ES_HOME/lib/*"

if [ "x$ES_MIN_MEM" = "x" ]; then
    ES_MIN_MEM=256m
fi
if [ "x$ES_MAX_MEM" = "x" ]; then
    ES_MAX_MEM=15g
fi
```
자바 메모리 용량이 변경되는 불필요한 오버헤드를 방지하기 위해 최소 메모리와 최대 메모리를 동일하게 지정해서 사용하는 것을 권장하고 있습니다.   
메모리 크기는 시스템 전체 메모리의 50% 가 넘지 않도록 하는 것이 안정적이라고 합니다.  

(elasticsearch.in.sh 파일이 8.9.1 버전에는 없군요)
```bash
$ ps -ef | grep elasticsearch
tree 19949  1 17  2023 ? 75-22:33:22 /jdk8/bin/java -Xms256m -Xmx15g -Des.path.home=/elasticsearch-2.3.0 ..생략
```

자바 힙 덤프 파일은 별로도 경로를 지정하지 않으면 엘라스틱서치 홈 디렉토리에 생성됩니다.   
자바 힙 메모리 오류가 발생하면 오류 내용으로 인해 수GB 사이즈의 파일이 생성될 수 있습니다.  
시스템 구성에 맞춰 용량이 여유있는 다른 파티션에 경로를 지정해주는 것이 좋습니다.  

```ini
# The path to the heap dump location, note directory must exists and have enough
# space for a full heap dump.
JAVA_OPTS="$JAVA_OPTS -XX:HeapDumpPath=$ES_HOME/logs/heapdump.hprof"
```

confing/elasticsearch.yml 설정을 살펴보겠습니다. 

```bash
$ vi /elasticsearch-2.3.0/config/elasticsearch.yml

path.data: /elasticsearch/data

# Lock the memory on startup:
bootstrap.mlockall: true

# http.port: 9200       # REST API 통신 포트 
# http.enabled: false   # REST API 통신을 하지 않음 

# transport.tcp.compress: true     # 통신하는 데이터를 압축해서 전송 
# http.max_content_length: 100mb   # 설정된 용량을 초과하면 데이터를 전송하지 않음 
```

* path.data : 데이터 경로는 쉼표를 이용해서 여러 경로를 지정할 수 있습니다. 
* bootstrap.mlockall : true 로 설정하면 자바가상머신 에서 실행중인 엘라스틱서치가 점유하는 메모리를 고정(Lock) 합니다. 
이렇게 하면 가상머신이 엘라스틱서치가 사용하지 않는 메모리를 다른 자바 프로그램으로 돌리는(swap)것을 방지할 수 있습니다. 
* http.port : REST API 서비스를 하기 위해 엘라스틱서치는 9200~9299 범위이 HTTP 통신 포트를 사용합니다.  
새로운 엘라스틱노드를 실행하면 두번째 노드는 9201 포트로 실행됩니다.  
참고로, 엘라스틱 노드가 다른 노드와 바이딩 되어 데이터 교환을 위해 통신하는 포트는 9300 ~ 9399 범위의 HTTP 통신 포트를 사용합니다. 
* http.enabled : false 로 설정하면 해당 엘라스틱서치 노드는 REST API 통신을 하지 않습니다. 
클러스터링해서 운영하는 경우 하나의 노드만 REST API 통신을 허용하고 나머지 노드는 false 로 설정해서 데이터를 저장하는 용도로만 사용할 수 도 있습니다.  


## HTTP 네트워크

같은 서버에서는 별도의 네트워크 설정 없이도 엘라스틱 서치 노드를 여러개 실행하면 바로 바인딩(binding) 됩니다.
같은 서버가 아닌 다른 네트워크에 있는 노드를 연결하려면 네트워크 설정을 해야 합니다. 

confing/elasticsearch.yml 

```ini
network.bind_host: 192.168.0.1
network.publish_host: 192.168.0.1
network.host: 192.169.0.1
```
* network.bind_host : 엘라스틱서치 서버의 내부 IP 주소
* network.publish_host : 외부(공개) IP 주소 
* network.host : 한번에 위의 2개 항목에 동일하게 반영 


REST API 를 서비스하기 위해 엘라스틱 서치는 9200 ~ 9299 범위의 HTTP 통신 포트를 사용합니다.  
서버에서 엘라스틱서치 노드를 처음 실행하면 9200 포트로 실행되고, 동일 서버에서 엘라스틱서치 노드를 또 실행하면 9201 포트로 실행됩니다. 
이런식으로 노드는 포트번호를 늘려가며 실행됩니다. 

<br>
엘라스틱서치 노드가 다른 노드와 바인딩(binding) 되어 데이터 교환을 위해 통신하는 포트는 9300~9399 범위의 HTTP 통신 포트를 사용합니다.  
바인딩 포트도 마찬가지로 같은 서버에서 두개 이상의 노드가 실행된 경우 9300, 9301 과 같이 포트 번호를 늘려가며 실행됩니다. 

<br>
아래 설정 항목에서 수정 할 수 있습니다.  
```ini
transpirt.tcp.port:9300
http.port:9200
```


### REST API 

Restful API 를 지원해서 URI 를 사용한 동작이 가능합니다. 
엘라스틱 서치는 HTTP 프로토콜 port 를 지정하지 않으면 기본값 9200을 사용합니다.  


{:.table.table-key-value-60}

| HTTP Method | CRUD | SQL | 
|---|---|---|
| POST | Create | Insert | 
| GET | Read | Select| 
| PUT | Update | Update | 
| DELETE | Delete | Delete | 

설정 정보를 가져오는 Rest API 를 호출해보겠습니다.  

```bash
$ curl -X GET https://localhost:9200/
{
  "name": "didalgus",
  "cluster_name": "elasticsearch",
  "cluster_uuid": "PoZUOHEM-",
  "version": {
    "number": "8.9.1",
    "build_flavor": "default",
    "build_type": "tar",
    "build_hash": "a813d015ef1826148d9d389bd1c0d781c6e349f0",
    "build_date": "2023-08-10T05:02:32.517455352Z",
    "build_snapshot": false,
    "lucene_version": "9.7.0",
    "minimum_wire_compatibility_version": "7.17.0",
    "minimum_index_compatibility_version": "7.0.0"
  },
  "tagline": "You Know, for Search"
}
```

elasticsearch.yml 설정에 `xpack.security.enabled` 항목 값이 true 설정되어 있어서 https 통신 + 사용자 인증을 합니다.   
로컬 테스트용인지라 해당 옵션을 비활성 하였습니다.  


```
# Enable security features
xpack.security.enabled: false
``` 
재구동 후 API 조회시 http(80) 포트를 사용합니다.  


실무에 사용중인 엘라스틱 서치도 조회해봅니다.  
```bash
$ curl -XGET http://10.xxx.xx.xx:9200
{
  "name" : "elasticsearch",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.3.0",
    "build_hash" : "8371be88371be88371be88371be88371be88371be8",
    "build_timestamp" : "2016-03-29T07:54:48Z",
    "build_snapshot" : false,
    "lucene_version" : "5.5.0"
  },
  "tagline" : "You Know, for Search"
}

$ curl -XGET http://10.xxx.xx.xx:9200/_cluster/stats?pretty=true
{
  "timestamp" : 1693995454267,
  "cluster_name" : "elasticsearch",
  "status" : "green",
  "indices" : {
    "count" : 457,

...

  "nodes" : {
    "count" : {
      "total" : 3,
      "master_only" : 0,
      "data_only" : 0,
      "master_data" : 3,
      "client" : 0
    },
    "versions" : [ "2.3.0" ],

```

* pretty=true : 커맨드 명령으로 조회할때 보기 좋은 결과값을 표현해줍니다.  

보고있는 책의 버전은 1.1.1   
회사 운영중인 엘라스틱서치는 2.3.0   
테스트용 설치는 8.9.1   
와.. 버전 갭 차이..어쩔..  

최신판 책을 샀습니다. 허허  

## URLs.

- Elastic 가이드 북 [https://esbook.kimjmin.net/](https://esbook.kimjmin.net/)
