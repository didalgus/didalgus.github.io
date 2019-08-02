---
layout: post
title:  "About Jboss"
date:   2019-08-02 12:00:00 +0900
abstract: ""
tags: [jboss]
image:
toc: true
categories: WAS
---

![JBoss AS 7]({{ site.url }}/assets/article_images/2019-08-02-About-Jboss/as7_logo.png)

## 소개  

Jboss 관련 포스팅 업데이트 중입니다.  
<br>

지난 포스팅에서 [톰캣](/2019/07/25/About-Tomcat.html)은 웹관련 부분(Servlet/JSP)만 구현한 웹 컨테이너라고 했지요.  

Jboss(Java Beans Open Source Software) 는 오픈소스로 Java EE 스펙에 따라 구현한 웹 어플리케이션 서버입니다.  
2006년에 RedHat 에서 인수하면서  
상용 `RedHat Jboss Enterprise (EAP)`, 무료 `JBoss AS (JBoss Application Server)` 로 나뉘게 되었습니다.  
<br>
레드헷에서는 제이보스 오픈소스 개발을 유지하면서 상용 제품을 출시하였습니다.   
이름의 혼동을 줄이고자 JBoss AS8(무료 버전) 이름을 Wildfly8 로 변경하였습니다.  
무료와 상용은 소스코드상 동일한 수준이며 차이는 벤더의 유료 기술지원여부 라고 하네요.

<br>

무료버전 `jboss-as-7.1.1.Final`운영하면서 몇가지 정리해보았습니다.  





## Version
오래된 서비스인지라 java 버전이 7이군요.  

```bash
$ cd /jboss-as-7.1.1.Final/bin
$ ./standalone.sh --version
=========================================================================

  JBoss Bootstrap Environment

  JBOSS_HOME: /usr/local/jboss-as

  JAVA: /usr/local/jdk1.7.0_51/bin/java

  JAVA_OPTS:  -server -XX:+UseCompressedOops -XX:+TieredCompilation -Xms512m -Xmx1024m -XX:PermSize=256m -XX:MaxPermSize=512m -Djava.net.preferIPv4Stack=true -Dorg.jboss.resolver.warning=true -Dsun.rmi.dgc.client.gcInterval=3600000 -Dsun.rmi.dgc.server.gcInterval=3600000 -Djboss.modules.system.pkgs=org.jboss.byteman -Djava.awt.headless=true -Djboss.server.default.config=standalone.xml -Dspring.profiles.active=dev

=========================================================================

15:02:08,351 정보    [org.jboss.modules] JBoss Modules version 1.1.1.GA
JBoss AS 7.1.1.Final "Brontes"

```


## Port

물리서버 1대에 여러개 WAS 를 띄울려면 port를 변경해야겠지요.  

2가지 방법이 있습니다.  
<br>

첫번째는 `standalone.xml` 설정파일에 `port-offset` 수치를 입력해서 일괄 반영합니다.  
기본 포트에 port-offset 이 더해지는 거지요.  
ex) 8080 + 10000 = 18080


```xml
<socket-binding-group name="standard-sockets" default-interface="public" port-offset="${jboss.socket.binding.port-offset:10000}">
    <socket-binding name="management-native" interface="management" port="${jboss.management.native.port:9999}"/>
    <socket-binding name="management-http" interface="management" port="${jboss.management.http.port:9990}"/>
    <socket-binding name="management-https" interface="management" port="${jboss.management.https.port:9443}"/>
    <socket-binding name="ajp" port="8009"/>
    <socket-binding name="http" port="8080"/>
    <socket-binding name="https" port="8443"/>
    <socket-binding name="osgi-http" interface="management" port="8090"/>
    <socket-binding name="remoting" port="4447"/>
    <socket-binding name="txn-recovery-environment" port="4712"/>
    <socket-binding name="txn-status-manager" port="4713"/>
    <outbound-socket-binding name="mail-smtp">
        <remote-destination host="172.xx.xx.xx" port="25"/>
    </outbound-socket-binding>
</socket-binding-group>
```


두번째는 `standalone.xml` 설정파일에 `socket-binding` 포트를 개별 지정하는것이지요.  
운영상황에 따라 필요하신 방법을 적용하시면 된답니다.  
<br>

port-offset 값은 0 입니다.
http, https 기본값을 변경하였군요.  

```xml
<socket-binding-group name="standard-sockets" default-interface="public" port-offset="${jboss.socket.binding.port-offset:0}">
    <socket-binding name="management-native" interface="management" port="${jboss.management.native.port:19999}"/>
    <socket-binding name="management-http" interface="management" port="${jboss.management.http.port:19990}"/>
    <socket-binding name="management-https" interface="management" port="${jboss.management.https.port:19443}"/>
    <socket-binding name="ajp" port="18009"/>
    <socket-binding name="http" port="18080"/>
    <socket-binding name="https" port="18443"/>
    <socket-binding name="osgi-http" interface="management" port="18090"/>
    <socket-binding name="remoting" port="14447"/>
    <socket-binding name="txn-recovery-environment" port="14712"/>
    <socket-binding name="txn-status-manager" port="14713"/>
    <outbound-socket-binding name="mail-smtp">
        <remote-destination host="localhost" port="125"/>
    </outbound-socket-binding>
</socket-binding-group>
```

### Port 변경 후 적용 안되는 경우

운영중이던 서비스의 port 를 변경하고 jboss 재시작 하였으나 적용되지 않을 때는  

`/jboss-as-7.1.1.Final/standalone/configuration/standalone_xml_history` 디렉토리 하위 파일을 삭제해 주세요.  
재시작할때 과거 기록을 참고하여 `standalone.xml`을 재 작성하는 경우 였습니다.  
<br>

로그파일 및 가상파일도 같이 정리해 준다면 디스크 사용 효율이 높아지겠지요?  
한번은 Jboss 압축파일을 전달받았는데 파일용량이 5G 이상이길래 확인해봤더니 vfs 파일때문이였습니다.  
로컬 개발시 배포, 재시작이 잦은터라 vfs 파일이 시간이 지남에 따라 눈덩이처럼 커진거지요.  

```bash
rm -rf /jboss-as-7.1.1.Final/standalone/configuration/standalone.xml/*
rm -f /jboss-as-7.1.1.Final/standalone/log/*
rm -rf /jboss-as-7.1.1.Final/standalone/tmp/vfs/*
```
