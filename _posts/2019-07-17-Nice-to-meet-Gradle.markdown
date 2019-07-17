---
layout: post
title:  "Nice to meet Gradle"
date:   2019-07-17 17:30:00 +0900
abstract: ""
tags: [Gradle]
image:
toc: true
categories: Gradle
last_modified_at: 2019-07-17T17:30:00+09:00
---

![Gradle logo]({{ site.url }}/assets/article_images/2019-07-17-Nice-to-meet-Gradle/logo-gradle.png)



# 소개  

[Gradle](https://gradle.org)은 [Groovy](https://groovy-lang.org)로 만들어진 녀석입니다.  
Groovy는 JVM(Java Virtual Machine)위에서 동작하지요.  
Gradle에 library를 추가하거나 정교하게 제어할려면 Groovy를 배워야 할테지만  
저는 일단 기본적인 기능만 사용할꺼라 간단하게 정리했습니다.  
<br>
Gradle은 앤트(Ant)의 기본적인 빌드 도구 기능에 메이븐(Maven)의 의존라이브러리 관리 기능을 차용하였습니다.  
메이븐은 상속구조인데요. 그래들은 설정 주입방식 사용하기에 유연한 빌드 환경 구성 가능하답니다.  
<br>
Gradle은 Task를 사용해서 빌드 순서도 제어할 수 있지요.  
제가 본 책은 Task 사용을 기본으로 설명하였더라구요. Task를 사용하지 않는 프로젝트도 있답니다.


# 동작

![Gradle (https://gradle.org)]({{ site.url }}/assets/article_images/2019-07-17-Nice-to-meet-Gradle/gradle-org-hero.png)



gradle 은 제일 먼저 settings.gradle 파일을 찾는답니다.  
구조 설정을 확인하기 위해서  
gradle 명령을 실행한 디렉토리에서 찾고 없다면 상위 디렉토리도 뒤지지요.  





# The Gradle Wrapper

설치된(locally) gradle 버전과 유지보수할 프로젝트의 gradle 버전이 달라 빌드가 실패하는거예요.  
이럴때 [gradle wrapper](https://docs.gradle.org/5.4/userguide/gradle_wrapper.html) 는 없어서는 안될 녀석입죠. (너란 존재, 빛나는구나)    
<br>

![Figure 1. The Wrapper workflow]({{ site.url }}/assets/article_images/2019-07-17-Nice-to-meet-Gradle/wrapper-workflow.png)

<br>
gradle wapper 에 설정된 버전을 확인하고 싶을땐 아래 경로에서 버전을 확인하세요.  
  
/project/gradle/wrapper/gradle-wrapper.properties  
```
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-5.2.1-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```


# ETC

이클립스의 기본 output 디렉토리는 bin 이고, 인텔리제이 기본output 디렉토리는 out입니다.  
인텔리 제이 IDE 를 쓰는 경우 아래 내용을 참고하세요.  
<br>

> 상호 간에 소스 디렉터리를 공유해서 개발을 진행하는 경우에는 로컬 개발 환경이 서로 다른데, 이 경우 정보가 각 개발 도구의 메타 정보에 포함되어 있어서 문제가 생기는 경우가 있습니다.   
그래들의 idea와 eclipse 같은 Task를 활용하면 각 개발 도구의 메타 정보를 생성할 수 있으므로 소스 공유 시 로컬 환경에 따른 충돌을 방지하고 개발을 진행할 수 있습니다.     
엔터프라이즈 빌드 자동화를 위한 Gradle - 한빛 미디어


/project/build.gradle

```
idea{
  module{
      outputDir = file("${buildDir}/classes/main")
      inheritOutputDirs = false
      downloadSources = true

      excludeDirs = [file(".gradle")]
      ["classes", "docs", "dependency-cache", "libs", "reports", "resources", "test-results", "tmp"].each {
          excludeDirs << file("$buildDir/$it")
      }
  }
}
```
