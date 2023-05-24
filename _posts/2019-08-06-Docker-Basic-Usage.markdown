---
layout: post
title:  "Docker Basic Usage"
series: 1/3
date:   2022-04-06 18:20:00 +0900
abstract: "Docker 시리즈 중 첫번째 글입니다. Docker 기본 사용법과 도커 이미지를 생성하여 도커 허브에 올려봅시다."
tags: [docker, featured]
image:
toc: true
categories: docker
---


새로 투입된 서비스가 개발환경이 docker로 구축되어 있어 docker 관련글을 검색하다  
서비큐라님의 [초보를 위한 도커 안내서 - 도커란 무엇인가?](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html) 를 읽게 되었지요.  
<br>  

![]({{ site.url }}/assets/article_images/2019-08-06-Docker-Basic-Usage/horizontal-logo-monochromatic-white.png)

<br>

읽다보니 '아~ 이렇게 깔끔하게 정리를 잘해놓다니! 나도 이런 기술블로그를 운영해보고 싶다!' 라는 생각이 들었습니다.  
그래서 저의 기술블로그도 시작되었답니다.   
<br>

* **Docker Basic Usage ✓** <span class="series">SERIES 1/3</span>
* [Docker Apache+PHP]({% post_url 2019-08-07-Docker-Apache-PHP %}) <span class="series">SERIES 2/3</span>
* [Docker Hub]({% post_url 2019-08-08-Docker-Hub %}) <span class="series">SERIES 3/3</span>


개인적으로 Apache + PHP 환경을 구축 해볼려고 합니다.  
그에 앞서 가벼운 마음으로 기본 사용법 숙지해볼까요?  


## Images

[서비큐라님](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html) 포스팅에 워낙 설명을 잘해놓으신 터라 저는 간단하게만 정리했습니다.  

### List

로컬 환경에 있는 이미지 목록을 확인해볼까요?  

```bash
$ docker images
REPOSITORY       TAG           IMAGE ID          CREATED         SIZE
httpd            latest        d3a13ec4a0f1      2 weeks ago     132MB
mysql            5.7           e47e309f72c8      3 weeks ago     372MB
```


### Search

Docker 저장소에 있는 centos가 뭐가 있는지 검색해봅니다.  

```bash
$ docker search centos
NAME                      DESCRIPTION                    STARS    OFFICIAL   AUTOMATED
centos                    The official build of CentOS.  5221     [OK]                
ansible/centos7-ansible   Ansible on Centos7             120                 [OK]
```


### Pull

Docker 저장소에 있는 가장 최신 httpd 이미지를 로컬에 가져옵니다.  

```bash
$ docker pull httpd:latest

latest: Pulling from library/httpd
~
Status: Downloaded newer image for httpd:latest
```

tag를 따로 지정하지 않으면 default로 latest 값이 자동 지정되지요.  
centos=centos:latest 와 동일합니다.

```bash
$docker pull centos
~
Status: Downloaded newer image for centos:latest
```

### Remove

이것저것 다운받았네요.   
필요없는것은 지워보겠습니다.  
이미지 해시코드 앞부분만 적어도 중복된는 코드가 없으면 알아서 삭제해 준답니다.  
여러개를 한번에 지울수도 있지요.

```bash
$ docker rmi 3d557875ffc6 fce289e99eb9 f09fe80eb0e7
Untagged: mkrss/centos6:latest
Untagged: mkrss/centos6@sha256:a2ea2e4aeb4aabf060134fd969ba9bf8e18a0154e123075c2d61694885aeaa6f
Deleted: sha256:3d557875ffc68482cb9ef9b9af00f5af6a63ef75a94490f2a19818e761c2cf24
Deleted: sha256:7da2d14f323ca2e7ebbca8aa624012432e1a5cfce9764a6b67a959b1f3cd915d
Deleted: sha256:8fdf88328491a3716d20e2147ed96f1104c5e5058533655ccaed5c29d889608a
```

컨테이너까지 강제 삭제할 수도 있어요.  
이미지 해시코드가 아닌 `컨테이이너 이름`을 지정해 주었습니다.

```bash
$ docker rmi -f nginx
Untagged: nginx:latest
Untagged: nginx@sha256:dd2d0ac3fff2f007d99e033b64854be0941e19a2ad51f174d9240dda20d9f534
Deleted: sha256:f09fe80eb0e75e97b04b9dfb065ac3fda37a8fac0161f42fca1e6fe4d0977c80
```


### Commit

필요한 라이브러리가 설치된 컨테이너를 이미지로 만들어 보아요.  

```bash
$ docker commit ubuntu-container willow/ubuntu-apache-php-mcrypt
sha256:d84f180ac83da523cde7f354339e778bfb68f64ac29c48da332777d1be81707f
```

## Container

### 컨테이너 실행


다운받은 로컬 이미지를 컨테이너로 띄워보겠습니다.  
<br>

 -i: 인터렉티브 interactive 옵션. 표준 입력 STDIN 을 유지하며 호스트와 통신을 유지  
 -t: pseudo-tty 옵션. 호스트에서 터미널을 이용해 컨테이너와 통신하려면 필수적인 옵션   
 --name: 컨테이너 이름. 이번의 경우엔 php-apache  
 -p: 호스트의 8888 포트와 컨테이너의 80 포트를 연결    
 -v: volume 마운트, 호스트의 /project/docker 를 컨테이너의 ~/source/project/DocRoot에 연결

```bash
$ docker run -i -t -p 8888:80 -v /project/docker:~/source/project/DocRoot --name php-apache
```


실행중인 컨테이너에 접속해볼까요?  
docker attach `컨테이너이름`  

```bash
$ docker attach willow_ubuntu
```

다른방법으로 외부에서 컨테이너 안에 명령을 내리는 방법도 있어요.  
docker exec `컨테이너 이름` `명령` `매개변수`



### 컨테이너 중지 & 시작

가장 많이 쓰게 될 명령이죠.  
컨테이너 전체 리스트의 상세 정보입니다.  

```bash
$ docker container ls -a   # 컨테이너 전체 리스트  (중지, 실행포함)

CONTAINER ID   IMAGE         COMMAND           CREATED       STATUS       PORTS                              NAMES
c54607322d9f   mysql:5.7     "docker-entryp…"  2 weeks ago   Up 2 hours   3060/tcp, 0.0.0.0:3060->3306/tcp   docker_mysql
f0c86d1423ba   nginx         "nginx -g 'dae…"  2 weeks ago   Exited (0)  2 hours ago                         webserver
8aa6a78bfe40   hello-world   "/hello"          2 weeks ago   Exited (0)  2 weeks ago                         agitated_buck
```

컨테이너를 중지해보겠습니다.  
```bash
$ docker container stop webserver
webserver
```


컨테이너를 시작해보겠습니다.
```bash
$ docker container start webserver
webserver
```

재시작 명령도 지원합니다.
```bash
$ docker container restart webserver
webserver
```


### 컨테이너 삭제

컨테이너를 삭제 합니다.
```bash
$ docker container rm webserver
webserver
```


기본값이 container 이여서 생략이 가능합니다.  
여러개 컨테이너도 한번에 지울수 있지요.

```bash
$ docker rm php-apache php_apache2 php_apache3
php-apache
php_apache2
php_apache3
```
