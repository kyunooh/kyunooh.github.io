---
layout: post
title: "Thymeleaf 사용경험 공유"
date: 2016-04-12 00:00:00 +0900
categories: Know-how 
---


![](http://i.imgur.com/voq7KFS.png)

## Java의 뷰템플릿

최근 프론트엔드 개발도 하게 되면서, 뷰템플릿에 대해 많이 고려했다. 이렇게 뷰템플릿을 많이 생각해본 것도 [jsoup을 사용하게 된 이유랑 비슷](http://jellyms.kr/800)한데, 일단 [장고(django)](https://www.djangoproject.com/)의 뷰템플릿을 한번 경험해본 게 큰 이유였다. 확실히 JSP + JSTL 보다는 훨씬 쉬웠기 때문이다. 이런 경험이 있다보니 자바에서 비슷한 구현체를 찾았고, [Thymeleaf](http://www.thymeleaf.org/), [freemarker](http://freemarker.org/), [tiles](http://tiles.apache.org/framework/index.html), [jade4j](https://github.com/neuland/jade4j) 등등을 비교해 보았다. 그리고 최종적으론 Thymeleaf로 결정했다.

## Thymeleaf를 선택한 이유

처음엔 이거 대체 어떻게 읽어야 될지 몰랐는데, 타임리프라고 읽는다 카더라...(아마 Time leaf의 말장난인 것 같다.) 여튼 Thymeleaf를 선택한 이유는 아래와 같다.

*   높은 생산성을 위해 도입한 만큼, 일단 학습이 쉬워야 함
*   검증된 라이브러리
*   layout 확장기능 지원(가장 중요!)
*   다른 사람이 보더라도, 보기 쉬운 코드로 작성되어야 함
*   라이브러리를 사용함으로 인해 기술부채가 발생치 않아야 함

기능적으로는 layout 기능을 가장 중요시 했다. HTML에서 코드 중복을 확실하게 줄일 수 있는 방법이기 때문인데, 이를 잘 지원하는 것에서 선택이 쉬웠다. ([Thymeleaf Page Layout](http://www.thymeleaf.org/doc/articles/layouts.html)) 제일 문서도 잘 정돈 된 편이었으며, 스프링 프로젝트에서 밀어주고 있다는 것은, 검증이라는 부분에 큰 점수를 주기 충분했다. Thymeleaf에서 가장 밀고있는 듯한 Natural Template기능은 딱히 고려하지 않았으며, 사용중에도 아무 쓸모가 없었다. 아마 대부분 혼자 개발 한다면 별로 필요한 기능이 아닌 것 같다. (퍼블리셔가 따로 있는 곳에서는, Thymeleaf를 사용할까? 라는 의문이 들기도 하지만...)

### Layout 기능은 굉장히 고마웠다.

기존 코드에서 많은 부분의 중복을 걷어내고, 깔끔하게 유지할 수 있었다. 유지보수 측면에서 굉장히 고마웠다. 다만 아쉬운 부분은 모든 템플릿을 전부 HTML형식 전부 맞춰서 만들어야 한다는 것이다. 이것은 여러가지로 혼란스럽기는 했지만, 확장을 하는(자식 입장) 측의 코드가 나중에 들어가게 되어서 개발에 불편함은 없었다.

#### Spring boot의 지원

스프링 부트에서 지원 한다는 점은 굉장히 크게 작용했다. 따로 의존성만 추가해주고 프로퍼티만 몇개 설정해주면 바로 사용할 수 있어서 이부분은 확실히 큰 장점이었다.

#### Natural Template이 필요 없는 이유

또 Natural Template 기능을 사용하지 않게 된 이유는 개발중엔 thymeleaf 캐시를 꺼두면 지속적으로 반영되었기 때문이다. 딱히 서버 재시작이 필요하지 않고, html만 따로 작업할 이유도 없었기 때문에 편하게 작업할 수 있었다.

### 뷰템플릿의 한계와 아쉬운 Custom Attribute

한동안 만족하면서 엄청나게 사용했었는데 문제가 생기기 시작했다. 흔히 interactive 라고 불리우는 기능들을 추가하면서, 결국 자바스크립트를 많이 사용하게 되었고, 기존에 jquery로 되어있던 부분들을 Angular.js로 변경하기 시작했다. 이 과정에서 문제가 발생하였는데, thymeleaf와 ng-value라는 속성을 사용하기 위해서 `th:ng-value="${ someobject.value }"` 라고 적으면 당연히 될 것 이라고 생각했던 것과 달리 동작이 되지 않았다. 나중에 [다른 방법](http://stackoverflow.com/questions/21801158/thymeleaf-custom-attribute)을 찾게 되긴 했지만, 고려사항중 하나인 **보기 쉬운 코드**에 어긋나기 때문에 사용하지 않고, Restful API를 따로 구현해서 사용했다.

#### 매뉴얼을 꼭 읽어야 한다는 것

다른 뷰템플릿의 경우 한번 익히면 필요한 기능이 생길 때나 메뉴얼을 읽지만, 웬만해선 Thymeleaf의 매뉴얼을 따로 띄어두고 작업 했었다. 당연히 될 것 같은데 안되는 부분이 워낙 많기도 했고, 사용법이 어려웠다.. 아 어렵다기보단 까다로웠다.

#### 그외 단점들

*   기본적으로 xml기반이라 태그 처리에 대해서 까다로운 점은 개발에 방해가 되었다.(물론 이부분은 장점이 될수도 있다. 현재 베타버전인 thymeleaf3의 경우엔 html5를 지원한다고 써있다.)
*   성능이 안좋다. 참고 : [http://www.slideshare.net/jreijn/comparing-templateenginesjvm](http://www.slideshare.net/jreijn/comparing-templateenginesjvm)

### 총평

전체적으로 나쁘지 않지만 성능면이나 표현적인 면에서 아쉬운점이 많이 남는 라이브러리였다. thymeleaf3에서도 Custom attribute에 대해서 같은 방식으로 처리하기 때문에... 아마 자바 뷰템플릿을 사용하게 된다면 다른 뷰템플릿 엔진을 사용하게 될 것 같다. 그래도 jsp보단 훨씬 좋은 경험이었다. 그리고 프론트엔드가 복잡해지면 결국 결론은 JS + Restful API 가 답인것 같다. 혹시 다음에 기회가 있다면 jade4J를 사용해보고 싶다.

#### 도움이 되었던 글들

*   Outsider's Dev Story : [Thymeleaf 사용 소감](https://blog.outsider.ne.kr/969 "Permanent Link to Thymeleaf 사용 소감 Thymeleaf 사용 소감")
*   Java Code Geeks : [Implement Bootstrap Pagination With Spring Data And Thymeleaf](https://www.javacodegeeks.com/2013/03/implement-bootstrap-pagination-with-spring-data-and-thymeleaf.html)