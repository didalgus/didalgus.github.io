---
layout: post
title:  "Docker Apache+PHP"
series: 2/3
date:   2019-08-07 17:40:00 +0900
abstract: ""
tags: [docker, apache, php, mysql, codeigniter]
image:
toc: true
categories: docker
---

![]({{ site.url }}/assets/article_images/2019-08-07-Docker-Apache-PHP/docker-logo.png)

* [Docker Basic Usage]({% post_url 2019-08-06-Docker-Basic-Usage %}) <span class="series">SERIES 1/3</span>
* **Docker Apache+PHP ✓** <span class="series">SERIES 2/3</span>
* [Docker Hub]({% post_url 2019-08-08-Docker-Hub %}) <span class="series">SERIES 3/3</span>

유지보수중인 서비스가 로컬 개발환경(MacOS)에 설치된 Apache, PHP버전(최신)에서 동작하지 않는 이슈가 발생하였습니다.  
<br>
부연하자면,  
MacOS에 내장된 Apache 는 OS 업그레이드 될때 기존 설정을 backup 후 초기화 해버립니다.  
이럴때마다 기존 설정을 복원해야하는 번거로움이 있더군요.  
PHP 는 homebrew로 버전별 설치 가능한데, 7버전(최신)는 mcrypt 모듈을 사용할 수 없고, 5버전에는 mcrypt 모듈 추가가 안되었습니다. (제가 방법을 몰라서 그런걸 수도 있어요.)  


## Goal

docker 컨테이너에 apache+php 환경 구성 후 로컬환경에 있는 소스파일을 볼륨연결 설정하여 개발 환경을 구축해보겠습니다.


## Version

컨테이너에 구축할 버전을 정리해 보았습니다.  
OS 이미지(Ubuntu)를 기반삼아 WebServer(Apache)와 PHP를 설치하였습니다.  
<br>
본인 개발환경이 Ubuntu 나 CentOS 이면 바로 Apache 와 PHP 만 올려도 되겠지요.  
사실 위 개발환경이면 docker로 구축할 필요도 없겠네요. ㅎㅎ  
<br>

{:.table.table-key-value-60}

| 항목 | version |
|---|---|
| Operating System          | Ubuntu 18.04.1 LTS |
| Web Server                | Apache 2.4.29      |   
| + Apache extension module | rewrite            |
| Language                  | PHP 5.6.40         |
| + PHP extension module    | mcrypt, mysql      |
| Framework                 | CodeIgniter 2.2    |


## Run

OS(Ubuntu)를 검색합니다.  
OFFICIAL 항목이 OK인 이미지가 있군요. 아무래도 공식 이미지가 안정적이겠지요.  

```bash
 $ docker search ubuntu
 NAME         DESCRIPTION                                      STARS      OFFICIAL      AUTOMATED
 ubuntu       Ubuntu is a Debian-based Linux operating sys…    9265        [OK]
```

최신버전으로 다운 받아요.

```bash
 $ docker pull ubuntu:latest
 ~
 Status: Downloaded newer image for ubuntu:latest
```

로컬 환경 도커 이미지 리스트로 다운받은 OS를 확인합니다.

```bash
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              47b19964fb50        3 weeks ago         88.1MB
```

컨테이너로 띄워 볼까요?  
참고로  -i -t 옵션 사용시 run 후 컨테이너 진입한답니다.

```bash
$ docker run -i -t --name ubuntu-container ubuntu
```

컨테이너에 접속되었네요.  
두근두근~ 너의 이름을 알려다오~  

```bash
root@259f01bb0482:$ cat /etc/issue
Ubuntu 18.04.1 LTS \n \l
```

## Apache

운영환경 Apache 버전은 2.2인데 apt-get 으로 설치할 수 있는 버전이 2.4이더군요.  
동작은 동일하니 설정을 동일하게 맞춰 주시면 되지요.

###  Apache 2.4

apt, compile, binary 파일 복사 방법중에 본인에게 가장 편한 방법으로 설치하시면 되겠습니다.
저는 아재인지라 apt-get를 이용해서 설치했어요.

```bash
root@b9f05a4fe868:$ apt-get update -y

root@b9f05a4fe868:$ apt-cache search apache2
apache2 - Apache HTTP Server
~

root@b9f05a4fe868:$ apt-get install apache2
```

설치된 Apache 버전정보나 실행파일 위치등을 확인~

```bash
root@b9f05a4fe868:$ apachectl -V
Server version: Apache/2.4.29 (Ubuntu)
Server built:   2018-10-10T18:59:25
~

root@b9f05a4fe868:$ which apache2
/usr/sbin/apache2
```

