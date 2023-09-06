---
layout: post
title:  "Apache Nginx Header Security"
date: 2023-09-05 16:30:00 +0900
abstract: "Apache"
tags: [Apache, Nginx]
image:
toc: true
categories: Tech
last_modified_at: 
published: true
---


제공하는 웹서비스가 많으면, 그만큼의 웹서버가 존재합니다.  
요즘, 내가 개발자인지 서버엔지니어인지 살짝 혼란스럽지만 "주어진 일에 최선을 다하자!" 라는 마음으로 일하고 있습니다.  
  
많은 웹서버의 보안 관련 헤더 설정을 하다보니 정리를 해야겠다는 생각이 들었습니다.  

## 헤더 설정 확인하기 

헤더 설정을 확인하는 방법은 다양합니다.  
외부에 공개된 서비스인 경우 https://securityheaders.com/ 와 같은 진단 사이트에서 조회해보는 방법도 있습니다. 


![Security after](/assets/article_images/2023-09-05-Apache-Nginx-Security/securityheaders_02.png)

보안헤더 설정 후 진단화면입니다.  
![Security before](/assets/article_images/2023-09-05-Apache-Nginx-Security/securityheaders_01.png)

내부에서만 접근 가능한 서비스인경우 커맨드 명령어로 조회할 수 있습니다.   
보안관련 헤더를 추가한 서비스를 조회한 샘플입니다.  
```bash 
$ curl -I https://tree.com       
HTTP/1.1 302 Found
Date: Tue, 05 Sep 2023 06:11:27 GMT
Server: Apache
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
X-Frame-Options: sameorigin                            
X-Content-Type-Options: nosniff                         
Cache-Control: no-cache, no-store, must-revalidate
Pragma: no-cache
Expires: Thu, 01 Jan 1970 00:00:00 GMT
Location: https://tree.com/error
Content-Language: en-US
X-XSS-Protection: 1; mode=block
Connection: close
```
* -I : CURL 에서 응답 헤드만 표시하는 옵션입니다. 
* -L : curl 에서 방향 전환을 추적할 때 -L 옵션을 지정합니다.  ex) curl -I -L 주소


## WebServer version

서버 버전이 노출되면 해당 버전의 취약점으로 공격가능성이 있기때문에 노출되지 않도록 설정합니다. 

버전 옵션 설정 전 
```bash 
$ curl -I tree.com
HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Tue, 05 Sep 2023 10:32:07 GMT
``` 

버전 옵션 설정 후
```bash 
$ curl -I tree.com
HTTP/1.1 200 OK
Server: nginx
Date: Tue, 05 Sep 2023 10:32:07 GMT
``` 

### nginx 
```bash 
server {
  server_tokens off;
}
``` 

### apahce 

```bash 
ServerTokens Prod
ServerSignature Off 
``` 

## default_server 

여러개의 서비스(vhost)를 설정한 경우 VIP, PublicIP 로 접근했을때 제어가 필요합니다. 

![](/assets/article_images/2023-09-05-Apache-Nginx-Security/securityheaders_03.png)

nginx 기본 페이지가 나오면 안되는 경우에는 대표 서비스로 이동하도록 설정해주세요.  
```bash 
server {
  listen       80 default_server;   # 
  server_name  localhost;

  server_tokens off;

  location / {
          rewrite ^ https://tree.com$request_uri? permanent;
  }

  # redirect server error pages to the static page /50x.html
  #
  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
          root   html;
  }
}
```

* default_server : 이 가상의 호스트가 다른 가상의 호스트들의 listen statement와 매치되지 않는 모든 요청에 응답


## X-Frame-Options

X-Frame-Options 옵션설정에 관한 대표적인 예로 HTML 태그중 `<frame>`, `<iframe>`, `<object>` 에서 렌더링 할 수 있는지 여부를 나타내는데 사용됩니다. 

ClickJacking 등의 공격을 방지할 있습니다.  
sameorigin 옵션값 을 권고하고 있습니다. samorigin 은 동일한 도메인인 경우 `<iframe>` 에서 호출 가능합니다. 

[Mozilla X-Frame-Options](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-Frame-Options) 보안헤더 관련 설명 잘 나와있습니다. 

### nginx 

```bash 
add_header X-Frame-Options sameorigin;
``` 

### apahce 

```bash 
Header always append X-Frame-Options "sameorigin"
``` 

## X-XSS-Protection

브라우저에서 XSS 필터링 사용하는 X-XSS-Protection을 설정합니다.   
옵션 값으로 1; mode=block 사용 권고하고 있습니다.  

### nginx 
```bash 
add_header "X-XSS-Protection" "1; mode=block";
``` 

### apahce 
```bash 
Header set X-XSS-Protection "1; mode=block"
``` 

[Mozilla X-XSS-Protection](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-XSS-Protection) 보안헤더 관련 설명 잘 나와있습니다. 


## Strict-Transport-Security

HTTPS를 사용하는 경우, HTTPS 통신을 강제하는 Strict-Transport-Security을 설정합니다. 

### nginx 
```bash 
add_header Strict-Transport-Security "max-age=31536000; includeSubdomains; preload";
``` 

### apahce 
```bash 
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
``` 

* max-age=31536000 : HSTS가 브라우저에 설정될 시간이며 초단위로 설정됩니다. 31536000은 1년을 의미합니다.
* includeSubdomains : HSTS가 적용될 도메인의 subdomain (www.rsec.kr또는 mail.rsec.kr따위) 까지 HSTS를 확장 적용함을 의미합니다.
* preload : HSTS 적용이 클라이언트 측에서 preload로 이루어짐을 의미합니다.


관련 블로그 : https://rsec.kr/?p=315


