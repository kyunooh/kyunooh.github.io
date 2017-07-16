---
layout: post
title: "Django 프로젝트와 Travis CI 연동 + selenium"
date: 2016-05-05 00:00:00 +0900
categories: Django 
---


## Django 프로젝트와 Travis CI를 연동해보자

### Travis CI에 대한 간단한 소개

[Travis CI](http://travis-ci.org)는 [GitHub](http://github.com)에서 만든 CI(지속적인 통합)툴 입니다. GitHub 오픈소스 프로젝트는 무료로 사용할 수 있으며, 다른 CI툴에 비해 사용법이 간단합니다.(개인적인 느낌) public repository의 경우 [travis-ci.org](http://travis-ci.org), private repository의 경우엔 [travis-ci.com](http://travis-ci.com)에서 사용할 수 있습니다.

### Django 프로젝트를 위한 Travis CI 설정

`설정 부분에 들어가기 전, **DB는 MySQL을 사용하였고, selenium의 브라우저는 FireFox**를 사용했음을 알려드립니다.` Travis CI에 로그인 하면 아래와 같은 화면을 볼 수 있습니다. (전 이미 프로젝트가 등록된 상태여서 화면이 다를 수 있습니다.) ![](http://i.imgur.com/CvLGQIp.png) 이제 이 화면 좌측에서, My Repository 오른쪽에 있는 **(+) 버튼**을 클릭합니다. 그 다음화면에선 본인이 가지고 있는 public repository 리스트를 확인할 수 있으며, 버튼을 클릭해서 travis 사용여부를 스위칭 할 수 있습니다. (만약 아래와 같은 화면이 안보이는 경우 **GitHub에서 Travis-CI에 대한 권한을 준 뒤, Sync**하시면 됩니다.) ![](http://i.imgur.com/sh1RBhf.png) CI에 사용할 프로젝트를 선택했다면, .travis.yml을 작성할 차례입니다.

#### .travis.yml 작성하기

travis.yml은 Travis-CI에서 사용하는 일종의 설정파일이라고 보시면 됩니다. 이 파일에서 사용할 언어, 프레임워크(라이브러리), 기타 환경(브라우저, DB 등등)을 설정 하실 수 있습니다. 이제 repository 루트 폴더에 아래와 같은 .travis.yml 파일을 작성합니다. requirements에는 DB 연동을 위한 mysqlclient와 selenium을 넣어두었습니다. .travis.yml에서 조금 더 중요한 부분을 자세히 살펴봅시다. 26번줄은 django setting을 분리하기 위해서 적어주었습니다. 아래에 settings.py에서 예시를 보여드리겠습니다. `- BUILD_ON_TRAVIS=true` 34~37번 줄은 Xvfb([영문 위키](https://en.wikipedia.org/wiki/Xvfb))를 설정하는 부분입니다. `before_install: - "export DISPLAY=:99.0" - "sh -e /etc/init.d/xvfb start" - sleep 3 # give xvfb some time to start` Xvfb는 X11의 디스플레이 서버 프로토콜로써, 본 예제에서는 **[selenium](http://www.seleniumhq.org/)을 쓰기 위해 사용**합니다. 위의 스크립트를 사용하여 Firefox를 Travis-CI에서도 사용할 수 있습니다. 50번째 줄에서는 MySQL에 database를 만듭니다. `- mysql -u root -e 'create database model_test;'`

#### Test 코드 작성

selenium이 정상적으로 작동되는 지 확인해보기 위해서 아래와 같은 간단한 test코드를 작성했습니다. 코드에 맞게 view와 template, url을 작성 및 수정해준 뒤 **.travis.yml과 함께 변경 된 부분을 커밋하고 github에 푸시합니다.** ![](http://i.imgur.com/Cg2cbnM.png) 자 이제 다시 Travis-CI에 접속해서 상태를 확인해 봅시다. 성공적으로 초록불이 들어왔네요. (사실 실수해서 몇번 커밋한 건 비밀)

#### travis setting 분리하기

작업을 하다보면 travis에서만 setting을 조금 바꾸고 싶을 때가 있습니다. 그럴 땐 아래와 같이 세팅을 분리 하실 수 있습니다. (위에서 BUILD_ON_TRAVIS=true를 설정한 이유, 좋은 방법은 아니라고 생각되지만...)

##### 기타 확인사항

- dist: trusty를 설정할 시, 기존과 addon 설정이 달라집니다. - selenium이 제대로 동작 안될 시 selenium과 Firefox의 버전을 한번 확인하세요.

### 마치며...

이번 글을 쓰면서 가이드 글을 쓰시는 분께 정말 존경한다는 말을 드리고 싶네요... 해당 프로젝트는 GitHub에 공개 되어있으며, 본래 다른 프로젝트에서 문제가 발생하여, travis에서 model설정을 테스트 해보려고 만든 repository 입니다. 그것을 변경하여 travis test용으로 변경하였는데, 고로 repository 이름이 이상한 걸 신경쓰면 지는 겁니다. GitHub repository : [https://github.com/kyunooh/django-model-test](https://github.com/kyunooh/django-model-test) 2주동안 Travis-CI로 인해 삽질한 경험을 정리하고자 하였으며, django강의가 아니다보니 내용엔 누락된 부분(model 등)이 조금 있습니다. 이해 부탁드립니다. Travis-CI에서 selenium 사용이 가능하다는 걸 알려드리고 싶었습니다. 저처럼 [PhantomJS](http://phantomjs.org/)로 다 바꿔야 되나 고민하고 계신분께 많은 도움이 되었으면 합니다. 내용중 잘못된 부분은 댓글 혹은 이메일 부탁드립니다. 아래는 이전에 설정 중 참고한 문서입니다. [https://speakerdeck.com/silent1mezzo/testing-django-with-travis-ci](https://speakerdeck.com/silent1mezzo/testing-django-with-travis-ci) 최현묵 (jelly) : chm073@gmail.com