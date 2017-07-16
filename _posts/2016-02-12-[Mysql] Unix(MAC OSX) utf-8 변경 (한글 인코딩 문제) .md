---
layout: post
title: "[Mysql] Unix(MAC OSX) utf-8 변경 (한글 인코딩 문제) "
date: 2016-02-12 00:00:00 +0900
categories: MySQL 
---


기존 리눅스에서 설정하던 방식으로 my.cnf 수정시

데몬이 시작되지 않는 문제가 발생하며

아래와 같이 수정해 주어야함

MySQL 설치 후 바로 설정 해주는 것이 좋음

/etc/my.cnf 파일을 생성 또는 /etc/my.cnf 파일에 아래내용 추가

%vi /etc/my.cnf (관리자 권한으로 실행)

[mysqld]

character-set-server=utf8

collation-server=utf8_general_ci

init_connect=SET collation_connection=utf8_general_ci

init_connect=SET NAMES utf8

[client]

default-character-set=utf8

[mysql]

default-character-set=utf8

참고 : http://isaacjc.tistory.com/96