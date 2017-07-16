---
layout: post
title: "[Javascript] object list(array)에서 특정 요소를 제거하기"
date: 2016-03-26 00:00:00 +0900
categories: Javascript 
---


근래에 자바스크립트를 자주 많이 만질 기회가 생겨서,

자바스크립트에서 오브젝트로 된 리스트(array)를 삭제할 일이 생겼다.

자바스크립트는 기본적으로 엘리먼트 제거가 pop, splice으로만 가능한 듯 보인다.

람다를 이용하면, 긴 리스트의 경우 성능이 좋을 것 같지가 않아서

[indexOf](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)와 [splice](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)를 이용한 방법으로 구현하였다.

indexOf 가 object의 위치까지 반환이 가능한 지 몰랐는데, 이번에 사용해보니 가능한 듯 보인다.

아마 자체적으로 해쉬를 관리해서 그런 듯 보이지만

전 초보이기 때문에 그런 거 잘 모르겠어서, 아시는 분이 계시다면 말씀 부탁드립니다.

여튼 바로 예제코드에 들어가 보자

위를 리스트와 오브젝트를 받는 함수로 바꿔서 호출해서 사용하려면 아래와 같이 쓰자.

기존의 리스트에 영향 없이 새로운 리스트를 생성해서 사용하고 싶으면 아래와 같이 구현하면 된다.

참고 : [http://stackoverflow.com/questions/5767325/remove-a-particular-element-from-an-array-in-javascript](http://stackoverflow.com/questions/5767325/remove-a-particular-element-from-an-array-in-javascript)