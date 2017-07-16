---
layout: post
title: "MySQL에서 값이 NULL인 데이터를 찾고 싶을 때"
date: 2016-04-30 00:00:00 +0900
categories: MySQL 
---


## 시간 낭비를 하지 않기 위해 일단 결론은..

MySQL에서 NULL 을 비교하기 위해선 SELECT * FROM jelly_table WHERE jelly_name **IS NULL**을 쓰면 된다. 아래부터는 뻘짓의 기록...

## NULL.. 너란 녀석...with MySQL

null은 원래 플랫폼, 언어 등등에 따라 그 구현이 다르지만 MySQL에서 만난 null은 조금 더 특별(이라고 쓰고 괴악이라 읽는다.)했다. 요즘 들어 부쩍 SQL에 자신감이 생긴 나는 당당하고 아주 빠르게 다음과 같은 쿼리를 날렸다. `SELECT jelly_id FROM jelly_table WHERE jelly_taste = null;` Empty Set 을 반환한다... 이제 한번 의심을 해본다.. '어 빈문자열로 들어가 있었었나?' 그래서 바로 다음과 같은 쿼리를 날려본다. `SELECT jelly_id FROM jelly_table WHERE jelly_taste = "";` Empty Set을 반환한다... ???? 이제 슬슬 멘붕이 오기 시작한다... 하지만 일단 침착하고 다음과 같은 쿼리를 날려본다. `SELECT jelly_id FROM jelly_table WHERE jelly_taste <> null;` Empty Set 을 반환한다. 이제 침착할 수가 없게 됬다... 내가 데이터를 전부 날린건가.... ㅠㅠㅠㅠ 그런데 `SELECT * FROM jelly_table;` 를 하면 데이터를 잘 가져온다. 도대체 뭐가 문제일까? 해결방법을 위해 구글링을 시작한다.

## NULL 찾아봤지만, 넌 어디에도 없었어..?

역시 구글링을 시작하자마자 [스택오버플로우에서 답](http://stackoverflow.com/questions/3536670/mysql-selecting-rows-where-a-column-is-null)을 찾을 수 있었다. SQL에선 NULL값은 특별하기 때문에 IS로 비교해야한다는 답변이 있었다. 하지만 이 답변보다는 나의 흥미를 끝었던 건 답변의 코멘트였는데, **NULL과 비교해서 나오는 값은 FALSE는 아니라는 것**이다. 그래서 또 다른 뻘짓을 하기 시작했다.

### 이렇게 하면 NULL 가질 수 있을거라 생각했어

![](http://i.imgur.com/EDGYmpE.png?1)![](http://i.imgur.com/vEsomhp.png?1) 일단 첫번째 결론은 **NULL 과 비교연산자(<, > , = , <>, <=, >= 등)를 사용하여 비교한 모든 값은 NULL을 반환한다.** ![](http://i.imgur.com/RiQXCQR.png) 이제부터 헤깔리기 시작한다... 두번째, **NULL AND TRUE(1) = NULL** 그리고 **NULL OR TRUE(1) = TRUE(1)** ![](http://i.imgur.com/D0e5npf.png) 세번째, **NULL AND FALSE(0) = FALSE(0)** 그리고 **NULL AND OR FALSE = NULL** 개인적인 예상으로 NULL OR x = x이고, NULL AND x = NULL 일 것으로 예상했으나 빗나갔다... 뭔가 찝찝하지만 결론은 **FALSE AND x = FALSE, TRUE OR x = TRUE** 로 외우면 될 것 같다. **위의 결과가 표준 SQL인지 MySQL에서만 그런건지도 잘 모르겠지만, 아마 표준인 듯 싶다. 나중에 다른 DBMS에서도 테스트 해봐야겠다.**