## X-Content-Type-Options

리소스의 MIMETYPE이 일치하는 경우에만 리소스를 다운로드하는 X-Content-Type-Options을 설정합니다. 

### nginx 
```bash 
add_header X-Content-Type-Options nosniff;
``` 

### apahce 
```bash 
Header always set X-Content-Type-Options "nosniff"
``` 


## Method Allow

불필요한 메서드를 허용하는 경우 허용된 메서드로 웹서버에 파일을 생성하거나 삭제할 수 있는 취약점이 발생합니다. 
REST API 서비스인 경우 사용하는 메서드만 허용합니다. 

```bash 
$ curl -v -X OPTIONS https://tree.com

< Allow: GET, HEAD, POST, PUT, DELETE, TRACE, OPTIONS, PATCH
``` 


## nginx

Nginx 설정에 아래와 같이 추가 합니다.  
톰캣도 함께 사용하는 경우 톰캣 설정도 추가해줍니다.  

```bash 
server {
    생략..
       location / {
            index  index.html index.htm;
            root html;

            limit_except GET POST PUT DELETE HEAD {
                deny all;
            }
       }
}
``` 

## apache 

httpd.conf 설정에 추가합니다.  
``` bash 
<Directory />
   <LimitExcept GET POST>
       Order allow,deny
       Deny from all
   </LimitExcept>
</Directory>
``` 

TRACE 메소드는 아래 설정을 추가합니다. 
``` bash 
TraceEnable Off
``` 

## Tomcat 

web.xml 파일 내 `<web-app>` 하위에 설정을 추가합니다. 

```xml 
<security-constraint>
    <display-name>Forbidden</display-name>
    <web-resource-collection>
        <web-resource-name>Protected Context</web-resource-name>
        <url-pattern>/*</url-pattern>
        <http-method>TRACE</http-method>
        <http-method>OPTIONS</http-method>
    </web-resource-collection>
    <auth-constraint>
        <role-name></role-name>
    </auth-constraint>
</security-constraint>
``` 

## Summary

## nginx

80 port 를 사용하는 서비스 설정입니다. 

```bash 
$ vi /nginx-1.22.0/conf/tree.80.conf

server {
  listen          80 default;

  ## 
  error_page 400 401 403 404 405 408 410 411 412 413 414 415 /error_4xx.html;
  error_page 500 501 502 503 504 505 506 /error_5xx.html;

  ## 
  server_tokens off;                # server_tokens가 off일 경우, 서버 정보가 노출되지 않음

  ## 
  add_header X-Content-Type-Options nosniff;                         # 4) 리소스의 MIMETYPE이 일치하는 경우에만 리소스를 다운로드

  # add_header X-Frame-Options SAMEORIGIN always;                    # 1) 단독 사용 사이트 경우 
  add_header X-Frame-Options "allow-from *.tree.com";                # 1) ClickJacking 등의 공격을 방지 (sameorigin 사용 권고), <frame>, <iframe>, <object>에서 해당 페이지를 렌더링 할 수 있는지 결정
  add_header Content-Security-Policy "frame-ancestors *.tree.com";   # 1) 상동 

  add_header X-XSS-Protection "1; mode=block" always;                # 2) 브라우저에서 XSS 필터링 사용 
```


위 설정을 적용 후 조회합니다. 
```bash 
$ curl -I http://tree.com/error_4xx.html
HTTP/1.1 200 OK
Server: nginx
Date: Tue, 5 Sep 2023 05:50:01 GMT
Content-Type: text/html
Content-Length: 2186
Last-Modified: Mon, 17 Apr 2023 09:46:48 GMT
Connection: keep-alive
Keep-Alive: timeout=5
ETag: "643d1588-88a"
Expires: Tue, 18 Apr 2023 05:50:00 GMT
Cache-Control: no-cache
X-Frame-Options: SAMEORIGIN                     ## 1) X-Frame-Options, 단독 사용 사이트 경우
X-XSS-Protection: 1; mode=block                 ## 2) X-XSS-Protection, 브라우저에서 XSS 필터링 사용 
X-Content-Type-Options: nosniff                 ## 3) X-Content-Type-Options, 리소스의 MIMETYPE이 일치하는 경우에만 리소스를 다운로드
Accept-Ranges: bytes
```


## apache 

443 port 를 사용하는 서비스 설정입니다.  

```bash 
$ vi /apache-2.4.39/conf/httpd.conf

ServerTokens Prod
ServerSignature Off

<VirtualHost *:443>
        DocumentRoot /html/tree
        ServerName tree.com
~
        # add 
        <IfModule headers_module>
            Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
            Header always append X-Frame-Options "sameorigin"
            Header always set X-Content-Type-Options "nosniff"
            Header set X-XSS-Protection "1; mode=block"
        </IfModule>
``` 
아파치 모듈 [mod-headers](https://httpd.apache.org/docs/current/mod/mod_headers.html) 문서 링크입니다.  


위 설정을 적용 후 조회합니다. 
```bash 
$ curl -I https://tree.com       
HTTP/1.1 302 Found
Date: Tue, 05 Sep 2023 06:11:27 GMT
Server: Apache
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload         # Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
X-Frame-Options: sameorigin                             # Header always append X-Frame-Options "sameorigin"
X-Content-Type-Options: nosniff                         # Header always set X-Content-Type-Options "nosniff"
Cache-Control: no-cache, no-store, must-revalidate
Pragma: no-cache
Expires: Thu, 01 Jan 1970 00:00:00 GMT
Location: https://tree.com/error
Content-Language: en-US
X-XSS-Protection: 1; mode=block                         # Header set X-XSS-Protection "1; mode=block"
Connection: close
```
