---
layout: post
title:  "Apache Kafka Usage"
series: 2/2
date: 2023-05-26 19:03:00 +0900
abstract: "Apache Kafka 시리즈 중 두번째 글입니다. Kafka 설치하고 Producer과 Consumer를 알아봅시다."
tags: [Kafka]
image:
toc: true
categories: Tech
last_modified_at: 
---

kafka 를 잊어버린 미래의 나에게 이렇게 설치하고 이렇게 쓰는거야~ 라고 알려주는 목표를 설정하고 글쓰기 하고있습니다.   
유툽에서 개발자 글쓰기를 할때 저렇게 설정하고 글을 써보라고 조언하시더군요.  
<br>

![kafka logo]({{ site.url }}/assets/article_images/2023-05-25-Kafka/kafka_logo-simple.png)

* [Apache Kafka Usage]({% post_url 2023-05-25-Kafka-Basic %}) <span class="series">SERIES 1/2</span>    
* **Apache Kafka Usage ✓** <span class="series">SERIES 2/2</span>    


### kafka 다운로드 & 설치

다운로드 받아봅시다. 
https://kafka.apache.org/downloads

2023년 2월 7일자 소스버전은 3.4.0가 최신이군요.   
맥 환경인지라 Binary 버전 2.13 으로 받아서 설치해보겠습니다. 

Scala 2.13 https://archive.apache.org/dist/kafka/2.8.2/kafka_2.13-2.8.2.tgz 

apps 디렉토리에 압축파일을 풀어줍니다. (명령어로든 더블클릭이로든~)  

```bash
$ mkdir ~/apps
$ cd apps 
```

접근 용이하게 심볼릭 링크를 걸어주겠습니다.
```bash
ln -s kafka_2.13-2.8.2 kafka
```

참 쉽죵?  
```bash
$ ll apps 
lrwxr-xr-x 1 willow staff 16B 1 5 17:38 kafka@ -> kafka_2.13-2.8.2
drwxr-xr-x@ 10 willow staff 320B 1 5 17:43 kafka_2.13-2.8.2/
```

### zookeeper 설정 & 구동

zookeeper data 가 저장될 디렉토리를 지정해줍니다.

```bash
$ vi ~/apps/kafka/config/zookeeper.properties 
# the directory where the snapshot is stored.
dataDir=/tmp/zookeeper
# the port at which the clients will connect
clientPort=2181
```

zookeeper를 구동해보겠습니다. 2181 포트로 구동되는군요.  
```bash
$ ~/apps/kafka/bin/zookeeper-server-start.sh ~/apps/kafka/config/zookeeper.properties 
= willow ~/apps/kafka/bin  $ ./zookeeper-server-start.sh ../config/zookeeper.properties 
...
[2023-05-25 17:43:13,562] INFO clientPortAddress is 0.0.0.0:2181 (org.apache.zookeeper.server.quorum.QuorumPeerConfig)
...
[2023-05-25 17:43:13,644] INFO binding to port 0.0.0.0/0.0.0.0:2181 (org.apache.zookeeper.server.NIOServerCnxnFactory)
```

zookeeper가 정상 구동되었는지 확인해보겠습니다. 
```bash
$ telnet 0 2181
Trying 0.0.0.0...
Connected to 0.
Escape character is '^]'.
```

  
zookeeper 쉘을 실행해볼까요?   
```bash
$ bin/zookeeper-shell.sh localhost:2181 
Connecting to localhost:2181
Welcome to ZooKeeper!
JLine support is disabled

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
ls /
[zookeeper]
```

zookeeper 로그를 확인보겠습니다.  
위에서 설정한 zookeeper.properties 경로에 로그가 생성된것을 확인했습니다. 
```bash
$ ll /tmp/zookeeper/version-2
total 48
-rw-r--r--  1 willow  wheel    64M  1  5 17:47 log.1
-rw-r--r--  1 willow  wheel   424B  1  5 17:43 snapshot.0
```

### kafka 설정 & 구동

kafka 설정 파일을 열어보니 9092 포트가 선언되어 있습니다. 

```bash
$ vi ~/apps/kafka/config/server.properties

listeners=PLAINTEXT://127.0.0.1:9092
```

