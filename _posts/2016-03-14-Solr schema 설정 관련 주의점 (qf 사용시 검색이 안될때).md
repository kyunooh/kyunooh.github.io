---
layout: post
title: "Solr schema 설정 관련 주의점 (qf 사용시 검색이 안될때)"
date: 2016-03-14 00:00:00 +0900
categories: Solr 
---


## qf만 설정하면 검색이 안됨...

아직 솔라에 관한 이해도 별로 없고, 자료도 별로 없어서 한참을 해맸다.

솔라의 dismax를 설정하고 qf 옵션을 함께 설정한 뒤 특정 코드(ID와 같은 unique value)를  검색하면 검색이 안되는 문제가 발생하였는데

처음엔 qf에 대한 가중치 문제로 접근했다가 시간을 너무 많이 소비했다.

아직까지 정확한 원인은 모르겠으나, 아래와 같이 string으로 타입을 지정하여 필터를 제거해서 문제를 해결하였다.

설정 전 예시

`<kbd><field name="id" type="**text_en**" indexed="true" stored="true"required="true" /> </kbd>`

`<kbd><field name="name" type="text_en" indexed="true" stored="true"/></kbd>`

`<kbd><field name="areaCode" type="**text_en**" indexed="true" stored="true /></kbd>`

설정 후

`<kbd><field name="id" type="**string**" indexed="true" stored="true"required="true" /> </kbd>`

`<kbd><field name="name" type="text_en" indexed="true" stored="true"/></kbd>`

`<kbd><field name="areaCode" type="**string**" indexed="true" stored="true /></kbd>`

ps. 앞으로 포스팅 주기를 짧게 잡아야 될텐데....