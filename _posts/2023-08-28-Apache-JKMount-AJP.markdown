---
layout: post
title:  "Apache"
date: 2023-08-28 16:30:00 +0900
abstract: "Apache"
tags: [Apache]
image:
toc: true
categories: Tech
last_modified_at: 
published: true
---

Apache 웹서버를 Nginx 웹서버로 이전해야 하는 일이 생겼습니다.   
Apach AJP 와 JkMount 를 이용하고 있어 설정을 확인하면서 겸사 겸사 정리해보았습니다. 

## Server Spec.

```bash
$ /apache-2.4.39/bin/apachectl -v
Server version: Apache/2.4.39 (Unix)

$ cat /etc/redhat-release
CentOS Linux release 7.4.1708 (Core)

$ arch
x86_64
```


## Process 

프로세스 리스트를 확인합니다.   
```bash
$ ps -ef | grep httpd
root    8662     1  0 Oct04 ?        00:02:37 /apache-2.4.39/bin/httpd -k start
tree   10083  8662  0 Oct04 ?        00:12:17 /apache-2.4.39/bin/httpd -k start
tree   10084  8662  0 Oct04 ?        00:09:56 /apache-2.4.39/bin/httpd -k start
tree   10085  8662  0 Oct04 ?        00:10:14 /apache-2.4.39/bin/httpd -k start
tree   10102  8662  0 Oct04 ?        00:10:33 /apache-2.4.39/bin/httpd -k start
tree   10145  8662  0 Oct04 ?        00:36:37 /apache-2.4.39/bin/httpd -k start
```


port 사용 리스트를 확인합니다. 
```bash
$ sudo netstat -lntp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address     Foreign Address   State     PID/Program name    
tcp6       0      0 :::80             :::*              LISTEN    8662/httpd     // apache http 
tcp6       0      0 :::443            :::*              LISTEN    8662/httpd     // apache https 

tcp6       0      0 127.0.0.1:5001    :::*              LISTEN    26721/java     // tomcat : tree_1 
tcp6       0      0 :::9001           :::*              LISTEN    26721/java 	 // tomcat : tree_1 (HTTP/1.1)
tcp6       0      0 :::6001           :::*              LISTEN    26721/java     // tomcat : ajp worker - tree_1 (AJP/1.3)

tcp6       0      0 127.0.0.1:5002    :::*              LISTEN    15366/java    // tomcat : tree_2  
tcp6       0      0 :::9002           :::*              LISTEN    15366/java   	// tomcat : tree_2 (HTTP/1.1)
tcp6       0      0 :::6002           :::*              LISTEN    15366/java    // tomcat : ajp worker - tree_2 (AJP/1.3)
```

## Configuration

80 port 2개, 443 port 1개 설정이 되어 있군요. (실서비스는 더 복잡합니다)

```bash
$ /apache-2.4.39/bin/apachectl -t -D DUMP_VHOSTS
VirtualHost configuration:
*:80                   is a NameVirtualHost
        default server tree_1.com (/apache-2.4.39/conf/httpd.conf:629)
        port 80 namevhost tree_1.com (/apache-2.4.39/conf/httpd.conf:629)
                alias tree_a.com
        port 80 namevhost tree_2.com (/apache-2.4.39/conf/httpd.conf:649)
                alias tree_2.com
*:443                  tree_2.com (/apache-2.4.39/conf/httpd.conf:712)
```

아파치 설정이 궁금하시면 https://httpd.apache.org/docs/2.4/ 공식 사이트에 매뉴얼에서 확인 하시면 됩니다. 

## JkMount

conf 설정 중 1개 가상호스트건을 보겠습니다.  
정적 리소스(css, js, html)의 경우 Apache 가 처리하게 설정하였습니다. 

그리고, L7 Heath Check 정적파일(/common/monitor/ok.html)도 Apache 에서 호출하도록 설정하였습니다.  

