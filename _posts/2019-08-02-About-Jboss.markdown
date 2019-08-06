---
layout: post
title:  "About Jboss"
date:   2019-08-02 12:00:00 +0900
abstract: ""
tags: [jboss, WAS, web application server]
image:
toc: true
categories: WAS
---

![JBoss AS 7]({{ site.url }}/assets/article_images/2019-08-02-About-Jboss/as7_logo.png)

## 소개  

지난 포스팅에서 [톰캣](/2019/07/25/About-Tomcat.html)은 웹관련 부분(Servlet/JSP)만 구현한 웹 컨테이너라고 했지요.  

Jboss(Java Beans Open Source Software) 는 오픈소스로 Java EE 스펙에 따라 구현한 웹 어플리케이션 서버입니다.  
2006년에 RedHat 에서 인수하면서  
상용 `RedHat Jboss Enterprise (EAP)`, 무료 `JBoss AS (JBoss Application Server)` 로 나뉘게 되었습니다.  
<br>
레드헷에서는 제이보스 오픈소스 개발을 유지하면서 상용 제품을 출시하였습니다.   
이름의 혼동을 줄이고자 JBoss AS8(무료 버전) 이름을 **Wildfly8** 로 변경하였습니다.  
무료와 상용은 소스코드상 동일한 수준이며 차이는 벤더의 유료 기술지원여부 라고 하네요.

![JBoss AS 7]({{ site.url }}/assets/article_images/2019-08-02-About-Jboss/2019-08-05-jboss-download.png)

[Download Page](https://jbossas.jboss.org/downloads)에 더이상 유지보수 되지않으며 지원하지 않는다는 안내가 있네요.  
저 역시 <u>WildFly 사용</u>을 권해드립니다.  

이 포스팅은 `jboss-as-7.1.1.Final` 버전을 운영하면서 정리한 것들을 모은 내용입니다.  

## Version

오래된 서비스인지라 java 버전이 7이군요.  

>JBoss AS 7 requires JDK 1.6 or later.  
AS7 won't work on JDK8. for that use WildFly 8.x or newer.  
You can use JDK7 with AS7.

로컬 개발환경 java8에서 동작하지 않기에 찾아보니 1.6 ~ 1.7 버전에서 동작하네요.  

{% highlight bash linenos %}
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
{% endhighlight %}

## Start, Stop

### start

환경변수는 설정해도 되고 안해도 되지요.  
저는 `~/.profile`에 선언해두었습니다.  

{% highlight bash linenos %}
export JBOSS_HOME=/usr/local/jboss-as-7.1.1.Final
export JAVA_HOME=~/java      
export PATH=$PATH:$JAVA_HOME/bin:$JBOSS_HOME/bin
{% endhighlight %}

콘솔 로그를 볼려면 첫번째 명령어를 쓰시고, 콘솔 로그 확인이 필요없는 경우 2번째 명령어를 사용하세요.

{% highlight bash linenos %}
$ ./standalone.sh &

$ $JBOSS_HOME/bin/standalone.sh > /dev/null 2>&1 &
{% endhighlight %}

운영중인 서버에 8080port 잘 떠있는지 확인해봅니다.


운영중인 서버중에 재기동은 안되고 기본 정보를 확인 하고 싶을때 있잔아요.  
그럴땐 `./standalone/log/boot.log` 파일을 살펴보세요.
```log
54     jboss.server.base.dir = /Users/we/Documents/jboss/jboss_evaluation/standalone
55     jboss.server.config.dir = /Users/we/Documents/jboss/jboss_evaluation/standalone/configuration
56     jboss.server.data.dir = /Users/we/Documents/jboss/jboss_evaluation/standalone/data
57     jboss.server.deploy.dir = /Users/we/Documents/jboss/jboss_evaluation/standalone/data/content
58     jboss.server.log.dir = /Users/we/Documents/jboss/jboss_evaluation/standalone/log
59     jboss.server.name = wmp-2017060003
60     jboss.server.temp.dir = /Users/we/Documents/jboss/jboss_evaluation/standalone/tmp
```

### Stop

WAS 를 중지하실때 간단하게 kill 하기도 하지만 그런경우 로그가 남지 않지요.

{% highlight bash linenos %}
$ cd /usr/local/jboss-as-7.1.1.Final/bin
$ ./jboss-cli.sh --connect command=:shutdown

$ $JBOSS_HOME/bin/jboss-cli.sh --connect --command=:shutdown
{% endhighlight %}

## Deployments

배포를 해볼까요?
배포 하실때 2가지 방법을 사용 하실 수 있지요.

### Web Deployment

먼저, 관리자 등록을 해야겠네요.  

{% highlight bash linenos %}
$ ./add-user.sh

What type of user do you wish to add?
 a) Management User (mgmt-users.properties)
 b) Application User (application-users.properties)
