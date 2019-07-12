---
layout: post
title:  "Nice to meet IntelliJ IDEA [SERIES 1/2]"
date:   2019-07-10 17:30:00 +0900
abstract: ""
tags: [IntelliJ, Tools]
image:
toc: true
categories: Tools IntelliJ
last_modified_at: 2019-07-12T17:30:00+09:00
---


![intellij logo]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/intellij_logo.png)

멍때리느니 뭔가 생산적인 일을 하자!
짬짬히 업데이트 하겠습니다.

# 운영 환경
- macOS Mojave 10.14.5  
- intelliJ IDEA 2019.1 (Ultimate Edition)
- intelliJ Keymap : Mac OS X 10.5+

# UI

intelliJ 사용시 `보여지는것`에 관한 것들에 대한 포스팅입니다.  
전부(단축키, 부가기능) 다룰려고 했더니 오히려 가독성이 떨어져서 분리하게 되었습니다.  

![Material Oceanic + Rainbow Brackets]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/rainbow-brackets-sample.png)

`Material Oceanic` 테마에 `Rainbow Brackets` 플러그인 적용한 화면입니다.


## 1. Theme


![Material Theme UI]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/material-theme.png)

기본 테마인 `Darcula`테마를 사용하는 중에  
2% 부족함을 느끼는 중에 다른분 테마를 보고 설치해보았습니다.  
[인텔리제이 아이콘](https://www.jetbrains.com/help/idea/symbols.html) 보다 훨씬 가독성이 좋네요.  

적용하고자 하는 테마는 [Material Theme UI](https://plugins.jetbrains.com/plugin/8006-material-theme-ui/versions) 중 `Material Oceanic` 입니다.


### install plugins
Preferences.. (⌘,) > Plugins > Marketplace  이동 > `Material Theme` 검색 후 설치합니다.

### editor window
설치 후 Editor 윈도우에 테마가 적용되지 않는 경우   
Preferences.. (⌘,) > Editor > Color Scheme 에서 Scheme 를 `Material Theme`로 선택 해주시면 됩니다.

저는 개인적으로 `Material Oceanic` 가 마음에 드네요.  


### reset theme
Q: 테마 지겹다! 나 돌아갈래!  
A: 플러그인 지우시면 됩니당~   
<br>
Q: 지우기는 아깝다~   
A: 그런경우   
Preferences.. (⌘,) > Appearance & Behavior > Appearance 이동 후 Theme 변경  
Preferences.. (⌘,) > Editor > Color Scheme  에서 Scheme 변경

## 2. Plugins

![Rainbow Brackets]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/rainbow-brackets.png)


무한 괄호의 늪에서 정신차리는데 도움이 되는 [Rainbow Brackets](https://plugins.jetbrains.com/plugin/10080-rainbow-brackets) 되시겠습니다.

### install plugins
Preferences.. (⌘,) > Plugins > Marketplace  이동 > `Rainbow Brackets` 검색 후 설치합니다.


## 3. Drectory Window
개인적으로 좋아하는 디렉토리창 옵션 설정입니다. (어디까지나 개취입니다.)  
![Rainbow Brackets]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/diretory-window.png)


프로젝트 타입에 따라 디렉토리 창 옵션도 달리 노출됩니다.  
ex. `Hide Empty Middle Pack`, `Show Modules`

{:.table.table-key-value-60}
|선택 | 옵션 | 설명 |
|---|---|---|
|[ ] | Flatten Packages  |  /src/main/java[test]/하위패키지 : 패키지단위 노출
|[v] | Compact Middle Packages   |  /src/main/java[test]/하위패키지 : 축약 노출
|[ ] | Show Modules  | 모듈플젝시 일반파일 비노출, 모듈단위로만 노출
|[v] | Show Excluded Files  |  예외 파일 노출 설정
|[v] | Group Tabs  |  ..  |  


## 4. Editor Tabs


파일 수정한 경우 상단 Tab에 표시 기능 추가해 보아요.  
Preferences.. (⌘,) > Editor > General > Editor Tabs 메뉴 이동  
[v] Mark modified (*)  
![Editor Tabs]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/editor-tab-2.png)



짜잔~ 연필모양이 생기네요~  
![Editor Tabs]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/editor-tab-1.png)

## 5. Editor Font

에디터 윈도우 폰트.. 이것은 개발자의 오류(오타)를 줄여주는 중요한 포인트!  
Preferences.. (⌘,) > Editor > Font


![Editor Font]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/editor-font.png)


## 6. Editor Colors

기본 `Darcula`테마 일때 색상 변경해서 사용했어요.  
intelliJ 버전별에 따라 categories 네이밍이 바뀌었네요. (큰 구조는 변화 없어 보이네요.)  
<br>

Preferences.. (⌘,) > Editor > Colors & Fonts > General  : intelliJ IDEA 2019.1 이하 버전   
Preferences.. (⌘,) > Editor > Color Scheme > General : intelliJ IDEA 2019.1

![Editor Font]({{ site.url }}/assets/article_images/2019-07-10-Nice-to-meet-IntelliJ/editor-colors.png)
