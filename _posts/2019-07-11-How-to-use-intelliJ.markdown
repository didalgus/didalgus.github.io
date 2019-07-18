---
layout: post
title:  "How to use intelliJ IDEA"
series: 2/2
date:   2019-07-11 16:30:00 +0900
abstract: ""
tags: [IntelliJ, Tools]
image:
toc: true
categories: Tools IntelliJ
last_modified_at: 2019-07-12T16:30:00+09:00
---

![intellij logo]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/intellij_logo.png)


* [Nice to meet IntelliJ IDEA]({% post_url 2019-07-10-Nice-to-meet-IntelliJ %}) <span class="series">SERIES 1/2</span>
* How to use intelliJ IDEA  ✓<span class="series">SERIES 2/2</span>

이번에는 유용하게 사용하는 팁 모음~! 되겠습니다.  
차차 분류를 정돈하고 내용을 더하겠습니다.  
(제목을 영어로 쓴 이유는 영어로 써야 있어보인다는 남푠님의 의견을 수용하였습니다. ㅋ)  


# USE

## Expand All

어디까지나 개인의 취향이 반영된 포스팅입니다.  
개발 룰에 사용하지 않는 import 정리 필수 인경우 파일을 열었을때 사용하지 않는 import 를 눈으로 확인하고 정리하는 패턴이 반복되는거예요.  
이런경우 매번 import 부분을 펼쳐는 저를 보며..

차라리 스크롤링을 하겠어! 클릭 귀찮아!!  
![Source Code]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/expand-all-1.png)

Preferences.. (⌘,) > Editor > General > Code Folding : [v] Imports 해제  
![Preferences > Code Folding]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/expand-all-2.png)

기본값은 접혀있고 가끔 펼치고 싶을때는 단축키를 이용하세요~  
Keymap 설정에 따라 단축키가 다르답니다.  
Preferences.. (⌘,) > Keymap  
![Preferences > keymap]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/expand-all-3.png)

## Shortcut

자주 쓰는 단축키 변경해 보아요~  
Preferences.. (⌘,) > Keymap > 명령라인 우클릭 > Add Keyboard Shorcut  
![Shortcut]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/shortcut.png)


## Tool Window
실수로 창 닫아버리고는 헤매지 말자!  
View > Tool Windows > Maven  
![Maven Projects]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/tool-window.png)


## Find Usages

어디어디에서 쓰이나~?   
![Find Usages]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/find-usages.png)

## Show memory indicator
Preferences.. (⌘,) > Appearance & Behavior > Appearance   
![Show memory indicator]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/show-memory-indicator-1.png)

우측 하단에 메모리 사용량이 보이는군요.  
![Show memory indicator]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/show-memory-indicator-2.png)

## TDD
Generate... : 생성하다  
![keymap]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/tdd-1.png)

### Test...
테스트코드 바로 만들어보아요.
![Test...]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/tdd-2.png)

### toString()
toString() 일일히 쓰기 귀찮;;
![toString()]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/tdd-3.png)

## Debug Mode
디버그 모드에서 메서드 호출 (너란녀석.. 양파같은 녀석..)  

![debug mode]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/debug-1.png)


## Git

Editor Window 에서 선택한 파일의 git history 를 보고자 할때   
마우스 우클릭 > Git > Show History   
![git]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/git-1.png)

파일이 엉망(다양한 의미)이 되었을때 원격에서 다시 파일 받고싶을때 `Get` 해서 받는 방법도 있지요.  
콘솔에서 다시 받기 귀찮;;  
![git]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/git-2.png)


## Apllication Servers

Preferences.. (⌘,) >  Build, Executions, Deployment -> Application Servers > "+" > JBoss Server

![jboss]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/jboss-1.png)

## Update Resource

이미지나 css 변경시 WAS에 반영할때 WAS 리스타트 하지 않는 방법이예요.  
Apllication Servers Tab 에서 파란색 화살표를 클릭하면 저렇게 선택할 수 있게 나와요.  
WAS별 옵션이 달리 나오는것 같아요. 아래이미지는 Jboss 설정시 나오는 옵션이예요.

![jboss]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/update-1.png)

서버 상세 설정에도 옵션이 있네요.  
On frame deactivation : 핫디플로이 기능!! 애플리케이션이 대기상태일때 변경사항을 갱신하는데 이걸 설정하면 IDE 가 바빠요;;  
처음에는 신기해서 자주썼는데 너무 IntelliJ 가 무거워지길래 안쓰게 되었네요.
![jboss]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/update-2.png)


## UML

구축된 서비스 분석할때 종종 들여다보게 되는 UML 이예요.  
심플한 예시 이미지예요. 복잡한경우 화면에 가득한 경우도 있어요. @0@
![UML]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/uml-2.png)

확인하고자하는 클래스파일에서 마우스 우클릭  
![UML]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/uml-1.png)

## Export
맥북 업글! (아리가또!)  
설정 옮겨야징! 우와~ 언제적 버전;  
![UML]({{ site.url }}/assets/article_images/2019-07-11-How-to-use-intelliJ/export.png)