(a): a

Enter the details of the new user to add.
Realm (ManagementRealm) :
Username : willow
Password :
Re-enter Password :
About to add user 'willow' for realm 'ManagementRealm'
Is this correct yes/no? yes
Added user 'willow' to file '/usr/local/jboss-as-7.1.1.Final/standalone/configuration/mgmt-users.properties'
Added user 'willow' to file '/usr/local/jboss-as-7.1.1.Final/domain/configuration/mgmt-users.properties'
{% endhighlight %}

war 파일을 Jboss 배포 위치에 옮기고 .deployed 파일을 생성해주세요.

{% highlight bash linenos %}
$ mv ./sample-0.0.1-SNAPSHOT.war /jboss-as-7.1.1.Final/standalone/deployments/
$ touch sample-0.0.1-SNAPSHOT.war.deployed
{% endhighlight %}

`configuration/standalone.xml` 파일에 `enable-welcome-root="false"` 설정을 확인해주세요.

{% highlight bash linenos %}
<virtual-server name="default-host" enable-welcome-root="false">
    <alias name="localhost"/>
</virtual-server>
{% endhighlight %}


웹콘솔 화면이예요.  
![JBoss AS 7]({{ site.url }}/assets/article_images/2019-08-02-About-Jboss/jboss-admin.png)

그럼 jboss 를 구동하겠습니다.

{% highlight bash linenos %}
$ ./standalone.sh &
    23:07:38,105 INFO  [org.jboss.web] (MSC service thread 1-5) JBAS018210: Registering web context: /srping-training
    23:07:38,118 INFO  [org.jboss.as.server] (HttpManagementService-threads - 4) JBAS018559: Deployed "srping-training.war"
{% endhighlight %}

브라우저에서 확인~  
http://localhost:8080/srping-training/


### CLI Deployment


[JBoss Web Web Application Deployment](https://docs.jboss.org/jbossweb/7.0.x/deployer-howto.html) 내용을 가져왔습니다.  



>Deploying using the command line  <br>  
Use the command line: ${jboss.server.base.dir}/bin/jboss-admin.sh For example:  
```bash
bin/jboss-admin.sh
You are disconnected at the moment. Type 'connect' to connect to the server or 'help' for the list of supported commands.
[disconnected /] connect
Connected to standalone controller at localhost:9999
[standalone@localhost:9999 /] deploy /home/jfclere/jbossweb_sandbox/webapps/myapp.war
'myapp.war' deployed successfully.
```
To undeploy:  
```bash
[standalone@localhost:9999 /] undeploy myapp.war
Successfully undeployed myapp.war.
```

## 변수

어플리케이션 변수를 지정하는 방법에는 2가지가 있지요.  
jboss 설정파일에 적용하는 방법과 구동시 인자로 넘기는 방법이 있어요.  

### conf
`bin/standalone.conf` 파일에 `-Dprofiles.active=local` 라인과 같이 추가해 주세요.

{% highlight bash linenos %}
 if [ "x$JAVA_OPTS" = "x" ]; then
   JAVA_OPTS="-Xms512m -Xmx1024m -XX:PermSize=256m -XX:MaxPermSize=512m -Djava.net.preferIPv4Stack=true -Dorg.jboss.resolver.warning=true -Dsun.rmi.dgc.client.gcInterval=3600000 -Dsun.rmi.dgc.server.gcInterval=3600000"
   JAVA_OPTS="$JAVA_OPTS -Djboss.modules.system.pkgs=$JBOSS_MODULES_SYSTEM_PKGS -Djava.awt.headless=true"
   JAVA_OPTS="$JAVA_OPTS -Djboss.server.default.config=standalone.xml"

   JAVA_OPTS="$JAVA_OPTS -Dprofiles.active=local"

else
   echo "JAVA_OPTS already set in environment; overriding default settings with values: $JAVA_OPTS"
fi
{% endhighlight %}

ps 명령어로 보니 잘 적용되었군요.

{% highlight bash linenos %}
$ ps -ef | grep java
  501  7279  7252   0 11:29AM ttys001    0:06.73 /Users/willow/java/bin/java -D[Standalone] -server -XX:+UseCompressedOops -XX:+TieredCompilation -Xms64m -Xmx512m -XX:MaxPermSize=256m -Djava.net.preferIPv4Stack=true -Dorg.jboss.resolver.warning=true -Dsun.rmi.dgc.client.gcInterval=3600000 -Dsun.rmi.dgc.server.gcInterval=3600000 -Djboss.modules.system.pkgs=org.jboss.byteman -Djava.awt.headless=true -Djboss.server.default.config=standalone.xml
  -Dspring.profiles.active=local
  -Dorg.jboss.boot.log.file=/usr/local/jboss-as-7.1.1.Final/standalone/log/boot.log -Dlogging.configuration=file:/usr/local/jboss-as-7.1.1.Final/standalone/configuration/logging.properties -jar /usr/local/jboss-as-7.1.1.Final/jboss-modules.jar -mp /usr/local/jboss-as-7.1.1.Final/modules -jaxpmodule javax.xml.jaxp-provider org.jboss.as.standalone -Djboss.home.dir=/usr/local/jboss-as-7.1.1.Final
{% endhighlight %}

### 인자
구동시 인자로 넘겨볼까요?

```bash
$ ./standalone.sh -Dprofiles.active=local &
```

## Port

물리서버 1대에 여러개 WAS 를 띄울려면 port를 변경해야겠지요.  

2가지 방법이 있습니다.  

첫번째는 `standalone.xml` 설정파일에 `port-offset` 수치를 입력해서 일괄 반영합니다.  
기본 포트에 port-offset 이 더해지는 거지요.  
ex) 8080 + 10000 = 18080


