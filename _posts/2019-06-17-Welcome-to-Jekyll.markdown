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
tags: [jekyll, install]
# 대표 이미지를 입력합니다. 이미지 업로드 위치는 아래에 기술합니다.
image:
# 포스트의 초안 여부를 입력합니다. "no" 로 입력할 경우 공개됩니다.
#draft: "yes"
toc: true
categories: Tech
last_modified_at: 2020-08-31T23:40:00+09:00
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

```bash
$ ruby -v
ruby 2.3.7 p456 (2018-03-28 revision 63024) [universal.x86_64-darwin18]
```

### Install jekyll

[jekyll 한국 매뉴얼](http://jekyllrb-ko.github.io/docs/home/)

```bash
$ gem install bundler jekyll
$ jekyll new my-awesome-site
$ cd my-awesome-site
$ bundle exec jekyll serve
```

### Apply theme

[jekyll 테마 사이트](http://jekyllthemes.org/themes/mediator/)에 가셔서 마음에 드는 테마를 골라서 다운받으시거나
깃헙에서 해당 [테마 저장소](https://github.com/dirkfabisch/mediator)를 Fork 하셔도 됩니다.  
저같은 경우 new 명령어로 생성한 기본 테마(minima)에 원하는 테마(mediator)를 덧입혔습니다.

```bash
$ cd ./theme-mediator
$ cp _includes _layouts _sass assets css favicon.ico index.html ../my-awesome-site
$ rm index.md
```


최초 생성시 사용된 테마 `_config.yml` 와 mediator 테마의 `_config.yml` 를 비교하여 필요한 설정을 추가하였습니다.



## github remote push

:cactus: git 계정이 하나이고 global 설정을 하신 경우 생략하세요.  
저는 계정이 여러개라 아래 과정을 추가하였습니다.

```bash
$ cd my-awesome-site
$ git init
$ git config user.name willow
$ git config user.email didalgus@gmail.com
$ git config --local --list
```

git 저장소 설정 및 push  

```bash
$ git remote add origin https://github.com/didalgus/didalgus.github.io.git
$ git remote -v
$ git add -A
$ git commit -m 'init'
$ git push -u origin master
```

## plugins  
기본 mediator 테마를 적용 후 한개씩 플러그인을 추가하였습니다.  

```bash
$ bundle install
```

플러그인 추가시 install 명령을 실행해줍니다.  

적용한 플러그인 전체 목록입니다.   
`didalgus.github.io/_config.yml`  


```bash
plugins:
  - jemoji
  - jekyll-sitemap
  - jekyll-feed
  - jekyll-tocÂ
  - jekyll-paginate
```

### jekyll-paginate

```bash
$ sudo gem install jekyll-pagination
$ bundle install
```

### jekyll-toc

목차 기능을 추가하였습니다. 서비큐라님의 css 와 js 를 적용하였습니다. (공유정신 쵝오!)  
github [jekyll-toc](https://github.com/toshimaru/jekyll-toc) 매뉴얼을 참고하세요.  



![jekyll-toc]({{ site.url }}/assets/article_images/2019-06-17-Welcome-to-Jekyll/2019-07-10.png)



>Add jekyll-toc plugin in your site's `Gemfile`, and run `bundle install`.

{% highlight ruby linenos %}
gem 'jekyll-toc'
{% endhighlight %}

>Add jekyll-toc to the `gems:` section in your site's `_config.yml`.

{% highlight yml linenos %}
plugins:
  - jekyll-toc
{% endhighlight %}

>Set `toc: true` in posts for which you want the TOC to appear.


{% highlight yml linenos %}
---
layout: post
title: "Welcome to Jekyll!"
toc: true
---
{% endhighlight %}

<br>  


locally 동작은 잘되나 github pages 에서 동작되지 않아 갓구글에서 검색해보니   
[jekyll-toc 개발자의 답변](https://github.com/toshimaru/jekyll-toc/issues/29) 에 따르면 github pages 에서 제공되는 플러그인외는 제한하므로 정적웹페이지를 저장소에 upload 해서 사용하라고 하네요. (귀찮아!)  
이기능이 매우 매력적이나 유지보수 용이를 위해서 과감히 포기! (눙물을 머금고~)  

github pages 에서 기본 제공하는 [플러그인 목록](https://help.github.com/en/articles/configuring-jekyll-plugins)을 확인 하세요.


### facebook-comments

[facebook for developers](https://developers.facebook.com/docs/plugins/comments/) 로그인 후 **댓글 플러그인 코드 생성 도구** 에서 [코드받기] 버튼을 클릭하여 코드를 받아 적용합니다.  
<br>
자매품~ [facaebook 공유 디버(https://developers.facebook.com/tools/debug/sharing/)  
Open Graph Tag 가 안맞아서 수정했지요.  


### google analytics
[Google Marketing Platfom](https://marketingplatform.google.com/about/analytics/) 로그인 후 추적 ID 발급하여 적용합니다.   
(개발자들이란.. 설명에 인색하다.. 사실 귀찬.. 구글링하세요 ㅠ)


## ETC

### post 작성 후 게시되지 경우

post 작성 후 글이 보이지 않아서 삽질을 좀 했습니다.ㅠ  
`_config.yml` 에 timezone 설정만 하면 post 가 게시 되는줄 알았는데 게시되지 않았습니다.

{% highlight yml linenos %}
timezone: Asia/Seoul
{% endhighlight %}

post 작성시 `+0900` 를 추가해주면 해결! (언제나 알고 나면 허탈~)

{% highlight yml linenos %}
date:   2019-06-17 16:30:01 +0900
{% endhighlight %}

### html, css 변경시 적용안되는 경우

- 로컬 환경에서   
브라우저 캐시 설정이 **실시간적용** 상태인데도 안되는 경우는  
`./my-awesome-site/didalgus.github.io/_site/` 하위 디렉토리를 삭제해 주세요.  
jekyll 이 실행시 자동 생성하는 파일입니다.  

<br>

- 운영 환경(github) 에서   
적용 안되는 경우는 url주소 뒤에 dummy(?v=20190823) 값을 붙여주세요.  

ex) `./my-awesome-site/_includes/header.html`

{% highlight html linenos %}
  <link rel="stylesheet" type="text/css" media="screen" href="{{ "/css/main.css"  | prepend: site.baseurl  }}?v=20190820" />
{% endhighlight %}


### Bundle Update

보안 취약점 안내 메일이 왔네요.  

![mail]({{ site.url }}/assets/article_images/2019-06-17-Welcome-to-Jekyll/security_vulnerability.png)

기존 버전 확인합니다.

`./my-awesome-site/Gemfile.lock`
```
nokogiri (1.10.3)
```

버전을 올려야겠네요.

```bash
$ bundle update
...
Fetching nokogiri 1.10.4
Installing nokogiri 1.10.4 with native extensions
...
```


## Jekyll 심폐소생

[hi macOS]({% post_url 2019-11-05-hi-macOS %}) 에서 이어지는 내용입니다.  
`Mojave 10.14.6` > `Catalina 10.15.1` 업데이트 후 식물인간 지킬을 심폐소생하였습니다.  
<br>
새로 셋팅하는 시나리오입니다. (집에있는 맥북~)  

## ruby

macOS 내장 ruby에서는 지킬이 정상동작하지 않습니다.  
```bash
$ gem install bundler jekyll

mkmf.rb can not find header files for ruby at /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/include/ruby.h

You might have to install separate package for the ruby development
environment, ruby-dev or ruby-devel for example.
```

구글링해보니 [homebrew 로 ruby 설치해서 사용](https://medium.com/faun/macos-catalina-xcode-homebrew-gems-developer-headaches-cf7b1edf10b7)하라고 안내해주는군요.

그래서 [homebrew 설치](https://brew.sh/index_ko.html) 합니다.

```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
..
- Run `brew help` to get started
- Further documentation:
    https://docs.brew.sh

$ ll /usr/local/
total 0
drwxrwxr-x   2 willow  admin    64B 11 30 22:59 Caskroom/
drwxrwxr-x   7 willow  admin   224B 11 30 23:03 Cellar/
drwxrwxr-x   3 willow  admin    96B 11 30 23:03 Frameworks/
drwxrwxr-x  19 willow  admin   608B 11 30 23:01 Homebrew/
...

$ brew -v
Homebrew 2.2.0
```

Ruby 설치합니다.  
Ruby 버전에 따라 환경변수가 달라지네요. (회사맥북은 변수선언이 추가되었네요.)

```bash
$ brew install ruby

...
By default, binaries installed by gem will be placed into:
  /usr/local/lib/ruby/gems/2.6.0/bin

You may want to add this to your PATH.

ruby is keg-only, which means it was not symlinked into /usr/local,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have ruby first in your PATH run:
  echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.bash_profile

For compilers to find ruby you may need to set:
  export LDFLAGS="-L/usr/local/opt/ruby/lib"
  export CPPFLAGS="-I/usr/local/opt/ruby/include"

localhost@willow:/usr/local$ /usr/local/opt/ruby/bin/ruby -v
ruby 2.6.5p114 (2019-10-01 revision 67812) [x86_64-darwin19]

```

PATH 설정을 해야겠네요.

```bash
$ vi ~/.profile

export RUBY_HOME=/usr/local/opt/ruby/bin
export LDFLAGS="-L/usr/local/opt/ruby/lib"
export CPPFLAGS="-I/usr/local/opt/ruby/include"
export GEM_PATH=~/.gem/ruby/2.6.0

export PATH=$RUBY_HOME:$GEM_PATH/bin:$PATH
```

### install jekyll
ruby가 정상 설치되었으니 이제 jekyll 를 살리러 갈까요?

```bash
$ gem install bundler jekyll
...
Post-install message from sass:

Ruby Sass has reached end-of-life and should no longer be used.

* If you use Sass as a command-line tool, we recommend using Dart Sass, the new
  primary implementation: https://sass-lang.com/install

* If you use Sass as a plug-in for a Ruby web framework, we recommend using the
  sassc gem: https://github.com/sass/sassc-ruby#readme

* For more details, please refer to the Sass blog:
  https://sass-lang.com/blog/posts/7828841
```

### jekyll serve

jekyll 이 정상 구동 되었습니다~ ^^  

```bash
$ git clone https://github.com/didalgus/didalgus.github.io.git
$ cd didalgus.github.io.git
$ bundle install
$ bundle exec jekyll serve
```


### etc

친절해라. root 로 실행하지말라고 안내해주네요.

```bash
root# bundle install
Don't run Bundler as root. Bundler can ask for sudo if it is needed, and installing your bundle as root will break this application for all non-root users on this machine.
```



## Markdown

[github markdown spec](https://github.github.com/gfm/)에 다양한 사용한 법이 있습니다. (스크롤링이 좀 됩니다.)  
위에 메뉴얼이 너무 길다~ 싶으면 간단한 사용법 [Basic writing and formatting syntax](https://help.github.com/en/articles/basic-writing-and-formatting-syntax) 을 참고하세요.  
[code block](https://help.github.com/en/articles/creating-and-highlighting-code-blocks) 사용시 문 [구문](https://github.com/github/linguist/blob/master/vendor/README.md) 강조를 할 수 있습니다.   

<br>
이모티콘 사용할 때 [EMOJI CHEAT SHEET](https://www.webfx.com/tools/emoji-cheat-sheet/) 참고하세요.  
