---
layout: post
title:  "Apache Kafka Basic"
series:
date: 2023-05-25 19:03:00 +0900
abstract: "Apache Kafka 시리즈 중 첫번째 글입니다. Kafka의 기본개념을 알아보고 설치 운영해봅시다."
tags: [Kafka]
image:
toc: true
categories: Tech
last_modified_at: 
---

kafka 를 잊어버린 미래의 나에게 kafka는 말이지~ 라고 알려주는 목표를 설정하고 글쓰기 하고있습니다.   
유투버에서 본 개발자 글쓰기를 할때 저렇게 설정하고 글을 써보라고 조언하시더군요.  
<br>
제가 있는 IOT 부서에서 디바이스(ex. 카메라, 도어락, 센서 등등) 데이터 수집을 Kafka 로 하고있습니다.   
Kafka 유료 온라인 강의를 시청하면서 정리를 해보았습니다. (돈이 아까우니!)  
<br>


![kafka logo]({{ site.url }}/assets/article_images/2023-05-25-Kafka/kafka_logo-simple.png)


* **Apache Kafka Basic ✓** <span class="series">SERIES 1/3</span>  
* [Apache Kafka Usage]({% post_url 2023-05-26-Kafka-Usage %}) <span class="series">SERIES 2/3</span>  



## Apache Kafka 의 기본 개념 및 이해 

