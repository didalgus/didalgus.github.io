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


* **ElasticSearch 1/2 ✓**  <span class="series">SERIES 1/2</span>  
* [ElasticSearch 2/2]({% post_url 2023-12-07-ElasticSearch2 %}) <span class="series">SERIES 1/2</span>


엘라스틱서치는 세이베논(Shay Banon)이 아파치 루씬을 이용하여 개발했으며 오픈소스로 프로젝트로 진행하고 있습니다.   
분산환경에서의 병령 처리와 실시간 검색을 지원하고 확장성이 뛰어납니다.   

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
➜  logs ps -ef | grep elasticsearch
  501 94506 93815   0  6:12PM ttys004    0:07.53 /elasticsearch-8.9.1/jdk.app/Contents/Home/bin/java -Xms4m -Xmx64m -XX:+UseSerialGC -Dcli.name=server -Dcli.script=./elasticsearch -Dcli.libs=lib/tools/server-cli -Des.path.home=/elasticsearch-8.9.1 -Des.path.conf=/elasticsearch-8.9.1/config -Des.distribution.type=tar -cp /elasticsearch-8.9.1/lib/*:/elasticsearch-8.9.1/lib/cli-launcher/* org.elasticsearch.launcher.CliToolLauncher
  501 94542 94506   0  6:12PM ttys004    0:25.96 /elasticsearch-8.9.1/jdk.app/Contents/Home/bin/java -Des.networkaddress.cache.ttl=60 -Des.networkaddress.cache.negative.ttl=10 -Djava.security.manager=allow -XX:+AlwaysPreTouch -Xss1m -Djava.awt.headless=true -Dfile.encoding=UTF-8 -Djna.nosys=true -XX:-OmitStackTraceInFastThrow -Dio.netty.noUnsafe=true -Dio.netty.noKeySetOptimization=true -Dio.netty.recycler.maxCapacityPerThread=0 -Dlog4j.shutdownHookEnabled=false -Dlog4j2.disable.jmx=true -Dlog4j2.formatMsgNoLookups=true -Djava.locale.providers=SPI,COMPAT --add-opens=java.base/java.io=org.elasticsearch.preallocate -XX:+UseG1GC -Djava.io.tmpdir=/var/folders/68/5xrtygkx6p14dr2rh4t1jc5h0000gn/T/elasticsearch-13955085521058029448 --add-modules=jdk.incubator.vector -XX:+HeapDumpOnOutOfMemoryError -XX:+ExitOnOutOfMemoryError -XX:HeapDumpPath=data -XX:ErrorFile=logs/hs_err_pid%p.log -Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,level,pid,tags:filecount=32,filesize=64m -Xms8192m -Xmx8192m -XX:MaxDirectMemorySize=4294967296 -XX:InitiatingHeapOccupancyPercent=30 -XX:G1ReservePercent=25 -Des.distribution.type=tar --module-path /elasticsearch-8.9.1/lib --add-modules=jdk.net --add-modules=org.elasticsearch.preallocate -m org.elasticsearch.server/org.elasticsearch.bootstrap.Elasticsearch
  501 94544 94542   0  6:12PM ttys004    0:00.01 /elasticsearch-8.9.1/modules/x-pack-ml/platform/darwin-aarch64/controller.app/Contents/MacOS/controller
```

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
시스템 구성에 맞춰 용량이 여유로운 다른 파티션에 경로를 지정해주는 것이 좋습니다.  

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

# http.port: 9200

# http.enabled: false
```
* path.data : 데이터 경로는 쉼표를 이용해서 여러 경로를 지정할 수 있습니다. 
* bootstrap.mlockall : true 로 설정하면 자바가상머신 에서 실행중인 엘라스틱서치가 점유하는 메모리를 고정(Lock) 합니다. 
이렇게 하면 가상머신이 엘라스틱서치가 사용하지 않는 메모리를 다른 자바 프로그램으로 돌리는(swap)것을 방지할 수 있습니다. 
* http.port : REST API 서비스를 하기 위해 엘라스틱서치는 9200~9299 범위이 HTTP 통신 포트를 사용합니다.  
새로운 엘라스틱노드를 실행하면 두번째 노드는 9201 포트로 실행됩니다.  
참고로, 엘라스틱 노드가 다른 노드와 바이딩 되어 데이터 교환을 위해 통신하는 포트는 9300 ~ 9399 범위의 HTTP 통신 포트를 사용합니다. 
* http.enabled : false 로 설정하면 해당 엘라스틱서치 노드는 REST API 통신을 하지 않습니다. 
클러스터링해서 운영하는 경우 하나의 노드만 REST API 통신을 허용하고 나머지 노드는 false 로 설정해서 데이터를 저장하는 용도로만 사용할 수 도 있습니다.  


### REST API 

엘라스틱 서치는 port 를 지정하지 않으면 기본값 9200을 사용합니다.  


설정 정보를 가져오는 Rest API 를 호출해보겠습니다.  

```bash
// https://localhost:9200/

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

Elastic 가이드 북 https://esbook.kimjmin.net/