kafka(broker) 서버를 실행합니다.
id = 0 서버가 구동되었네요. 
```bash
$ ~/apps/kafka/bin/kafka-server-start.sh config/server.properties

...
[2023-05-25 17:16:35,827] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)
```


## Topic 

Topic은 논리적인 표현으로 Kafka에서 메시지가 저장되는 장소 입니다. 



Topic 네이밍이 중요해서 찾아보았습니다.   
운영중에 변경하기 어렵기때문에 생성시 알기쉽게 네이밍하는게 중요하더군요.   
public 하게 명명하면 운영중 유지보수시 헷갈릴 수 있답니다. ㅠ   
현 IOT부서에서는 _(언더바)를 권장하지 않아서 `SensorEvent` 이런식으로 파스칼 케이스(pascal case) 표기명을 사용하고 있습니다.   

토픽 네임 규칙  
- 최대 249자   
- 공백허용 X
- _. 권장하지 않음 

깃헙 소스 내 정의된 규칙은 아래와 같군요.   https://github.com/apache/kafka/blob/trunk/clients/src/main/java/org/apache/kafka/common/internals/Topic.java

```java
public static final String LEGAL_CHARS = "[a-zA-Z0-9._-]";
private static final int MAX_NAME_LENGTH = 249;
```


topic를 생성해보겠습니다.

```bash
$ ~/apps/kafka/bin/kafka-topics.sh --create --topic willow --bootstrap-server localhost:9092
Created topic willow.
```

생성된 topic 를 확인해봅니다. 
```bash
$ ~/apps/kafka/bin/kafka-topics.sh --list --bootstrap-server localhost:9092
willow
```

## producer & consumer 





topic을 생성해보았으니 프로듀서와 컨슈머를 사용해보겠습니다.

producer 
```bash
$ ~/apps/kafka/bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic willow 
>11
>22
```

consumer 
```bash
$ ~/apps/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic willow                  
11
22

$ ~/apps/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic willow --from-beginning
11
22
```

topics 토픽 확인
```bash
$ ~/apps/kafka/bin/zookeeper-shell.sh localhost:2181
Connecting to localhost:2181
Welcome to ZooKeeper!
JLine support is disabled

WATCHER::

WatchedEvent state:SyncConnected type:None path:null

ls /
[admin, brokers, cluster, config, consumers, controller, controller_epoch, feature, isr_change_notification, latest_producer_id_block, log_dir_event_notification, zookeeper]
ls /brokers
[ids, seqid, topics]
ls /brokers/ids
[0]
ls /brokers/topics
[__consumer_offsets, willow, greenpine]


```

## 로그 확인

로그 디렉토리를 살펴볼까요?

```bash
$ ll ~/apps/kafka/tmp/kafka-logs/           
total 32
-rw-r--r--  1 willow  wheel     0B  1  6 17:16 cleaner-offset-checkpoint
drwxr-xr-x  6 willow  wheel   192B  1  6 17:22 willow-0/
drwxr-xr-x  6 willow  wheel   192B  1  6 17:29 greenpine-0/
-rw-r--r--  1 willow  wheel     4B  1  6 17:30 log-start-offset-checkpoint
-rw-r--r--  1 willow  wheel    88B  1  6 17:16 meta.properties
-rw-r--r--  1 willow  wheel    33B  1  6 17:30 recovery-point-offset-checkpoint
-rw-r--r--  1 willow  wheel    33B  1  6 17:30 replication-offset-checkpoint

$ ll ~/apps/kafka/tmp/kafka-logs/willow-0
total 16
-rw-r--r--  1 willow  wheel    10M  1  6 17:29 00000000000000000000.index
-rw-r--r--  1 willow  wheel   302B  1  6 17:29 00000000000000000000.log
-rw-r--r--  1 willow  wheel    10M  1  6 17:29 00000000000000000000.timeindex
-rw-r--r--  1 willow  wheel     8B  1  6 17:29 leader-epoch-checkpoint

$ cat ~/apps/kafka/tmp/kafka-logs/willow-0/00000000000000000000.log
```
