---
layout: post
title: "FastCGI sent in stderr: failed to open stream: Permission denied in .php"
date: 2016-02-12 00:00:00 +0900
categories: Server, etc 
---


Apache에서 Nginx로 이동하면서 가장 해맸던 에러가 있었는데.. 바로 아래와 같은 에러였다.(보안에 문제가 있을것 같은 부분은 #으로 처리함)

<pre>*1 FastCGI sent in stderr: "PHP message: PHP Warning:  require(#######################.php) [[function.require](function.require)]: failed to open stream: Permission denied in #############################.php on line 21
PHP message: PHP Fatal error:  require() [[function.require](function.require)]: Failed opening required '#####################.php' (include_path='.:/usr/share/pear:/usr/share/php') in ##################.php on line 21" while reading response header from upstream, client: #########, server: ##################, request: "GET / HTTP/1.1", upstream: "fastcgi://127.0.0.1:#####", host: "####################"
</pre>

위 에러의 현상은 Apache(httpd)로 돌릴때는 매우 잘돌아가는데, nginx + php-fpm 으로 할라하면 blank 페이지가 뜨면서 응답코드는 500으로 나타난다. 일단 위의 에러는 Permission 관련 에러인데 php-fpm 혹은 nginx의 user 또는 group이 잘못 설정되었을때 나타난다. (에러난 파일에 chmod 777 한다고 해결 안된다.....) 난 yum으로 nginx와 php-fpm을 설치하였고, os는 centos 기준이다. 디렉토리 및 파일 퍼미션의 그룹과 유저는 각각 somebody, somegroup 으로 가정한다.

##### php-fpm 설정 변경

# vim /etc/php-fpm.d/www.conf

<pre>user = nobody
group = nobody
</pre>

부분을 아래와 같이 변경한다.

<pre>user = somebody
group = somegroup
</pre>

##### nginx 설정 변경

# vim /etc/nginx/nginx.conf 제일 상단부분을

<pre>#user  nobody;
</pre>

아래와 같이 변경한다.

<pre>user  somebody;
</pre>