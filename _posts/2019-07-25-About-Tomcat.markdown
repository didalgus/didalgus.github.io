---
layout: post
title:  "about tomcat"
date:   2019-07-25 15:00:00 +0900
abstract: ""
tags: [tomcat]
image:
toc: true
categories: Utility
---




톰캣 서버의 폴더 구조

|directory name | desc |
|---|---|
|bin | 샐행과 관련된 배치 파일, 스크립트 파일 |
|conf | 설정파일 |
|lib | 톰캣 서버의 java library |
|logs | 톰캣 서버 실행 상태를 기록한 로그 파일 |
|temp | 톰캣 서버 실행중 사용하는 임시 디렉토리 |
|webapps | 웹 어플리케이션을 배치하는 디렉토리 |
|work | JSP 를 java servlet 소스로 변환한 파일 위치 |



$ brew search tomcat
==> Formulae
tomcat                       tomcat-native                tomcat@6                     tomcat@7                     tomcat@8 ✔



$ brew list --versions | grep tomcat
tomcat@8 8.5.38