Kafka는 Linkedin 내에서 개발하여 후에 아파치 재단([Kafka 공식사이트](https://kafka.apache.org/))에 2011년에 기부되어 오픈소스화 되었습니다.   
기존의 MQ(Messaging Platform) 에서 처리 불가능 이벤트 스트림 처리를 위해 개발 되었습니다.  
 
Kafka 라고 명명한 계기는 창시자인 Jay Kreps는 아래와 같이 말했습니다.  
<br>

![]({{ site.url }}/assets/article_images/2023-05-25-Kafka/quora-2023-05-25.png)  

구글번역  [원문 링크](https://www.quora.com/What-is-the-relation-between-Kafka-the-writer-and-Apache-Kafka-the-distributed-messaging-system/answer/Jay-Kreps)
```
카프카는 작가 이름을 쓰는 것에 최적화된 시스템이니까 말이 된다고 생각했다. 나는 대학에서 많은 조명 수업을 들었고 Franz Kafka를 좋아했습니다. 또한 오픈 소스 프로젝트의 이름이 멋지게 들렸습니다.  
따라서 기본적으로 관계가 많지 않습니다.  
```
Jay Kreps 이분은 2014년에 CONFLUENT 회사를 설립했습니다.



### Evnet란 무엇일까요?   

Kafka 를 사용하기로 했다면 Event 가 많이 발생하는 시스템이겠군요.   
Kafka 를 이야기하려면 Event 를 먼저 이야기해야 겠군요.  

- 은행 거래, 송금
- 웹사이트에서의 행위
- 온라인 마켓의 주문
- 카메라의 움직임 감지 


### Kafka 의 활용 사례를 무엇이 있을까요?
BigData가 발생하고 Event가 사용되는 모든곳이라고 할 수 있습니다. 

- IOT 디바이스로부터 데이터 수집 (제가 현재 있는 IOT부서에서 활용하는 사례입니다.)
- DB동기화
- Messaging System 
- 실시간 ETL
- Spark, Flink, Storm, Hadoop 과 같은 빅데이터 기술과 같이사용


## Topic, Partition, Segment 

### Topic 
Topic 은 Kafka 안에서 메시지가 저장되는 장소, 논리적인 표현입니다.   
<br>

### Partition
Partition 은 Commit Log, 하나의 Topic은 하나이상의 Partition으로 구성되어 있지요.    
병렬처리(Throughput 향상)를 위해서 다수의 Partition 사용하게 됩니다.    
Topic 생성시 Partition 개수를 지정합니다.     
개수변경 가능하나 운영시에는 변경 권장하지 않습니다.     
Partition 번호는 0부터 시작하고 오름차순입니다. 
Topic 내의 Partition 들은 서로독립적입니다. 

Offset 값은 계속증가하고 0으로 돌아가지 않습니다. 
Event(Message)의 순서는 하나의 Partition 내에서만 보장합니다. 그래서 순서가 보장되야 하는경우 Partition 개수를 1로 지정합니다.   
Partition에 저장된 데이터(Message)는 변경이 불가능(Immutable)합니다.   
Partition에 Write되는 데이터는 맨끝에 추가되어 저장됩니다.   
Partition은 Segment File들로 구성됩니다.   
Rolling 정책: log.segment.bytes(default 1 GB), log.roll.hours(default 168 hours)  

<br>
### Segment
Segment 는 메시지(데이터)가 저장되는 실제 물리 File 입니다.<br>
Segment File이 지정된 크기보다 크거나 지정된 기간보다 오래되면 새파일이 열리고 메시지는 새파일에 추가되게 동작합니다.   


## Kafka broker 

Kafka Server 라고 부르기도 합니다.  
모든 Kafka Broker는 Bootstrap(부트스트랩)서버라고 부릅니다.  
Kafka Cluster는 여러개의 Broker들로 구성됩니다.   
최소 3대이상의 Broker를 하나의 Cluster로 구성해야 하며 4대 이상을 권장합니다.  
Kafka broker 는 Topic 내의 Partition 들을 분산,유지 및 관리합니다.   
Broker 는 Partition 에 대한 Read 및 Write를 관리하는 소프트웨어입니다. 

각각의 Broker 들은 ID로 식별합니다. (단, ID는숫자)  

Topic 데이터의 일부분(Partition)을 갖을 뿐 데이터 전체를 갖고있지 않습니다.  
Client는 특정 Broker에 연결하면 전체 클러스터에 연결됩니다. (편리!)  
하지만,특정 Broker 장애를 대비하여, 전체 Broker List(IP, port)를 파라미터로 입력 권장합니다.   
각각의 Broker는 모든 Broker, Topic, Partition 에 대해알고있습니다. (Metadata)


## Zookeeper

Zookeeper는 Broker를 관리(Broker 들의 목록/설정을 관리)하는 소프트웨어입니다.   

Zookeeper 는 변경사항에 대해 Kafka 에 알립니다.   
ex) Topic 생성/제거, Broker 추가/제거 등등   

Zookeeper 없이는 Kafka가 작동 할 수 없습니다.   
(KIP-500 을 통해서 Zookeeper 제거가 진행중)  

Zookeeper 는 홀수의 서버로 작동하게 설계되어있습니다. (최소3, 권장5)  
Zookeeper 에는 Leader(writes) 가 있고 나머지 서버는 Follower(reads)입니다.   

<br>
Ensemble 은 Zookeeper 서버의 클러스터입니다.   
Quorum(쿼럼) 은 “정족수”이며, 합의체가 의사를 진행시키거나 의결을 하는데 필요한 최소한 인원수를뜻합니다.   
분산 코디네이션환경 에서 예상치 못한 장애가 발생해도 분산시스템의 일관성을 유지시키기 위해서 사용합니다.   
Ensemble이 3대로 구성되었다면 Quorum 은 2, 즉 Zookeeper 1대가 장애가 발생 하더라도 정상동작합니다.  



## Producer는

Producer는 메시지를 생산(Produce)해서 Kafka의 Topic으로 메시지를 보내는 애플리케이션입니다.   

Consumer Group는 Topic의 메시지를 사용하기 위해 협력하는 Consumer들의 집합입니다.  
하나의 Consumer는 하나의Consumer Group에 포함되며, Consumer Group내의 Consumer들은 협력하여 Topic의 메시지를 분산병렬 처리합니다.  

Producer와 Consumer는 서로 알지 못하며, Producer와 Consumer는 각각 고유의 속도로 Commit Log에 Write및 Read를 수행합니다. 



## Consumer 

Consumer는 Topic의 메시지를 가져와서 소비(Consume)하는 애플리케이션입니다.   

Consumer 는 각각 고유의 속도로 Commit Log 로부터 순서대로 Read(Poll) 를 수행합니다.   
다른 Consumer Group 에 속한 Consumer 들은 서로관련이없으며, Commit Log 에 있는 Event(Message)를 동시에 다른위치 에서 Read 할수있습니다.     


Consumer Offset 
  
- Consumer 가 자동이나 수동으로 데이터를 읽은위치를 commit하여 다시 읽음을 방지  
- __consumer_offsets 라는 Internal Topic 에서 Consumer Offset을 저장하여 관리  
- LOG-END-OFFSET: Producer 가 Write  
- CURRENT-OFFSET : Group의 Consumer가 Read하고 처리한후에 Commit  
- Cardinality : 특정 데이터 집합에서 유니크(Unique)한 값의 개수  
- Partition 전체에 Record를 고르게 배포하는 Key를 만드는것이 중요  



## Replication 

Broker에 장애가 발생하면 장애가 발생한 Broker의 Partition들은 모두 사용할 수 없게 되는 문제발생합니다.   
Partition을 복제(Replication)하여 다른 Broker상에서 복제물(Replicas)을 만들어서 장애를 미리대비합니다. 

Producer는 Leader 에만 Write하고 Consumer는 Leader로부터만 Read합니다. 
Follower는 Broker 장애시 안정성을 제공하기 위해서만 존재합니다. 
Follower는 Leader 의 Commit Log 에서 데이터를 가져오기 요청(Fetch Request)으로 복제합니다. 


Partition Leader에 대한 자동분산  
Hot Spot 방지

```bash
auto.leader.rebalance.enable : 기본값 enable
leader.imbalance.check.interval.seconds : 기본값 300 sec
leader.imbalance.per.broker.percentage : 기본값 10
```

위 설정값을 운영중인 서버 (server.properties 파일)에 설정이 없는걸로 보아 기본값이 적용되고 있습니다. 


## In-Sync Replicas(ISR)

Leader 장애시 Leader 를 선출하는데 사용합니다.   
Partition을 복제(Replication)하여 다른 Broker상에서 복제물(Replicas)을 만들어서 장애를 미리대비합니다.   
Replicas - Leader Partition, Follower Partition  
In-Sync Replicas(ISR)는 High Water Mark라고 하는 지점까지 동일한 Replicas (Leader와 Follower모두)의 목록입니다.  
Leader에 장애가 발생하면, ISR 중에서 새 Leader를 선출합니다.  

<br>
High Water Mark  
- 가장 최근에 Committed 메시지의 Offset 추적  
- replication-offset-checkpoint 파일에 체크포인트를 기록  

<br>
Leader Epoch  
- leader-epoch-checkpoint 파일에 체크포인트를 기록   

<br>
replica.lag.time.max.ms 으로 판단 해야합니다.    
Follower가Leader로 Fetch 요청을 보내는 Interval을 체크합니다.    

```bash
replica.lag.max.messages 
replica.lag.time.max.ms 
```