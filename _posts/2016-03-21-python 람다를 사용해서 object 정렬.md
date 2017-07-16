---
layout: post
title: "python 람다를 사용해서 object 정렬"
date: 2016-03-21 00:00:00 +0900
categories: Core 
---


블로그 초기부터 카테고리안에선 글이 제대로 정렬되지 않아 디버깅 하던 중,

python의 list함수중 하나인 sort 메소드가 원하는 방식대로 동작을 안하는 것으로 보였다.

기존 소스에선 아래와 같이 구현하였는데, 저 id가 doucment.id로 동작할 것이라 생각했던 게 오산이었다.

`document_list.sort(key=**id**, reverse=True)`

구글링을 통하여 람다를 사용해서 해결하는 방법을 확인하여 아래와 같이 수정했다.(하는김에 pk로 잡았다)

`document_list.sort(key=**lambda x: x.pk**, reverse=True)`

파이썬 함수에 대한 이해 부족으로 인해, 발생한 버그여서 더 공부해야겠다는 생각과 함께

파이썬 문법이 정말 간결하다라는 생각이 함께드는 삽질이었다.

(패치는 다음번에 몰아서 할 예정이다)

참고 : [http://stackoverflow.com/questions/403421/how-to-sort-a-list-of-objects-in-python-based-on-an-attribute-of-the-objects](http://stackoverflow.com/questions/403421/how-to-sort-a-list-of-objects-in-python-based-on-an-attribute-of-the-objects)