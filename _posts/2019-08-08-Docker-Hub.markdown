---
layout: post
title:  "Docker Hub"
series: 3/3
date:   2019-08-08 18:20:00 +0900
abstract: ""
tags: [docker, docker hub]
image:
toc: true
categories: docker
---

![docker hub logo]({{ site.url }}/assets/article_images/2019-08-08-Docker-Hub/dockerhub-logo.png)


* [Docker Basic Usage]({% post_url 2019-08-06-Docker-Basic-Usage %}) <span class="series">SERIES 1/3</span>
* [Docker Apache+PHP]({% post_url 2019-08-07-Docker-Apache-PHP %}) <span class="series">SERIES 2/3</span>
* **Docker Hub ✓**  <span class="series">SERIES 3/3</span>


지난 포스팅에서 만든 Image 를 [Docker Hub](https://hub.docker.com/) 에 올려서 공유 해보도록 하겠습니다.

## Login

[Docker Hub](https://hub.docker.com/) 계정을 생성하세요.  
커맨드라인(CLI) 명령을 사용해서 로그인 해주세요.  

```bash
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you dont have a Docker ID, head over to https://hub.docker.com to create one.
Username: 계정
Password: 비밀번호
Login Succeeded
```


## Push Image

Image 를 DockerHub(저장소)에 올릴때 아래 규칙에 맞춰 작성해주세요.   

`Docker Hub 사용자 계정`/`이미지 이름`:`태그`

```bash
$ docker push didalgus/ubuntu18_apache24_php56:0.1
The push refers to repository [docker.io/didalgus/ubuntu18_apache24_php56]
~
0.1: digest: sha256:271adb3e3f87dd4604cf9633208db2b890c68e55f7ee4567f8615a281dc08630 size: 1778
```


## Repositories

내 저장소에 잘 올라갔는지 웹에서 확인해볼까요?  
웹에서 검색해보고요~

![https://hub.docker.com/r/didalgus/ubuntu18_apache24_php56]({{ site.url }}/assets/article_images/2019-08-08-Docker-Hub/dockerhub-repository.png)

CLI에서도 검색해보세요.

```bash
$ docker search didalgus
NAME                               DESCRIPTION                  STARS               OFFICIAL            AUTOMATED
didalgus/ubuntu18_apache24_php56   apache webservice with php   0                
```


잘 올라갔군요.  
이제 함께 유지보수 할 개발자분들께 공유해요~  
개발환경 구성에 들이는 시간을 다같이 줄여보자구요! :whale:
