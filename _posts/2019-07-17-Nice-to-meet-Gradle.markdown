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
<br>
Gradle 은 Groovy 로 만들어진 녀석인지라 런타임시에도 설정을 반영할 수있다고 합니다. (해보진 않았어요)  

# 동작

![Gradle (https://gradle.org)]({{ site.url }}/assets/article_images/2019-07-17-Nice-to-meet-Gradle/gradle-org-hero.png)



gradle 은 제일 먼저 settings.gradle 파일을 찾는답니다.  
gradle 명령을 실행한 디렉토리에서 찾고 없다면 상위 디렉토리도 뒤지지요.  
상위 디렉토리에도 settings.gradle 파일이 없다면 그 빌드는 싱글 프로젝트로 인식되어 실행 됩니다.  
<br>
`setting.gradle` 은 프로젝트의 구성 정보를 기록하고, 어떤 하위 프로젝트가 어떤 관계로 구성되어 있는지 기술하지요.   
그래서 gradle 은 setting.gradle 에 기술된대로 프로젝트를 구성한답니다.  
<br>
`build.gradle` 은 빌드에 필요한 기본 설정 파일이라도 보시면 되겠습니다.  
의존성이나 플러그인 설정의 위한 스크립트 파일이지요.   
정의된 의존성 설치 후에 프로젝트 인덱싱을 하지요.  





# Use



## Task

제가 유지보수하는 프로젝트는 대부분 Web프로젝트입니다. 그래서 task 쓸일이 없지요.  
그러다 추가된 데몬 프로젝트가 task로 동작하기에 살펴보다 실습까지 해보았습니다.

![Java plugin - tasks]({{ site.url }}/assets/article_images/2019-07-17-Nice-to-meet-Gradle/javaPluginTasks.png)

이미지 출처: [The following diagram shows the relationships between these tasks.](https://docs.gradle.org/current/userguide/java_plugin.html)


<br>

### init

init 명령으로 구성할 수 있는 프로젝트를 확인해 봅니다.  

```bash
$ gradle help --task :init
...
     --type     Set the type of project to generate.
                Available values are:
                     basic
                     cpp-application
                     cpp-library
                     groovy-application
                     groovy-library
                     java-application
                     java-library
                     kotlin-application
                     kotlin-library
                     pom
                     scala-library
```
basic 으로 시작해 볼까요?  

```bash
$ gradle init
=
$ gradle init --type basic

Select build script DSL:
  1: groovy
  2: kotlin
Enter selection (default: groovy) [1..2] 1

Project name (default: gralde_practice): practice1

BUILD SUCCESSFUL in 32s
2 actionable tasks: 2 executed
```
init 명령시 type 옵션을 추가하지 않아도 기본값이 `basic` 이랍니다.  
설치 후 디렉토리를 살펴보면 `wrapper` 이 보이지요.  
init 명령을 수행하면 자동으로 생성된답니다.  
init 명령을 실행한 환경에 설치된 gradle 버전이 설정되는군요.  

### task 실습
`build.gradle` 파일에 아래 코드를 추가해보아요.
```gradle
task welcome {
    println 'Welcome Gradle!'
}
```

그래들은 멀티 프로젝트를 구성할 수있기 때문에 콜론(:)으로 프로젝트와 Task를 구별합니다.  
공란인 이유는 싱글 프로젝트여서겠지요.  
task를 실행해보아요.   

```bash
$ gradle welcome
=
$ gradle :welcome

> Configure project :
Welcome Gradle!

Default tasks: welcome

BUILD SUCCESSFUL in 0s
```

참 쉽죠잉~?

### task options

- Task 전체 목록을 확인하고 싶다고요?   

```bash
$ gradle tasks --all
> Configure project :
Welcome Gradle!
Hello~
...
Default tasks: welcome
...
Other tasks
-----------
hello
welcome - 디폴트 Task입니다.
```

- 디폴트 Task 지정

`build.gradle` 파일에 아래와 같이 설정 해보아요.  
설명도 추가할 수 있어요.

```
defaultTasks 'welcome(description:'디폴트 Task입니다.', dependsOn:'hello')'
```  

그런 다음 gradle 실행을 하면
```gradle
$ gradle
> Configure project :
default task
```

- Task 그룹  

```
def myGroup = 'myGroup'

task task1(group: myGroup) {
    println "task1"
}

task task2(group: myGroup) {
    println "task2"
}
```  



## Compile

> build 디렉터리는 그래들에서 컴파일할 때 클래스 파일들이 위치하는 기본 경로로, 컴파일 이전의 디렉터리 구조와 동일하게 생성됩니다.  
엔터프라이즈 빌드 자동화를 위한 Gradle - 한빛 미디어 p.34

```bash
$ gradle compileJava

BUILD SUCCESSFUL in 8s
1 actionable task: 1 executed
```

자, build 디렉토리가 가서 확인해볼까요?

```bash
$ ll build/classes/java/main/practice2/
total 16
-rw-r--r--  1 we  staff   605B  7  8 18:53 FirstGradle.class
-rw-r--r--  1 we  staff   350B  7  8 18:53 Library.class
```

## Clean

build 디렉토리에 컴파일된 class 파일을 삭제해볼까요?

```bash
$ gradle clean task

$ ll build/classes/java/main/practice2/
ls: build/classes/java/main/practice2/: No such file or directory
```




## Build


```bash
$ ./gradlew clean build
```




## Run
```bash
$ ./gradlew project-web:bootRun -Dspring.profiles.active=dev
```


## Multi Project

멀티프로젝트로 형식으로 구성된 스프링부트 프로젝트를 실행할때 명령어입니다.  

```bash
$ ./gradlew project-web:bootRun -Dspring.profiles.active=dev
```
스프링부트에서 어플리케이션을 분리된 형태로 실행할 수 있는 [bootRun](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-tools/spring-boot-gradle-plugin/src/main/java/org/springframework/boot/gradle/tasks/run/BootRun.java) gradle plugin을 제공하고 있습니다.


# Plugins

Gradle 에 사용할 수 있는 [plugins](https://plugins.gradle.org/)을 검색할 수 있어요.  
위에서 이야기한 [spring boot 플러그인](https://plugins.gradle.org/plugin/org.springframework.boot) 페이지입니다.  

# The Gradle Wrapper

설치된(locally) gradle 버전과 유지보수할 프로젝트의 gradle 버전이 달라 빌드가 실패하는거예요.  
이럴때 [gradle wrapper](https://docs.gradle.org/5.4/userguide/gradle_wrapper.html) 는 없어서는 안될 녀석입죠. (너란 존재, 빛나는구나)
<br>
gradle wrapper 는 JAVA도 gradle 도 설치할 필요가 없지요.  
로컬에 설치된 gradle 버전도 신경 쓸 필요도 없구요.
그러니 gradle wrapper 를 사용합시다.     
<br>

![Figure 1. The Wrapper workflow]({{ site.url }}/assets/article_images/2019-07-17-Nice-to-meet-Gradle/wrapper-workflow.png)

Gradle wrapper 의 버전을 수정하게 되면 task 실행시 자동으로 새로운 Wrapper 파일을 로컬캐시에 다운로드 받는답니다.

<br>
gradle wapper 에 설정된 버전을 확인하고 싶을땐 아래 경로에서 버전을 확인하세요.  

/project/gradle/wrapper/gradle-wrapper.properties  
```gradle
distributionUrl=https\://services.gradle.org/distributions/gradle-5.2.1-bin.zip
```

cli 명령어로도 확인가능하지요.  
프로젝트별로 버전이 다른걸 확인 할 수 있습니다.  
```bash
~/Documents/source/willow/toybox $ ./gradlew -v
Gradle 5.2.1

~/Documents/source/company/project $ ./gradlew -v
Gradle 5.1
```

<br>
로컬 환경 gradle 버전을 확인해 볼까요?  
```bash
~/Documents/source/willow/gralde_practice $ gradle --version

------------------------------------------------------------
Gradle 5.2.1
------------------------------------------------------------

Build time:   2019-02-08 19:00:10 UTC
Revision:     f02764e074c32ee8851a4e1877dd1fea8ffb7183

Kotlin DSL:   1.1.3
Kotlin:       1.3.20
Groovy:       2.5.4
Ant:          Apache Ant(TM) version 1.9.13 compiled on July 10 2018
JVM:          1.8.0_131 (Oracle Corporation 25.131-b11)
OS:           Mac OS X 10.14.5 x86_64

```


# Log Level


```
$ gradle -q
```

[Gradle Docs - Logging](https://docs.gradle.org/current/userguide/logging.html) 페이지를 방문하시면 더욱 자세한 설명을 확인 할 수있습니다.  
우리에겐 공홈 레퍼가 있다! :stuck_out_tongue_winking_eye:


{:.table.table-key-value-60}
|Option|ERROR|Error messages|
|---|---|---|
|-q or --quiet|QUIET|Important information messages|
|-w or --warn|WARNING| Warning messages|
|no logging options|LIFECYCLE|Progress information messages|
|-i or --info|INFO|	Information messages|
|-d or --debug|DEBUG|Debug messages|





# ETC


## Source
로컬 환경에 설치된 gradle 소스를 구경해보았어요.  


### Settings
/usr/local/gradle-4.8.1/src/core-api/org/gradle/api/initialization/Settings.java

```java
@HasInternalProtocol
public interface Settings extends PluginAware {
    /**
     * <p>The default name for the settings file.</p>
     */
    String DEFAULT_SETTINGS_FILE = "settings.gradle";

```

### Project

/usr/local/gradle-4.8.1/src/core-api/org/gradle/api/Project.java
```
@HasInternalProtocol
public interface Project extends Comparable<Project>, ExtensionAware, PluginAware {
    /**
     * The default project build file name.
     */
    String DEFAULT_BUILD_FILE = "build.gradle";


```

/usr/local/gradle-4.8.1/src/core-api/org/gradle/api/Task.java
```
    /**
     * The default build directory name.
     */
    String DEFAULT_BUILD_DIR_NAME = "build";
```
> Task 인터페이스에서는 기본적으로 Task의 이름과 Task의 유형, Task의 의존성 여부, Task 액션 등을 속성으로 가지고 있습니다.  
엔터프라이즈 빌드 자동화를 위한 Gradle - 한빛 미디어 p.10




## IntelliJ 설정

이클립스의 기본 output 디렉토리는 bin 이고, 인텔리제이 기본output 디렉토리는 out입니다.  
인텔리 제이 IDE 를 쓰는 경우 아래 내용을 참고하세요.  
<br>

> 상호 간에 소스 디렉터리를 공유해서 개발을 진행하는 경우에는 로컬 개발 환경이 서로 다른데, 이 경우 정보가 각 개발 도구의 메타 정보에 포함되어 있어서 문제가 생기는 경우가 있습니다.   
그래들의 idea와 eclipse 같은 Task를 활용하면 각 개발 도구의 메타 정보를 생성할 수 있으므로 소스 공유 시 로컬 환경에 따른 충돌을 방지하고 개발을 진행할 수 있습니다.     
엔터프라이즈 빌드 자동화를 위한 Gradle - 한빛 미디어


/project/build.gradle

```gradle
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
