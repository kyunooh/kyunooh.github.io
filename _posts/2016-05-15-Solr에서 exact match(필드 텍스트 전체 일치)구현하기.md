---
layout: post
title: "Solr에서 exact match(필드 텍스트 전체 일치)구현하기"
date: 2016-05-15 00:00:00 +0900
categories: Solr 
---


_본 글은 픽션입니다. 실존하는 단체, 인물, 제품과는 무관함을 알려드립니다._

## [](#문제의-시작)문제의 시작

젤리 판매사이트 **- 주식회사 젤리의 망상 -** 에선 여러가지 젤리를 많이 판매하고 있다. 젤리의 망상에서 몇년전에 출시한 **“하리봉과 곰이베어” 제품은 인기가 너무 많았다**. 그 이후 이 제품은 여러가지 **시리즈 제품으로 출시**하기 시작한다.  
“하리봉과 곰이베어”  
“하리봉과 곰이베어 익스트림”  
“하리봉과 곰이베어 2”  
“하리봉과 곰이베어 3”  
“하리봉과 곰이베어 스페셜”  
“하리봉과 곰이베어 스페셜 2”  
“하리봉과 곰이베어 후르츠 믹스”  
“하리봉과 곰이베어 짱맛 짱짯맛”  
“하리봉과 곰이베어 더이상 시리즈 이름이 생각나지 않는데”  
…  
…  
…  
“하리봉과 곰이베어 파이널”  
“하리봉과 곰이베어 진짜 파이널”  
“하리봉과 곰이베어 리얼 진짜 파이널”  
이렇게 시리즈가 많아지다 보니, 문제가 생겼다. 출시 된지 가장 오래되었지만, 그래도 가장인기가 많았던 **“하리봉과 곰이베어”가 바로 검색이 안되기 시작한 것**이다. 유저는 이로인해 불편함을 남기었고, 젤리의 망상은 비상이 걸린다.

## [](#왜-이런-문제가-생겼지...)왜 이런 문제가 생겼지…

젤리의 망상의 검색 엔진은 Solr를 사용하고 있다. 빠른 검색을 위해 사용하고 있지만, “하리봉과 곰이베어”가 포함된 경우 검색결과 점수(score) 똑같이 나왔다. 하지만 유저에겐 그런 건 핑계가 될 수 없다. 해결방법 중 하나로 제안된 것은, 일단 검색어가 정확하게 일치할 경우엔 우선적으로 해당 젤리가 등장하도록 하는 것. 하지만 Solr에서는 exact match를 자체 지원 하지 않았다…

## [](#solr에서-exact-match-구현하기)Solr에서 Exact Match 구현하기

Solr에서 정식으로 지원하는 기능은 아니지만, **꼼수로 Exact Match를 구현**하는 방법이 있었다.(구글갓은 무엇이든 알고 있다.)  
일단 아래와 같은 원본 필드가 있을것이다.

`<field name="jellyName" type="text_ko" stored="true"/>`

그 다음 해당 필드를 복사하는 아래와 같은 필드를 선언한다. **exactJellyName의 타입은 string으로 설정한다(토크나이저를 안 태우기 위함)**

`<field name="exactJellyName" type="string" />`  
`<copyField source="jellyName" dest="exactJellyName"/>`

자 이제 거의 끝났다. 설정 적용과 dataimport를 다시 한번 돌려준 뒤 아래와 같이 파라미터를 설정하면 된다.

`jellysolrsolrsolr.com/solr/jelly/select?q=하리봉과+곰이베어&indent=true&defType=dismax&pf=exactJellyName`

**dismax 옵션을 설정한 뒤, pf에 아까 설정해 준 exactJellyName 파라미터만 추가해주면 된다.** 이렇게 설정해 준다면, **exactJellyName은 토크나이저를 우회한 상태로 필드에 점수가 올라가기 때문에 기존에 같은 스코어일 경우 정확하게 매칭된 값이 우선적으로 검색된다.**  
자 이제 이렇게 젤리의 망상은 다시 “하리봉과 곰이베어”의 판매량을 올릴 수 있었다.

참고 : [http://stackoverflow.com/questions/29103155/solr-exact-match-boost-over-text-containing-the-exact-match](http://stackoverflow.com/questions/29103155/solr-exact-match-boost-over-text-containing-the-exact-match)

ps. 필자는 곰이베어보단 하리봉을 선호한다.