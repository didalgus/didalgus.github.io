---
layout: post
title:  "How to compile java"
date:   2019-07-17 17:30:00 +0900
abstract: ""
tags: [java]
image:
toc: true
categories: JAVA
---

멀티 Thread 테스트중 동일 클래스를 여러개 띄울 일이 생겼습니다.    
IntelliJ 로 실행하면 1개만 뜨더군요.  
하나 더 띄울려고 하면 기존 쓰레드를 중지하라고 안내창이 떠서 CLI로 여러개를 띄워야겠다 싶었지요.


![IntelliJ]({{ site.url }}/assets/article_images/2019-07-22-How-to-compile-JAVA/2019-07-23-intellij.png)

Java 개발자가 CLI Java 실행 방법을 다 알까?  
IDE에 익숙한 그대여, 아는가?  
(동료 개발자에게 물었더니 IDE 에서 우클릭해서 Run 하라고 알려줌;;)

<br>


## Compile


가볍게 몸풀기 해볼까요?  

프로젝트 생성후 `src` 디렉토리에 `Hello.java` 파일을 생성합니다.  

```java
public class Hello {

	public static void main(String[] args) {
		System.out.println("Hello World!");
	}
}
```

### javac

동일 `src` 디렉토리에서 `javac` 명령을 수행해서 `src` 디렉토리에 binary 파일 생성~

```bash
$ javac Hello.java

$ ls
Hello.java
Hello.class
```

### java

동일 `src` 디렉토리에서 `java` 명령으로 실행해볼까요?

```bash
$ java Hello

Hello World!
```
잘되네요.  

### javap

그럼 `javap` 명령으로 역컴파일(Decompile) 해볼까요?


```bash
$ javap Hello > Hello.javap
```
디컴파일한 파일 내용을 보니 디컴파일 했다는 `Compiled from` 정보가 추가되어 있네요.

```java
Compiled from "Hello.java"
public class Hello {
  public Hello();
  public static void main(java.lang.String[]);
}
```

IDE 에서 바이너리 파일을 열면 CLI 명령어 보다 깔끔하게 자동으로 역컴파일 해주지요. (ex. IntelliJ)

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

public class Hello {
    public Hello() {
    }

    public static void main(String[] var0) {
        System.out.println("Hello World!");
    }
}
```


## Package Compile


![java logo]({{ site.url }}/assets/article_images/2019-07-22-How-to-compile-JAVA/u15-java-logo.png)


프로젝트 생성후 `./src/lesson/`디렉토리에 `Hello.java` 파일을 생성합니다.  

```java
package lesson;

public class Hello {

	public static void main(String[] args) {
		System.out.println("Hello World!");
	}
}
```

### javac

그럼 패키지 구성된 파일은 어떻게 컴파일 하면 될까요?  
사실 위에 기본적인 명령과 동일합니다. 옵션은 취향껏(?) 넣으시면 되지요.  

<br>

javac -d `클래스파일 빌드경로` `원본소스1 경로` `원본소스2 경로` ...  
javac -d `클래스파일 빌드경로` `소스경로/*.java`

<br>

매뉴얼을 살펴보았습니다.  
 -d 옵션없이 빌드 하는 경우 명령을 실행하는 위치에 바이너리 파일이 생성됩니다.  

```bash
$ man javac

Standard Options
   -classpath classpath
          Sets  the  user  class path, overriding the user class path in the CLASSPATH environment variable.  If neither CLASSPATH or -classpath is specified,
          the user class path consists of the current directory.  See Setting the Class Path for more details.

          If the -sourcepath option is not specified, the user class path is searched for both source files and class files.


   -d directory
          Sets the destination directory for class files.  The destination directory must already exist; javac will not create the destination directory.   If
          a class is part of a package, javac puts the class file in a subdirectory reflecting the package name, creating directories as needed.  For example,
          if you specify -d /home/myclasses and the class  is  called  com.mypackage.MyClass,  then  the  class  file  is  called  /home/myclasses/com/mypack-
          age/MyClass.class.

          If -d is not specified, javac puts the class file in the same directory as the source file.

          Note: The directory specified by -d is not automatically added to your user class path.
```



경로는 프로젝트 `/my-project/` root 디렉토리로 이동합니다.

```bash
$ javac -d ./bin ./src/lesson/Hello.java
  =
$ javac -d ./bin ./src/lesson/*.java
```

bin 디렉토리에 가서 볼까요?  
비어있던 `bin` 디렉토리 하위에 `lesson` 디렉토리와 `Hello.class` 파일이 생성되었네요.

```bash
$ ll ./bin/lesson/

-rw-r--r--  1 we  staff   425B  7 23 11:34 Hello.class
```


### java


"에러를~ 피하고~ 싶었어~ 아무리 애를써도~"

```bash
$ java Hello

오류: 기본 클래스 Hello을(를) 찾거나 로드할 수 없습니다.
```

명령어 기본 사용법입니다.  
java -classpath `클래스파일경로` `패키지명.클래스이름`  

<br>

`클래스파일 빌드경로`로 지정한 `bin` 디렉토리로 이동합니다.  
패키지 파일을 실행해 볼까요?  

```bash
$ java lesson/Hello
Hello World!

$ java lesson.Hello
Hello World!
```

패키지명으로 호출해도 되고 디렉토리경로로 호출해도 되는군요.  

```bash
$ man java

    -classpath classpath
    -cp classpath
          Specifies a list of directories, JAR archives, and ZIP archives to search for class files.  Class path entries are separated by colons (:). Specifying -classpath or -cp overrides any  setting  of
          the CLASSPATH environment variable.

          If -classpath and -cp are not used and CLASSPATH is not set, the user class path consists of the current directory (.).

```
`bin` 디렉토리로 이동하지 않고 `classpath` 를 지정해서 실행하는 방법도 있습니다.

실행해볼까요?  

```bash
$ java -classpath ./bin/ lesson.Hello
Hello World!
```
