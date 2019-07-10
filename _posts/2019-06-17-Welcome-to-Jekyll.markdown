---
# 수정하지 않습니다.
layout: post
# 포스트 제목을 기재합니다.
title:  "Welcome to Jekyll!"
# 저자를 입력합니다. 리스트형태로 복수의 저자를 추가할 수 있습니다.
#author: ["willow"]
# 포스트 공개일을 입력합니다. 파일명의 날짜와 일치해야 하며 미래의 날짜로 입력된 포스트는 공개되지 않습니다.
date:   2019-06-17 16:30:01 +0900
# 블로그 메인페이지에 썸네일과 함께 노출될 텍스트를 입력합니다. 일정 길이를 초과하면 잘려서 표시됩니다.
abstract: "Welcome to Jekyll!"
# 태그를 입력합니다.
tags: featured
# 대표 이미지를 입력합니다. 이미지 업로드 위치는 아래에 기술합니다.
image:
# 포스트의 초안 여부를 입력합니다. "no" 로 입력할 경우 공개됩니다.
#draft: "yes"
toc: true
categories: mediator feature
last_modified_at: 2019-06-19T14:23:00+09:00
---


![Jekyll logo]({{ site.url }}/assets/article_images/2019-06-17-Welcome-to-Jekyll/logo-2x.png)

[서비큐라 기술 블로그](https://subicura.com) 쥔장님의 허락하에 github 저장소 fork 하여 스킨 적용하였습니다.  
감사드립니다!

Jekyll + github 를 이용하여 정적 웹 사이트를 만들어 보았습니다.  
방법은 다양합니다. 구글링을 먼저 하길 권해봅니다.  
제가 설치한 순서를 정리해보았습니다.  

## github 저장소 생성
본인 계정에 저장소를 생성합니다.  
저장소 이름 형식은 `계정명.github.io` 입니다.
```
ex) didalgus.github.io
```

## Local PC 에 jekyll 설치

### ENV

ruby 버전
{% highlight bash %}
$ ruby -v
ruby 2.3.7 p456 (2018-03-28 revision 63024) [universal.x86_64-darwin18]
{% endhighlight %}

### Install jekyll

[jekyll 한국 매뉴얼](http://jekyllrb-ko.github.io/docs/home/)

{% highlight bash %}
$ gem install bundler jekyll
$ jekyll new my-awesome-site
$ cd my-awesome-site
$ bundle exec jekyll serve
{% endhighlight %}

### Apply theme

[jekyll 테마 사이트](http://jekyllthemes.org/themes/mediator/)에 가셔서 마음에 드는 테마를 골라서 다운받으시거나
깃헙에서 해당 [테마 저장소](https://github.com/dirkfabisch/mediator)를 Fork 하셔도 됩니다.  
저같은 경우 new 명령어로 생성한 기본 테마(minima)에 원하는 테마(mediator)를 덧입혔습니다.

{% highlight bash %}
$ cd ./theme-mediator
$ cp _includes _layouts _sass assets css favicon.ico index.html ../my-awesome-site
$ rm index.md
{% endhighlight %}


최초 생성시 사용된 테마 _config.yml 와 mediator 테마의 _config.yml 를 비교하여 필요한 설정을 추가하였습니다.



## github remote push

! git 계정이 하나이고 global 설정을 하신 경우 생략하세요.  
저는 계정이 여러개라 아래 과정을 추가하였습니다.
{% highlight bash %}
$ cd my-awesome-site
$ git init
$ git config user.name willow
$ git config user.email didalgus@gmail.com
$ git config --local --list
{% endhighlight %}

git 저장소 설정 및 push
{% highlight bash %}
$ git remote add origin https://github.com/didalgus/didalgus.github.io.git
$ git remote -v
$ git add -A
$ git commit -m 'init'
$ git push origin master
{% endhighlight %}

## plugins  
기본 mediator 테마를 적용 후 한개씩 플러그인을 추가하였습니다.  

```
$ bundle install
```
플러그인 추가시 install 명령을 실행해줍니다.

### jekyll-toc

목차 기능을 추가하였습니다. 서비큐라님의 css 와 js 를 적용하였습니다. (공유정신 쵝오!)  
github [jekyll-toc](https://github.com/toshimaru/jekyll-toc) 매뉴얼을 참고하세요.  



![jekyll-toc]({{ site.url }}/assets/article_images/2019-06-17-Welcome-to-Jekyll/2019-07-10.png)



>Add jekyll-toc plugin in your site's `Gemfile`, and run `bundle install`.
>
> ```ruby
> gem 'jekyll-toc'
> ```
>
>Add jekyll-toc to the `gems:` section in your site's `_config.yml`.
>
>```yml
>plugins:
>  - jekyll-toc
>```
>
>Set `toc: true` in posts for which you want the TOC to appear.
>
>```yml
>---
>layout: post
>title: "Welcome to Jekyll!"
>toc: true
>---
>```
>

locally 동작은 잘되나 github pages 에서 동작되지 않아 갓구글에서 검색해보니   
[jekyll-toc 개발자의 답변](https://github.com/toshimaru/jekyll-toc/issues/29) 에 따르면 github pages 에서 제공되는 플러그인외는 제한하므로 정적웹페이지를 저장소에 upload 해서 사용하라고 하네요. (귀찮아!)  
이기능이 매우 매력적이나 유지보수 용이를 위해서 과감히 포기! (눙물을 머금고~)  

github pages 에서 기본 제공하는 [플러그인 목록](https://help.github.com/en/articles/configuring-jekyll-plugins)을 확인 하세요.


### facebook-comments

[facebook for developers](https://developers.facebook.com/docs/plugins/comments/) 로그인 후 **댓글 플러그인 코드 생성 도구** 에서 [코드받기] 버튼을 클릭하여 코드를 받아 적용합니다.

### google analytics
[Google Marketing Platfom](https://marketingplatform.google.com/about/analytics/) 로그인 후 추적 ID 발급하여 적용합니다.   
(개발자들이란.. 설명에 인색하다.. 사실 귀찬.. 구글링하세요 ㅠ)

## etc

### post 작성 후 게시되지 경우

post 작성 후 글이 보이지 않아서 삽질을 좀 했습니다.ㅠ  
_config.yml 에 timezone 설정만 하면 post 가 게시 되는줄 알았는데 게시되지 않았습니다.

```
timezone: Asia/Seoul
```

post 작성시 `+0900` 를 추가해주면 해결! (언제나 알고 나면 허탈~)

```
date:   2019-06-17 16:30:01 +0900
```