구동중인 Apache 의 로그도 살펴보았어요.  
구동방법등 기초적인 사항은 구글신께~

```bash
$ tail -f /var/log/apache2/access.log
```


###  Apache Module

CodeIgnier Framework이 rewrite rule 를 사용해서 rewrite 를 활성했어요.

```bash
root@99c8baee6af4:$ a2enmod rewrite
Enabling module rewrite.
```

Apache 설정에 rewrite 모듈라인이 주석처리 되어 있으면 해제해주세요.

```bash
$ cat /etc/apache2/mods-available/rewrite.load
LoadModule rewrite_module /usr/lib/apache2/modules/mod_rewrite.so
```

rewrite 모듈이 활성이 안된경우 Apache 구동중 나는 에러예요.

```bash
Invalid command 'RewriteEngine', perhaps misspelled or defined by a module not included in the server configuration
```

드뎌 rewrite 모듈이 잘 적용되었네요.

```bash
$ apachectl -D DUMP_MODULES | grep rewrite
rewrite_module (shared)
```


## PHP

### php5

php를 설치해 보겠습니다.
apt-get 기본 저장소에는 php7 버전만 있어서 old 버전 설치를 위해 저장소를 추가했어요.

```bash
$ apt-get install software-properties-common  # add-apt-repository 사용을 위해 설치
$ add-apt-repository ppa:ondrej/php           # old version php 저장소
$ apt-cache search php5                       # php5 검색
$ apt-get update
```


php 설치 해보겠습니다.

```bash
$ apt-get install php5.6
~
After this operation, 19.7 MB of additional disk space will be used.
~
Creating config file /etc/php/5.6/apache2/php.ini with new version
~
apache2_invoke: Enable module php5.6
Setting up php5.6 (5.6.40-5+ubuntu18.04.1+deb.sury.org+1) ...
```

잘 설치되었는지 확인해볼께요.

```bash
$ php -v
PHP 5.6.40-5+ubuntu18.04.1+deb.sury.org+1 (cli)
```

브라우저로 서비스를 접근해도 빈화면만 나오네요. 에러를 확인해야 겠군요.  
`display_errors = On` 설정을 추가하거나 주석처리된경우 주석 해지해주세요.

```bash
$ vi /etc/php/5.6/apache2/php.ini
$ vi /etc/php/5.6/cli/php.ini

display_errors = On
```

### PHP Module

암복화에 mcrypt 함수를 사용하고 있어 `mcrypt` 모듈을 추가해야겠군요.

해당 모듈이 있는지 검색해볼까요?

```bash
$ apt-cache search php5.6
~
php5.6-mcrypt - libmcrypt module for PHP
```

설치합니다.

```bash
$ apt-get install php5.6-mcrypt
After this operation, 303 kB of additional disk space will be used.
Do you want to continue? [Y/n] Y
~
Creating config file /etc/php/5.6/mods-available/mcrypt.ini with new version
```


DB 는 mysql를 사용하고 있어 `mysql` 모듈도 설치~   

```bash
$ apt-get install php5.6-mysql
~
After this operation, 540 kB of additional disk space will be used.
Creating config file /etc/php/5.6/mods-available/mysql.ini with new version
Processing triggers for libapache2-mod-php5.6 (5.6.40-5+ubuntu18.04.1+deb.sury.org+1) ...
```


모듈이 잘 붙었는지 확인해보아요.  

```bash
$ php -m | grep mcrypt
mcrypt

$ php -m | grep mysql
mysql
mysqli
mysqlnd
pdo_mysql

```


php 를 실행해서 확인해볼까요?

```bash
$ php -r "mcrypt_create_iv();"
```

<br>

:cactus: 참고로, mcrypt는 PHP 7.1 버전부터 Deprecated 되었어요.

