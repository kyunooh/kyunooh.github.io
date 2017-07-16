---
layout: post
title: "jsoup 사용중 SocketTimeoutException"
date: 2016-04-11 00:00:00 +0900
categories: Know-how 
---


![](http://i.imgur.com/JKJ3572.png)

## Java의 BeautifulSoup

요즘 여러가지 언어들을 사용하다보니, 한번 사용해보고 좋았던 라이브러리들은, 다른 언어로도 구현된 것들을 찾아보곤한다. [jsoup](http://jsoup.org)도 그중에 하나인데, python으로 구현된 [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/)을 구현한 것이다. 최근 페이지를 스크랩해서 데이터를 정제해야할 일이 있었다. 모듈화 해서 쓰려고 자바로 된 구현체를 찾다가, jsoup을 발견하게 되었고, 최근엔 많이 사용하고 있다.. 사용방법은 잘 정리된 글이 많으니 따로 다루진 않겠다. (사용법이 잘 정리된 글 > 에돌이의 얕고 넓은 샘 :: [jsoup - 자바를 위한 BeautifulSoup (HTML parser)](http://edoli.tistory.com/95))

## 뜬금없는 SocketTimeoutException

잘 사용하고 있던중에, 가끔씩 데이터를 제대로 못가져 오는 경우가 생겨서 로그를 확인해보니 `java.net.SocketTimeoutException: Read timed out` 위와 같은 예외가 발생하고 있었다. 일반적인 socket timeout 이라고는 생각하지 않고, 혹시 스크랩을 너무 많이 해서 ip가 차단 당했나 싶었지만, request 자체를 정상 유저보다 적게 쓰고 있었기 때문에 그 이유도 아니었다. 혹시 사이트가 그냥 응답을 안하나 싶었는데, 그런것도 아니었다.. 그래서 결국 구글링을 하니 [jsoup Connection](http://jsoup.org/apidocs/org/jsoup/Connection.html) 문서에 `The **default timeout is 3 seconds (3000 millis).** A timeout of zero is treated as an infinite timeout.`와 같은 걸 발견하였다. 아니 타임아웃이 3초라니... 전에 에러로깅 정리를 엄청하면서 'Java의 기본 SocketTimeout은 60초로 맞춰놓는구나'라는 편견으로 인해, 디버깅을 괜히 오래하게 된 케이스다. 또한 '스크랩 라이브러리이면 보통 방대한 자료를 스크랩 하지 않을까?'라고 생각해서 3초로 맞춰졌을거라는 생각은 전혀 못했다. 이정도면 설계에 실수라고 생각했는데, 동기식으로 크롤링하는 경우를 생각해보면 3초도 적당해 보인다.

## 해결 방법과 결론

여튼 해결방법은 아래와 같이 timeout설정을 바꿔주면 된다. `Jsoup.connect("http://jellyms.kr").**timeout(30000)**.get();` 케이스별로 적당하게 timeout을 설정하자. 나같은 경우 성능이 딱히 중요한 부분도 아니고 지연이 되더라도 데이터를 전부 제대로 가져오는 게 더 중요했기 때문에 30초로 맞추어서 반영하였다. 아 그리고 jsoup 사용중에 socket timeout과 같은 예외 말고도 Element를 전부 가져오지 못하는 버그가 있었는데, 실제 사이트에서 데이터를 잘못 던지는 것인지, jsoup의 버그인지 찜찜한 부분이 있었다. (사이트 인코딩 문제도 의심된다..) 일단 임시로 해결하긴 하였는데, 비슷한 증상이 있으신 분은 알려주시면 감사하겠습니다. 이번에 디버깅을 하면서 얻은 교훈은 '과거의 경험이 독이 될 수 있다'는 것과 결론은 역시 구글갓! 참고 : [http://stackoverflow.com/questions/6571548/i-get-a-sockettimeoutexception-in-jsoup-read-timed-out](http://stackoverflow.com/questions/6571548/i-get-a-sockettimeoutexception-in-jsoup-read-timed-out)