{% highlight bash linenos %}
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
{% endhighlight %}


두번째는 `standalone.xml` 설정파일에 `socket-binding` 포트를 개별 지정하는것이지요.  
운영상황에 따라 필요하신 방법을 적용하시면 된답니다.  
<br>
port-offset 값은 0 입니다.
http, https 기본값을 변경하였군요.  

{% highlight bash linenos %}
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
{% endhighlight %}

### Port 변경 후 적용 안되는 경우

운영중이던 서비스의 port 를 변경하고 jboss 재시작 하였으나 적용되지 않을 때는  

`/jboss-as-7.1.1.Final/standalone/configuration/standalone_xml_history` 디렉토리 하위 파일을 삭제해 주세요.  
재시작할때 과거 기록을 참고하여 `standalone.xml`을 재 작성하는 경우 였습니다.  
<br>

로그파일 및 가상파일도 같이 정리해 준다면 디스크 사용 효율이 높아지겠지요?  
한번은 Jboss 압축파일을 전달받았는데 파일용량이 5G 이상이길래 확인해봤더니 vfs 파일때문이였습니다.  
로컬 개발시 배포, 재시작이 잦은터라 vfs 파일이 시간이 지남에 따라 눈덩이처럼 커진거지요.  

{% highlight bash linenos %}
rm -rf /jboss-as-7.1.1.Final/standalone/configuration/standalone.xml/*
rm -f /jboss-as-7.1.1.Final/standalone/log/*
rm -rf /jboss-as-7.1.1.Final/standalone/tmp/vfs/*
{% endhighlight %}

## Rewrite

톰캣에도 [rewrite](https://docs.jboss.org/jbossweb/7.0.x/config/host.html) 기능이 있군요.  
웹서버가 따로 있는 경우 웹서버에서 rewrite 처리하지만 없는 경우(ex.개발서버)에 필요하지요.   
테스트 해보니 enable-welcome-root=true 인 경우에 rewrite 기능이 동작하는군요.  


{% highlight bash linenos %}
<subsystem xmlns="urn:jboss:domain:web:1.1" default-virtual-server="default-host" native="false">
    <connector name="http" protocol="HTTP/1.1" scheme="http" socket-binding="http"/>
    <virtual-server name="default-host" enable-welcome-root="true">
        <alias name="localhost"/>
        <rewrite pattern="^/$" substitution="/home/about" flags="nocase"/>
    </virtual-server>
</subsystem>
{% endhighlight %}


## Log

콘솔로그를 줄여봤어요.  

변경전
{% highlight bash linenos %}
12:16:59,282 INFO  [stdout] (http--0.0.0.0-8080-7) [2019-08-05 12:16:59,282] [DEBUG] [http--0.0.0.0-8080-7] [sql.didalgus.selectBoxProcess:132] <==      Total: 1
{% endhighlight %}

변경후
{% highlight bash linenos %}
[2019-08-05 15:28:22,794] [DEBUG] [http--0.0.0.0-8080-1] [sql.didalgus.selectBoxProcess:132] <==      Total: 1
{% endhighlight %}


변경전 `standalone.xml` 설정파일입니다.  

{% highlight bash linenos %}
<subsystem xmlns="urn:jboss:domain:logging:1.1">
    <console-handler name="CONSOLE">
        <level name="INFO"/>
        <formatter>
            <pattern-formatter pattern="%d{HH:mm:ss,SSS} %-5p [%c] (%t) %s%E%n"/>
        </formatter>
</console-handler>
{% endhighlight %}

`standalone.xml` 설정파일을 변경했습니다.  

{% highlight bash linenos %}
<subsystem xmlns="urn:jboss:domain:logging:1.1">
    <console-handler name="CONSOLE">
        <level name="INFO"/>
        <formatter>
            <pattern-formatter pattern="%s%E%n"/>
        </formatter>
</console-handler>
{% endhighlight %}

### SQL Log

jboss 관련은 아니지만 로그설정에 이어서   
콘솔로그를 확인하는데 sql 문이 나오지 않길래  
`/src/main/resources/logback.xml` 에 아래 설정 추가하고 WAS 재시작합니다.  

{% highlight bash linenos %}
<logger name="sql" level="DEBUG" additivity="false">
    <appender-ref ref="WSYS" />
    <appender-ref ref="WSYS-ERROR" />
    <appender-ref ref="STDOUT" />
</logger>
{% endhighlight %}

## Auto Deploy

[Deployment Scanner configuration](https://docs.jboss.org/author/display/AS7/Deployment+Scanner+configuration) 의 내용을 가져왔습니다.

{:.table.table-key-value-60}

| Name | Description |
|---|---|
|name |	 스캐너 이름, 지정되지 않는 경우 기본값 |
|path |	 스캔 파일 경로 <br> 'relative-to' 값이 있는경우 상대경로, 없는 경우 절대경로 |
|relative-to |  jboss.server.base.dir 는 JBOSS_HOME/standalone |
|scan-enabled |	true: 스캐너 기능 활성화 |
|scan-interval | 변경사항을 스캔하는 주기 <br> milliseconds (1000분의 1초) ex) 5000 : 5초 |
|auto-deploy-zipped | 기본값: fasle <br> 사용자가 .dodeploy marker file 추가하지 않고도 <br> 압축 된 배포 내용을 스캐너가 자동으로 배포 여부 제어 |
|auto-deploy-exploded |	기본값 : false (권장) <br> 사용자가 .dodeploy marker file 추가하지 않고도 <br> 배포(압축X) 내용을 스캐너가 자동으로 배포 여부 제어, <br>  true 인경우 콘텐츠 변경도중 배포 될수 있음 |
|deployment-timeout | 기본값 60초, 배포가 취소되기전에 실행할 수 있는 제한 시간(초)|


### CI 배포

운영환경마다 배포 구성이 다르지요.  
CI툴에서 빌드,배포,WAS 재기동 까지 하는 경우 아래와 같이 설정하면 되지요.  

{% highlight bash linenos %}
<subsystem xmlns="urn:jboss:domain:deployment-scanner:1.1">
    <deployment-scanner path="deployments" relative-to="jboss.server.base.dir" scan-enabled="true" scan-interval="5000" auto-deploy-exploded="true" deployment-timeout="600"/>
</subsystem>
{% endhighlight %}

### 수동 배포
구성상 자동배포(CI)가 적용될수 없어 수동으로 배포하는 경우도 있답니다.  
이런경우 압축된 파일이 아닌 jsp, class 파일들을 일일히 변경하는경우 총 10개 변경파일중 3개 적용하는중에 WAS 가 재기동되는 불상사가 발생하기도 하지요.  
<br>

그럴땐 `auto-deploy-exploded="false"` 설정한 후 변경파일을 적용하고  
`auto-deploy-exploded="true"` 로 변경하고 WAS 를 재구동하는 방법이 있지요.  
:grey_exclamation: `standalone.xml` 파일을 수정하는경우 설정을 적용해야 할려면 WAS를 재기동 해야 한답니다.  

<br>
`standalone.xml` 파일 설정 변경하고 싶지 않다!  
이런경우 `.dodeploy` 파일을 생성하는 방법도 있답니다.

{% highlight bash linenos %}
$ cd /usr/local/jboss-as/standalone/deployments
$ touch sample-project.war.dodeploy
{% endhighlight %}

`scan-enabled="true"` 활성되어 있으면 아래와 같이  
<u>dodeploy</u> > <u>isdeploying</u> > <u>deployed</u> 순으로 파일이 변경되면서 진행된답니다.

{% highlight bash linenos %}
-rw-rw-r-- 1 u g   0  sample-project.war.dodeploy      # Jboss 배포 파일 생성
-rw-rw-r-- 1 u g  18  sample-project.war.isdeploying   # Jboss 배포 진행중
-rw-rw-r-- 1 u g  18  sample-project.war.deployed      # Jboss 배포 완료
-rw-rw-r-- 1 u g  18  sample-project.war.undeployed    # Jboss 배포 실패
{% endhighlight %}

`.dodeploy` 파일이 없는 경우 아래와 같은 에러 로그를 확인 하실수 있어요.  

{% highlight bash linenos %}
5:00:21,260 INFO  [org.jboss.as.server.deployment.scanner] (DeploymentScanner-threads - 1) JBAS015003: Found sample-project.war in deployment directory. To trigger deployment create a file called sample-project.war.dodeploy
{% endhighlight %}

### Hot Deploy

WAS 재시작 없이 jsp 변경 사항을 적용하고 싶을때가 있지요.  
이걸 hot deploy 라고 말하기엔 좀 억지다 싶긴 해요; ㅎㅎ  

#### jsp-configuration

`standalone.xml` 파일 설정 아래 `jsp-configuration` 설정을 추가해주는 방법이 있는데요.  
운영 환경을 좀 타는것으로 보이네요.(안되는 경우가 더 많네요.)  
구글신도 버그 픽스가 필요하다고 하구요. tmp 파일을 지워보라고 하네요.  

{% highlight bash linenos %}
<extension module="org.jboss.as.web"/>

<subsystem xmlns="urn:jboss:domain:web:1.1" default-virtual-server="default-host" native="false">
	<configuration>
		<jsp-configuration development="true" keep-generated="false" check-interval="1" modification-test-interval="1" recompile-on-fail="true"/>
	</configuration>
	<connector name="http" protocol="HTTP/1.1" scheme="http" socket-binding="http"/>
	<virtual-server name="default-host" enable-welcome-root="false">
		<alias name="localhost"/>
	</virtual-server>
</subsystem>
{% endhighlight %}

#### development

`standalone.xml` 파일에 `auto-deploy-exploded="false"` 설정 후

{% highlight bash linenos %}
<subsystem xmlns="urn:jboss:domain:deployment-scanner:1.1">
    <deployment-scanner path="deployments" relative-to="jboss.server.base.dir" scan-interval="5000" auto-deploy-zipped="false" auto-deploy-exploded="false"/>
</subsystem>
{% endhighlight %}

개발소스 `WEB-INF/web.xml` 파일에 `development : true` 설정하는건 잘 되는것 같아요.  

{% highlight bash linenos %}
<servlet>
	<servlet-name>jsp</servlet-name>
	<servlet-class>org.apache.jasper.servlet.JspServlet</servlet-class>
	<init-param>
		<param-name>development</param-name>
		<param-value>true</param-value>
	</init-param>
	<load-on-startup>3</load-on-startup>
</servlet>
{% endhighlight %}


## Virtual Host

[가상 호스트](https://docs.jboss.org/jbossweb/7.0.x/config/subsystem.html) 설정이 필요하신가요?  

`/jboss-as-7.1.1.Final/standalone/configuration/standalone.xml` 파일에 `virtual-server` 설정을 추가해주세요.

{% highlight bash linenos %}
<subsystem xmlns="urn:jboss:domain:web:1.1" default-virtual-server="default-host" native="true">
	<connector name="http" protocol="HTTP/1.1" scheme="http" socket-binding="http"/>
	<virtual-server name="default-host" enable-welcome-root="false">
		<alias name="localhost"/>
		<alias name="example.com"/>
	</virtual-server>
	<virtual-server name="willowServer" default-web-module="Willow">
		<alias name="willow.com"/>
	</virtual-server>
</subsystem>  
{% endhighlight %}

개발 소스 `/WEB-INF/jboss-web.xml`파일에 `virtual-host` 설정을 맞게 넣어주시면 되지요.

{% highlight bash linenos %}
<jboss-web>
   <context-root>/</context-root>
   <virtual-host>willowServer</virtual-host>
</jboss-web>
{% endhighlight %}