```bash
$ /apache-2.4.39/conf/httpd.conf
<VirtualHost *:80>
        DocumentRoot html/tree_1
        ServerName tree_1.com
        ServerAlias tree_a.com

        JkMount /* tree_1

        JkUnMount /*.css tree_1
        JkUnMount /*.js tree_1
        JkUnMount /*.html tree_1

        JkMount /common/monitor/ok.html tree_1

        SetEnvIfNoCase Request_URI "\.(ico|gif|jpg|swf|png|css|js)$" nolog-request
        ErrorLog "| /apache-2.4.39/bin/rotatelogs -l /apache-2.4.39/logs/error_tree_1.log.%Y%m%d 86400"
        CustomLog "| /apache-2.4.39/bin/rotatelogs -l /apache-2.4.39/logs/access_tree_1.log.%Y%m%d 86400" combined env=!nolog-request
</VirtualHost>
```

* JkMount : JkMount를 이용해 worker를 등록 
* JkUnMount : 특정 url 패턴에서 톰캣의 연동을 해제, 톰캣이 아닌 아파치로 이동할 경로


위에서 JkMount 를 이용해서 worker를 등록한다고 하였는데요. 그렇다면 worker 설정파일을 확인해야겠네요.  
다수의 worker 가 있는경우 `worker.list`에 쉼표를 이용해서 쭉 기재해줍니다.   
각 워커의 설정은 그 아래 단락에 이어서 설정합니다.  


```bash
$ vi /apache-2.4.39/conf/workers.properties 

worker.list=tree_1,tree_2

worker.tree_1.type=ajp13
worker.tree_1.port=6001
#worker.tree_1.connect_timeout=1000
#worker.tree_1.prepost_timeout=1000
worker.tree_1.socket_timeout=10
worker.tree_1.connection_pool_timeout=10
#worker.tree_1.reply_timeout=1000

worker.tree_2.type=ajp13
worker.tree_2.port=6002
worker.tree_2.socket_timeout=10
worker.tree_2.connection_pool_timeout=10
```


## Tomcat 

1개의 톰캣에 여러개의 WAS를 띄우는 경우 conf 설정을 달리해서 `-config` 옵션을 이용해서 구동합니다.    
  
샘플 예시입니다. (구동 옵션 설정이 길어서 생략했습니다.) 
```bash
$ ps -ef | grep java
tree   10302     1  0 Jun28 ?        02:54:39 /jdk/bin/java -Djava.util.logging.config.file=/tree_1/conf/logging.properties  -config /conf/tomcat/tree_1/server.xml start
```


설정파일 server.xml 을 살펴볼까요?  
protocol AJP/1.3 설정에 사용하는 Connector port="6001" 가 보이는 군요.   
```xml
$ vi /conf/tomcat/tree_1/server.xml

<Server port="5001" shutdown="SHUTDOWN">
        <Service name="Catalina">
                <Connector port="9001" protocol="HTTP/1.1" connectionTimeout="20000" URIEncoding="UTF-8" redirectport="8443"/>
                <Connector port="6001" enableLookups="false" redirectPort="8443" protocol="AJP/1.3" URIEncoding="UTF-8" />
                <Engine name="Catalina" defaultHost="localhost">
                        <Host workDir="work/tree_1" name="localhost" debug="0" appBase="webapps" unpackWARs="true">
                                <Context docBase="/html/tree_1" path="">
                                        <Logger className="org.apache.catalina.logger.SystemOutLogger" verbosity="4" timestamp="true" />
                                </Context>
                        </Host>
                </Engine>
        </Service>
</Server>
```



## Apache Start, Stop 

아파치 구동, 종료 명령어를 알아볼까요? 
```bash
$ /apache-2.4.39/bin/apachectl start
$ /apache-2.4.39/bin/apachectl stop
```

아파치 종료 없이 설정만 리로드 하는 명령입니다. nginx 에서의 reload 와 동일합니다. 
```bash
$ /apache-2.4.39/bin/apachectl -k graceful
```

아파치 설정파일 문법 체크 명령입니다. nginx 도 동일하게 `-t` 입니다. 
```bash
$ /apache-2.4.39/bin/apachectl -t
Syntax OK
```

설치된 모듈 리스트입니다.  
```bash
$ /apache-2.4.39/bin/httpd -l
Compiled in modules:
core.c
mod_so.c
http_core.c
```