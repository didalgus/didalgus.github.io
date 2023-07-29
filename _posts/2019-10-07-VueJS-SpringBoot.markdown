---
layout: post
title:  "Vue.js with SpringBoot"
series:
date:   2019-10-07 17:30:00 +0900
abstract: ""
tags: [VueJS, SpringBoot, JAVA, featured]
image:
toc: true
categories: JAVA
---

계속 업데이트 할게요~ 뿅!

## 목표

SpringBoot 프로젝트에  
nodejs 기반 Vue.js 프로젝트를 생성하여  
webpack으로 SpringBoot 정적 리소스를 생성해 봅시다.  
<br>
나는요~ Full Stack 개빨쫘(가 될테얏)~

## 환경

* macOS Mojave 10.14.6
* IntelliJ IDEA 2019.1
* [github](https://github.com/didalgus/vuejs-springboot-jpa){: target="_blank" }
* node : 10.15.3 LTS(Long Term Support, 장기 지원 버전)
* npm : 6.4.1
* webpack : 3.12.0

## Project with IntelliJ

기본 틀을 생성해 보아요. 편한 도구를 이용하세요.  
CLI도 좋구요. IDE도 좋아요.  
한땀한땀 손바느질도 좋구요. 미싱으로 드르륵도 좋아요. (나의 취미는 쏘잉~)  
<br>  

손에 익은 IntelliJ 로 만들겠습니다.

`IntelliJ IDEA > File > New > Project` 메뉴로 이동 후  
`Spring Initializr` 창에서 필요한 것들 줍줍~ 챙기세요.

![New Project]({{ site.url }}/assets/article_images/2019-10-07-VueJS-SpringBoot/2019-10-07-001.png)

![Spring Initializr]({{ site.url }}/assets/article_images/2019-10-07-VueJS-SpringBoot/2019-10-07-002.png)

기본 구조가 생성되었네요.

### gitignore

![gitignore.io]({{ site.url }}/assets/article_images/2019-10-07-VueJS-SpringBoot/gitignoreio.svg)
저장소에 올리기전에 .gitignore 파일을 갈무리 해줄까요?  
[gitignore.io](https://www.gitignore.io) 사이트에서 .gitignore 파일 내용을 생성~  
주소를 공유할 수도 있지요.  
요렇게~ [https://www.gitignore.io/api/git,java,vuejs,gradle,eclipse,intellij](https://www.gitignore.io/api/git,java,vuejs,gradle,eclipse,intellij){: target="_blank" }    
깨끗한 저장소를 위하여~!  

<br>

### Lombok

롬복 라이브러리 사용을 위해   
Lombok Requires Annotation Processing  
"Settings > Build > Compiler > Annotation Processors"  
메뉴로 이동 후
[v] Enable annotation prodessing
위 항목 선택해주세요.   

## Git Repository

개인깃헙에 저장소를 생성하고 push 할께요.  

```bash
$ cd ~/source/didalgus/vuejs
$ git init
$ git config user.name willow
$ git config user.email didalgus@gmail.com
$ git config --local --list    # 설정확인
$ git remote add origin https://github.com/didalgus/vuejs-springboot-jpa.git
$ git remote -v   # 설정확인
$ git add -A
$ git commit -m "inital"
$ git push -u origin master
```

## NodeJS

이미 node.js 설치 되어 있으시면 넘어가주세요.  
[node.js](https://nodejs.org/en/){: target="_blank" } 사이트에 가서 개발환경에 맞게 설치해주세요.
<br>  

`vue-cli` 는 Vue 전용 CLI 도구예요.  
Node.js에 기반을 둔 Vue.js 프로그램의 빠른 시작과 구현을 위해 빌드 설정이 포함된 프로젝트를 생성해준답니다.

```bash
$ sudo npm install -g vue-cli
npm WARN deprecated coffee-script@1.12.7: CoffeeScript on NPM has moved to "coffeescript" (no hyphen)
/usr/local/bin/vue-init -> /usr/local/lib/node_modules/vue-cli/bin/vue-init
/usr/local/bin/vue -> /usr/local/lib/node_modules/vue-cli/bin/vue
/usr/local/bin/vue-list -> /usr/local/lib/node_modules/vue-cli/bin/vue-list
+ vue-cli@2.9.6
added 239 packages from 206 contributors in 6.974s
```

설치경로가 일반계정권한이 없는 경로인지라 root 계정으로 설치 해주었습니다.   
```bash
$ ls /usr/local/lib/node_modules/
npm/     vue-cli/     webpack/
```

로컬에 설치된 버전들입니다.  
```bash
$ node -v
v10.15.3

$ npm -v
6.4.1

$ webpack -v
3.12.0
```

## VueJS

이제 Node.js 기반으로 Vue.js 프로젝트를 설치해볼까요?  
<br>
Springboot 최상위 디렉토리로 이동하세요.  
webpack을 사용할 예정이라 아래와 같은 명령을 실행하였습니다.  
대화형 설치 스크립트예요.  
필요여부를 선택해주면 자동 생성된답니다.  

```bash
~/source/didalgus/vuejs (master) $ vue init webpack frontend

? Project name frontend
? Project description A Vue.js Project
? Author willow <didalgus@gmail.com>
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Set up unit tests Yes
? Pick a test runner noTest
? Setup e2e tests with Nightwatch? No
? Should we run `npm install` for you after the project has been created? (recommended) npm

   vue-cli · Generated "frontend".
```

필요한 구조가 준비되었습니다.  
친절하게도 `package.json` 파일을 보니 `scripts` 속성에 필요한 명령까지 추가되어 있네요.  

```bash
~/source/didalgus/vuejs/frontend (master) $ vi package.json
...
  "scripts": {
    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
    "start": "npm run dev",
    "test": "npm run unit",
    "lint": "eslint --ext .js,.vue src test/unit",
    "build": "node build/build.js"
  },
```

이제 설치해볼까요?

```bash
~/source/didalgus/vuejs/frontend (master) $ npm install
audited 12299 packages in 6.498s
found 10 vulnerabilities (6 moderate, 4 high)
  run `npm audit fix` to fix them, or `npm audit` for details
```
`package.json` 파일 설정을 참고하여 설치합니다.  
Nodejs 기반으로 된 Vue.js 프로젝트를 시작해보아요.   


```bash
~/source/didalgus/vuejs/frontend (master) $ npm start

> frontend@1.0.0 start ~/source/didalgus/vuejs/frontend
> npm run dev


> frontend@1.0.0 dev ~/source/didalgus/vuejs/frontend
> webpack-dev-server --inline --progress --config build/webpack.dev.conf.js

 12% building modules 23/31 modules 8 active ...e/didalgus/vuejs/frontend/src/App.vue{ parser: "babylon" } is deprecated; we now treat it as { parser: "babel" }.
 95% emitting                                                                        

 DONE  Compiled successfully in 3880ms       18:06:03

 I  Your application is running here: http://localhost:8080
```  

브라우저에서 확인해보아요~  

![vue.js with node.js]({{ site.url }}/assets/article_images/2019-10-07-VueJS-SpringBoot/2019-10-07-003.png)

여기까지 오셨다면 반이상 오셨습니다.  
수고하셨습니다~ 짝짝짝!



## VueJS 설정

저의 목표는 Springboot 프로젝트와 Node.js (Vue.js)프로젝트를 함께 구현하는 것이지요.
그래서 맞춤 작업 들어가겠습니다.  
<br>
Springboot 프로젝트가 8080포트를 사용하고 있으니 Vue.js 프로젝트는 3000포트로 설정하겠습니다.  

`/frontend/config/index.js` 파일을 아래와 같이 수정해주세요.

```javascript
module.exports = {
  dev: {
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {                    
      "**": "http://localhost:8080"  // 추가
    },

    host: 'localhost',
    port: 3000,                      // 수정 (8080 > 3000)
```


설정 수정후 Vue.js 를 띄워볼께요.

```bash
~/source/didalgus/vuejs/frontend (master) $ npm start

> frontend@1.0.0 start ~/source/didalgus/vuejs/frontend
> npm run dev


> frontend@1.0.0 dev ~/source/didalgus/vuejs/frontend
> webpack-dev-server --inline --progress --config build/webpack.dev.conf.js

 12% building modules 22/31 modules 9 active ...e/didalgus/vuejs/frontend/src/App.vue{ parser: "babylon" } is deprecated; we now treat it as { parser: "babel" }.
 95% emitting                                                                        

 DONE  Compiled successfully in 3897ms                                 11:37:20

 I  Your application is running here: http://localhost:3000
```

브라우저에서 확인~  
[http://localhost:3000](http://localhost:3000)

![localhost]({{ site.url }}/assets/article_images/2019-10-07-VueJS-SpringBoot/2019-10-08-001.png)

## webpack

이제 Springboot에서 사용할 정적파일들을 webpack을 이용해서 생성하겠습니다.

`/frontend/config/index.js` 파일에 build 속성 하위에 경로를 springboot 경로로 변경해주세요.
```javascript
  build: {
    // Template for index.html
    index: path.resolve(__dirname, '../../src/main/resources/templates/index.html'),

    // Paths
    assetsRoot: path.resolve(__dirname, '../../src/main/resources/static'),
```

webpack(+babel)으로 build 합니다.  

```bash
~/Documents/source/didalgus/vuejs/frontend (master) $ npm run build

> frontend@1.0.0 build /Users/we/Documents/source/didalgus/vuejs/frontend
> node build/build.js

Hash: 2fcf9801bf1a223d84f8
Version: webpack 3.12.0
Time: 7628ms
                                                  Asset       Size  Chunks             Chunk Names
               static/js/vendor.e00e51762311e00a2108.js     121 kB       0  [emitted]  vendor
                  static/js/app.b22ce679862c47a75225.js    11.6 kB       1  [emitted]  app
             static/js/manifest.2ae2e69a05c33dfc65f8.js  857 bytes       2  [emitted]  manifest
    static/css/app.30790115300ab27614ce176899523b62.css  432 bytes       1  [emitted]  app
static/css/app.30790115300ab27614ce176899523b62.css.map  797 bytes          [emitted]  
           static/js/vendor.e00e51762311e00a2108.js.map     610 kB       0  [emitted]  vendor
              static/js/app.b22ce679862c47a75225.js.map    22.2 kB       1  [emitted]  app
         static/js/manifest.2ae2e69a05c33dfc65f8.js.map    4.97 kB       2  [emitted]  manifest
                                ../templates/index.html  510 bytes          [emitted]  

  Build complete.

  Tip: built files are meant to be served over an HTTP server.
  Opening index.html over file:// won't work.
```

파일이 잘 생성되었네요.  

```bash
~/source/didalgus/vuejs (master) $ ll src/main/resources/templates/
total 8
-rw-r--r--  1 we  staff   510B 10  8 11:57 index.html

~/source/didalgus/vuejs (master) $ ll src/main/resources/static/static/
total 0
drwxr-xr-x  4 we  staff   128B 10  8 11:57 css/
drwxr-xr-x  8 we  staff   256B 10  8 11:57 js/
```

## SpringBoot

Springboot 를 실행하여 잘 적용되었는지 확인해볼까요?  
<br>

gradlew 로 빌드합니다.  

```bash
~/source/didalgus/vuejs (master) $ ./gradlew clean build

BUILD SUCCESSFUL in 2s
5 actionable tasks: 5 executed
```

springboot 실행
```bash
~/source/didalgus/vuejs (master) $ ./gradlew bootRun

> Task :bootRun
...
BUILD SUCCESSFUL in 3s
3 actionable tasks: 1 executed, 2 up-to-date
```

브라우저로 확인해볼까요?

![springboot]({{ site.url }}/assets/article_images/2019-10-07-VueJS-SpringBoot/2019-10-08-002.png)

음~  
Node.js 로 띄운 Vue.js 와 화면이 같아서 그런지 차이점을 모르겠나요?  

그럴땐 소스보기를 해보면 확실해지지요.


![springboot]({{ site.url }}/assets/article_images/2019-10-07-VueJS-SpringBoot/2019-10-08-004.png)

어떠시나요?  
그렇담 None.js 로 띄운 Vue.js 의 소스는 webpack 과정을 거치기 전 원본 소스이겠지요?  

![springboot]({{ site.url }}/assets/article_images/2019-10-07-VueJS-SpringBoot/2019-10-08-003.png)
