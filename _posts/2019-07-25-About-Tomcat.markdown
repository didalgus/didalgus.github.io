---
layout: post
title:  "About Tomcat"
date:   2019-07-25 15:00:00 +0900
abstract: ""
tags: [tomcat]
image:
toc: true
categories: WAS
last_modified_at: 2019-12-08T22:30:00+09:00
---

## 소개  

먼저 WAS(Web Application Server) 에 대해 알아볼까요?  
<br>

> 자바 플랫폼, 엔터프라이즈 에디션(Java Platform, Enterprise Edition; Java EE)은 자바를 이용한 서버측 개발을 위한 플랫폼이다.   
Java EE 플랫폼은 PC에서 동작하는 표준 플랫폼인 Java SE에 부가하여, 웹 애플리케이션 서버에서 동작하는 장애복구 및 분산 멀티티어를 제공하는 자바 소프트웨어의 기능을 추가한 서버를 위한 플랫폼이다.   
이전에는 J2EE라 불리었으나 버전 5.0 이후로 Java EE로 개칭되었다.  
이러한 Java EE 스펙에 따라 제품으로 구현한 것을 웹 애플리케이션 서버 또는 WAS라 불린다.  
출처 : [https://ko.wikipedia.org/wiki/자바_플랫폼,_엔터프라이즈_에디션](https://ko.wikipedia.org/wiki/자바_플랫폼,_엔터프라이즈_에디션)


![apache tomcat]({{ site.url }}/assets/article_images/2019-07-25-About-Tomcat/tomcat.png)


톰캣은 WAS(Web Application Serve) 에서도 서블릿 컨테이너만 있는 웹 어플리케이션 서버이지요.  
<br>

> This is the top-level entry point of the documentation bundle for the Apache Tomcat Servlet/JSP container.  
 Apache Tomcat version 8.5 implements the Servlet 3.1 and JavaServer Pages 2.3 specifications from the Java Community Process, and includes many additional features that make it a useful platform for developing and deploying web applications and web services.  
출처 : [https://tomcat.apache.org/tomcat-8.5-doc/index.html](https://tomcat.apache.org/tomcat-8.5-doc/index.html)



### 톰캣 디렉토리 구조

{:.table.table-key-value-60}

|directory name | desc |
|---|---|
|bin | 톰캣 서버 시작, 중지 명령(실행) 관련된 스크립트 파일 |
|conf | 환경 설정파일 |
|lib | 톰캣 서버의 java library (jar) |
|logs | 톰캣 서버 인스턴스 실행상태를 기록하는 로그 파일 |
|temp | 톰캣 서버 인스턴스 실행중 사용하는 임시 디렉토리 |
|webapps | 웹 어플리케이션 배포 디렉토리 (html, java) |
|work | JSP를 java servlet 소스(class)로 변환한 파일 (읽기전용) |


### 멀티 톰캣 운영

물리 서버 1대에 톰캣서버 2개 이상 띄워야 하는경우  
- 엔진 디렉토리 1개, 인스턴스 디렉토리 2개 로 나누어 띄우는 방법과  
- 톰캣 디렉토리 한개 더 copy 해서 server.xml 설정(ex. port) 변경해서 띄우는 방법과  
- [데몬으로 띄우는 방법](#jsvc)이 있답니다.  



## 로컬환경

개발자 로컬환경에 톰캣을 설치 해볼까요?  
<br>

제 개발환경(macOS)같은 경우  
- binary 파일 실행 방법과  
- homebrew 설치 후 실행 방법이 있지요.  
<br>  

binary 파일인 경우 다운로드 후 압출을 풀면 끝이랍니다. (참 쉽죵~?)  

### homebrew 설치

homebrew 설치 방법도 볼까요?    
<br>

homebrew야~ tomcat을 검색해다오.  

```bash
$ brew search tomcat
==> Formulae
tomcat    tomcat-native    tomcat@6    tomcat@7    tomcat@8 ✔
```

그래 버전은 8로 정했다!  

```bash
$ brew install tomcat@8

==> Caveats
tomcat@8 is keg-only, which means it was not symlinked into /usr/local/homebrew,
because this is an alternate version of another formula.

If you need to have tomcat@8 first in your PATH run:
  echo 'export PATH="/usr/local/homebrew/opt/tomcat@8/bin:$PATH"' >> ~/.bash_profile


To have launchd start tomcat@8 now and restart at login:
  brew services start tomcat@8
Or, if you dont want/need a background service you can just run :
  catalina run
==> Summary
🍺  /usr/local/homebrew/Cellar/tomcat@8/8.5.37: 641 files, 13.1MB, built in 7 seconds
```

잘 설치되었는지 확인해 볼까요?  
설치된 목록 확인 `list` 명령에  `--versions` 버전정보 옵션도 추가해보아요.

```bash
$ brew list --versions | grep tomcat
tomcat@8 8.5.38
```

### 톰캣서버 기동

homebrew 설치시 안내해준 방법으로 톰캣을 기동해보겠습니다.

```bash
$ cd /usr/local/homebrew/Cellar/tomcat\@8/8.5.38/bin
$  ./catalina start
Using CATALINA_BASE:   /usr/local/homebrew/Cellar/tomcat@8/8.5.38/libexec
Using CATALINA_HOME:   /usr/local/homebrew/Cellar/tomcat@8/8.5.38/libexec
Using CATALINA_TMPDIR: /usr/local/homebrew/Cellar/tomcat@8/8.5.38/libexec/temp
Using JRE_HOME:        /Users/didalgus/java
Using CLASSPATH:       /usr/local/homebrew/Cellar/tomcat@8/8.5.38/libexec/bin/bootstrap.jar:/usr/local/homebrew/Cellar/tomcat@8/8.5.38/libexec/bin/tomcat-juli.jar
Tomcat started.
```

CLI 에서 8080 포트가 잘 떴는지 확인~  

```bash
$ lsof -nP -i | grep LISTEN
java    14725   we   50u  IPv6 0x18c983bb654b427f    0t0  TCP *:8080 (LISTEN)
java    14725   we   55u  IPv6 0x18c983bb654b707f    0t0  TCP *:8009 (LISTEN)
```

<br>
브라우저에서도 확인해 볼께요.  
[http://localhost:8080/](http://localhost:8080/)    


### Configuration

그럼 설정 파일 `/conf/server.xml`을 들여다 보겠습니다.  
톰캣서버 port, 웹 소스 디렉토리, 로그파일 디렉토리 설정 등을 할수있지요.  
아무것도 수정하지 않는 경우는 기본값이랍니다.  

```xml
  <Service name="Catalina">
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />

    <Engine name="Catalina" defaultHost="localhost">
      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
      </Host>
    </Engine>
  </Service>
```


톰캣 버전 정보를 확인해 볼까요?  
개발자 환경에서는 상위 디렉토리에 버전 정보가 퐝! 하고 있어서 허무하긴 하지만요.  
운영 서버에서는 간략하게 `/usr/local/tomcat` 이렇게만 있는경우가 흔해요.  

```bash
$ /usr/local/tomcat/bin/version.sh
Using CATALINA_BASE:   /usr/local/tomcat
Using CATALINA_HOME:   /usr/local/tomcat
Using CATALINA_TMPDIR: /usr/local/tomcat/temp
Using JRE_HOME:        /usr/lib/jvm/java-8-oracle
Using CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
Server version: Apache Tomcat/8.5.23
...
```

### Manager

톰캣 서버에 서버 상태, 관리 콘솔, 호스트 관리 메뉴를 이용할 수 있어요.  
이 메뉴는 `webapps` 하위에서 확인 할 수 있어요.  

```bash
$ ls ~/apache-tomcat-8.5.41/webapps
ROOT/    docs/    examples/     host-manager/     manager/
```

브라우저에서 확인 해볼까요?  

![apache tomcat]({{ site.url }}/assets/article_images/2019-07-25-About-Tomcat/2019-08-01-tomcat-1.png)

`Server Status`, `Manager App` , `Host Manager` 버튼을 눌러볼까요?  
권한 설정 안내가 있군요.    

![apache tomcat]({{ site.url }}/assets/article_images/2019-07-25-About-Tomcat/2019-08-01-tomcat-2.png)


안내에 따라 `conf/tomcat-users.xml`에 권한을 추가합니다.

```xml
<role rolename="manager-gui"/>
<user username="manager" password="<must-be-changed>" roles="manager-gui"/>
```

톰캣 재시작 후 브라우저에서 `Server Status` 메뉴 로그인 해볼까요?  

![Server Status]({{ site.url }}/assets/article_images/2019-07-25-About-Tomcat/2019-08-01-tomcat-3.png)

`Manager App` 에서는 웹에서 어플리케이션 Start, Stop, Reload 등 할 수있네요. (편리!)  
![Manager App]({{ site.url }}/assets/article_images/2019-07-25-About-Tomcat/2019-08-01-tomcat-4.png)



## 운영환경

Prod 환경을 살펴보겠습니다. (장애, 너란 녀석;;)  
설정 확인을 위해 server.xml 파일을 살펴보니 기본 설정 그대로 쓰고 있네요.  
프로세스 리스트를 살펴보니 `jsvc` 로 실행했군요.  


### JSVC


>Jsvc is a set of libraries and applications for making Java applications run on UNIX more easily.  
Jsvc allows the application (e.g. Tomcat) to perform some privileged operations as root (e.g. bind to a port < 1024), and then switch identity to a non-privileged user.  
출처 : [Apache Commons - DAEMON - Jsvc](http://commons.apache.org/proper/commons-daemon/jsvc.html)


`jsvc`(Java based daemons or services)는 Native library로 톰캣을 데몬으로 띄워주는 역할을 하지요.  
톰캣 바이너리 버전을 다운받으면 기본으로 포함되어 있답니다.  
<br>


```bash
$ ps -ef | grep tomcat
tomcat   11 10  2 10:21 ? 00:00:58 jsvc.exec -java-home /usr/lib/jvm/java-8-oracle  
-user tomcat
-pidfile /home/willow/my-project/run/my-project.pid
-outfile /home/willow/my-project/logs/catalina-daemon.out
-Dspring.profiles.active=prod
-Djava.net.preferIPv4Stack=true
-Duser.timezone=Asia/Seoul
-Dcatalina.base=/home/willow/my-project
-Dcatalina.home=/usr/local/tomcat
...
```




>Tomcat can be run as a daemon using the jsvc tool from the commons-daemon project. Source tarballs for jsvc are included with the Tomcat binaries, and need to be compiled. Building jsvc requires a C ANSI compiler (such as GCC), GNU Autoconf, and a JDK.
Before running the script, the JAVA_HOME environment variable should be set to the base path of the JDK.   Alternately, when calling the ./configure script, the path of the JDK may be specified using the --with-java parameter, such as ./configure --with-java=/usr/java.  
Using the following commands should result in a compiled jsvc binary, located in the $CATALINA_HOME/bin folder. This assumes that GNU TAR is used, and that CATALINA_HOME is an environment variable pointing to the base path of the Tomcat installation.  
...
The file $CATALINA_HOME/bin/daemon.sh can be used as a template for starting Tomcat automatically at boot time from /etc/init.d with jsvc.  
출처 : [Apache Tomcat 8 - User Guide - Tomcat Setup](https://tomcat.apache.org/tomcat-8.5-doc/setup.html)


환경 변수에 `JAVA_HOME` 설정정보가 있는지 확인해볼까요?  

```bash
$ env | grep JAVA_HOME
JAVA_HOME=/usr/lib/jvm/java-8-oracle
```


톰캣 시작, 종료시 /bin/daemon.sh 를 사용한다고 하는군요.
```bash
$ ll /usr/local/tomcat/bin
...
-rwxr-x--- 1 tomcat tomcat   8004 Sep 28  2017 daemon.sh*
-rwxr-x--- 1 tomcat tomcat  21793 Sep 28  2017 catalina.sh*
-rwxr-xr-x 1 tomcat tomcat 192424 May 10 17:22 jsvc*
...
```


## WAR 배포  

### Gradle   

플러그인 `java`, [`war`](https://docs.gradle.org/current/userguide/war_plugin.html) 를 적용한 gradle 프로젝트를 build 해서 war 를 톰캣서버에 배포해볼까요?  

```bash
$ cat /source/sample/build.gradle

apply plugin: 'java'
apply plugin: 'war'
...
```

설정 확인후 빌드합니다.  
```bash
/source/sample $ ./gradlew clean build
...
BUILD SUCCESSFUL in 8s
```

gradle 빌드시 `build/libs` 위치에 war 파일생성 된답니다.

```bash
$ ll build/libs/
-rw-r--r--  1 willow  staff   8.0M 12  8 22:02 sample-1.0.war
```

생성된 war 파일을 톰캣 서버 webapps 디렉토리로 복사합니다.
<br>

참고로,  
`$TOMCAT_HOME/webapps`  : 웹 애플리케이션을 배치하는 디렉토리

```bash
$ mv build/libs/sample-1.0.war :/usr/local/apache-tomcat-8.5.15/webapps/
```

톰캣을 시작합니다.
```bash
$ cd $TOMCAT_HOME/bin
$ ./startup.sh      // 실행
$ ./shutdown.sh     // 종료
```


브라우저에서 확인해보아요.  
![http://localhost:8080/sample/]({{ site.url }}/assets/article_images/2019-07-25-About-Tomcat/2019-12-08-war.png)


sample/ 하위 디렉토리가 아닌 최상위 디렉토리에 적용하고 싶다고요?  
그럼~   
sample.war 가 아닌 ROOT.war 로 파일을 생성 한 후 webapps 디렉토리에 넣어주세요~ ^.~  

### Jenkins

CI 배포시 war 파일 이동 명령입니다.  
```bash
scp -r /var/lib/jenkins/workspace/project/build/libs/project-0.0.1-SNAPSHOT.war ssh계정@10.x.x.x:/opt/tomcat/latest/webapps/ROOT.war
```

```bash
commands:
  - mv target/*.war target/ROOT.war
```

```bash
- source: ROOT.war
destination: /var/lib/tomcat8/webapps
```
