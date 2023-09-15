---
layout: post
title:  "Sourcetree : Native support for Apple Silicon"
date: 2023-09-15 15:30:00 +0900
abstract: "SourceTree"
tags: [Git, Sourcetree, Atlassian]
image:
toc: true
categories: Tech
last_modified_at: 
published: true
---

Git Client 중에서 가장 애착하는 Sourcetree.   
Intellij 에서 대부분의 작업을 하긴 하지만   
신규 프로젝트를 분석할때나 전체 히스토리를 파악할때 Sourcetree 로 살펴보면 한눈에 보여서 좋습니다요.  

![](/assets/article_images/2023-09-15-SourceTree/sourcetree_version.png)

애착툴이 어느날 부터 크러쉬나면서 꺼지는 현상이 종종 발생하더니    
오늘은 실행되자마자 꺼져버리는군요. 


## Development Environment
* MacBook Pro 16  
* MacOS Ventura 13.4.1  
* 칩 Apple M1 Pro  

## Sourcetree Release Notes
릴리즈 노트를 살펴봅니다.   

<br>  

실리콘 버전 지원 시작. 
```
Sourcetree 4.1.6 - Minor Release [8 February 2022]
CHANGES
* [SRCTREE-7446] Native support for Apple Silicon 
```

크러쉬 현상을 버그픽스 하였군요. 
```
Sourcetree 4.2.2 - Minor Release [28 Feb 2023]
BUG FIXES
* [SRCTREE-7918] Crashes frequently without doing anything on Apple Silicon M1
```

내장 git 이 동작하지 않는 현상도 버그픽스~ 

```
Sourcetree 4.2.4 - Minor Release [28 Jun 2023]
BUG FIXES
* [SRCTEE-7851][SRCTREE-8050] M1: embedded git don't work
```

가장 최신 버전 4.2.4 설치!  
종일 사용하였는데 동작 잘되는군요. Good~!

