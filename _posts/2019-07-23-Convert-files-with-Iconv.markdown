---
layout: post
title:  "Convert files with iconv"
date:   2019-07-23 15:00:00 +0900
abstract: ""
tags: [linux, iconv]
image:
toc: true
categories: Utility
---

![Error]({{ site.url }}/assets/article_images/2019-07-23-Convert-files-with-Iconv/encoding.png)


개발 소스 중에 EUC-KR 인코딩 파일은 macOS 에서 한글이 깨지는 현상이 있지요.  
UTF-8 인코딩 파일로 변환해서 컴파일하고 실행해야지요.  


<br>

## 파일 인코딩  

파일 인코딩을 확인해 볼까요?
I 옵션을 주면 mime 타입 인코딩 정보가 나옵니다.  
<br>
씁슬하게도 `EUC-KR` 인코딩은 라틴어 표준코드인 `ISO-8859-1` 로 인식하네요.  

```bash
$ file Client.java
Client.java: c program text, ISO-8859 text

$ file -I Client.java
Client.java: text/x-c; charset=iso-8859-1
```

UTF-8 인코딩파일 보시겠습니다.  
```bash
$ file Test.java
Test.java: Java source, UTF-8 Unicode text

$ file -I Test.java
Test.java: text/x-java; charset=utf-8
```

## ICOV 사용법

아래 명령어로 변환시 파일내용이 콘솔에 그대로 출력되서 나오지요.  
간단한 내용확인만 필요하고 변환된 인코딩 파일 생성은 필요없는 경우겠지요.  
iconv -f `원본파일인코딩` -t `변경인코딩` `원본파일명`

<br>
보통은 파일 저장해서 쓰겠지요?  
iconv -f `원본파일인코딩` -t `변경인코딩` `원본파일명` > `새파일명`  


```bash
$ iconv -f EUC-KR -t UTF-8 Test.java > Test_utf8.java
```

## 인코딩 목록

iconv 에서 변환가능한 인코딩 목록입니다.  

```bash
$ iconv -l
ANSI_X3.4-1968 ANSI_X3.4-1986 ASCII CP367 IBM367 ISO-IR-6 ISO646-US ISO_646.IRV:1991 US US-ASCII CSASCII
UTF-8 UTF8
...
```


## 안내말씀

혹시나 해서 드리는 말인데,  
파일명 새로 변환하기 귀찮다고 새파일명을 동일이름으로 지정한다면 O KB 파일을 보게될꺼예요.  
귀차니즘이 낳은 대참사를 겪을실껍니다.  

```bash
$ iconv -f EUC-KR -t UTF-8 Test.java > Test.java
```