>**Warning**
This feature was DEPRECATED in PHP 7.1.0, and REMOVED in PHP 7.2.0.  
출처 : [https://www.php.net/manual/en/intro.mcrypt.php](https://www.php.net/manual/en/intro.mcrypt.php)


## Apache + PHP

### Configuration

이제 Apache에 php 설정을 추가 하겠습니다.

Apache 설정에 php 모듈라인에 주석`#`이 있으면 해제해주세요.

```bash
$ cat /etc/apache2/mods-available/php5.6.load
LoadModule php5_module /usr/lib/apache2/modules/libphp5.6.so
```


Apache 내 php 설정에 아래 설정이 없으면 추가해주세요.

```bash
$ cat /etc/apache2/mods-available/php5.6.conf

<FilesMatch ".+\.ph(p[3457]?|t|tml)$">
SetHandler application/x-httpd-php
</FilesMatch>
```

### Vhost

`sites-available` 디렉토리 하위에 `001-codeigniter.conf` 가상 호스트 설정 파일을 생성합니다.

{% highlight xml linenos %}
<VirtualHost *:80>
    ServerName dev.willow.pe.kr
    ServerAdmin email@gmail.com
    DocumentRoot /var/www/codeigniter/
    ErrorLog ${APACHE_LOG_DIR}/codeigniter-error_log
    CustomLog ${APACHE_LOG_DIR}/codeigniter-access_log common

    <Directory "/var/www/codeigniter">
        AllowOverride All
        Require all granted
    </Directory>

    RewriteEngine on
    RewriteCond $1 !^(/index.html|/index\.php|/phpinfo\.php|/images|/css|/js|/utils|/htmls)
    RewriteRule ^(.*)$ /index.php/$1 [L]
</VirtualHost>
{% endhighlight %}

`sites-available`에 있는 설정파일을 `sites-enable`에 심볼릭 링크를 걸어주세요.

```bash
$ ls -al /etc/apache2/sites-available/
001-codeigniter.conf

$ ln -s ../sites-available/001-codeigniter.conf /etc/apache2/sites-enabled/      # 심볼릭링크 설정

$ ls -al /etc/apache2/sites-enabled/
001-codeigniter.conf -> ../sites-available/001-codeigniter.conf

```

### Service start

아파치를 구동해볼까요?

```bash
$ service apache2 start
```


아파치 로그를 보아요.  

```bash
$ tail -f /var/log/apache2/codeigniter-error_log
$ tail -f /var/log/apache2/codeigniter-access_log
```


브라우저로 확인해볼까요?  
Apache Documnet Root 로 설정된 위치에 phpinfo.php 파일을 생성합니다.   

```bash
$ cat /var/www/html/phpinfo.php

<?php phpinfo() ?>
```

확인하셨나요?  

![]({{ site.url }}/assets/article_images/2019-08-07-Docker-Apache-PHP/php-info.png)

<br>

### Docker Image

이제 필요한 환경구성은 다 되었군요.  
컨테이너를 이미지로 구워보겠습니다.  

```bash
$ docker commit ubuntu-container didalgus/ubuntu18_apache24_php56:0.2
```



## CodeIgniter

이제 프레임워크를 올려볼께요.

[https://codeigniter.com/download](https://codeigniter.com/download)  
운영중인 서비스에 적용된 버전에 맞추어 `CodeIgniter 2.x`을 로컬 개발환경에 다운로드 합니다.  

### Volume mount

로컬개발환경 ~/Documents/CodeIgniter-2.2.6 에 압축을 풀었습니다.   
위에서 구워논 이미지를 컨테이너로 띄울때 로컬개발환경 볼륨정보를 추가하였습니다.  

```bash
$ docker run -i -t -p 80:80 -v ~/Documents/CodeIgniter-2.2.6:/var/www/codeigniter --name my_container didalgus/ubuntu18_apache24_php56:0.2
```

컨테이너에서 /var/www/ 경로를 확인하니 마운트 된걸 확인 할 수 있군요.  

```bash
$ ll /var/www/
drwxr-xr-x 10 root root  320 Mar 21 21:59 codeigniter       # docker volume mount (local)
drwxr-xr-x  1 root root 4096 Mar 21 00:10 html              # apache default root
```

브라우저에서 확인해보아요.

![Welcome to CodeIgniter!]({{ site.url }}/assets/article_images/2019-08-07-Docker-Apache-PHP/php-welcome.png)


### Database connect

Docker 컨테이너의 private IP 는 뭘까요?  

{% highlight php linenos %}
echo $_SERVER['SERVER_ADDR']; exit;
{% endhighlight %}

답은 **172.17.0.2** 입니다.  

<br>

MySQL Database 에 docker container private ip 를 허용한 db계정을 생성해 주세요.  
172.17.0.**1** 을 허용한 db 계정을 생성해놓고 빈화면을 보며 삽질 했답니다.

<br>
원인은 `CodeIgniter 2.x` 소스 내 mysql함수앞에 **@** 처리(에러무시)를 해두어서 에러 표시가 안되고 있었습니다.

{% highlight php linenos %}
@mysqli_connect()
{% endhighlight %}

여러분은 브라우저의 빈화면을 보는 시간이 적기를.. (흑흑 ㅠㅠ)  
