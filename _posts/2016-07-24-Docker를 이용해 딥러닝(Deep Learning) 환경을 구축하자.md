---
layout: post
title: "Docker를 이용해 딥러닝(Deep Learning) 환경을 구축하자"
date: 2016-07-24 00:00:00 +0900
categories: Core 
---


## [](#가이드-전-주의사항)가이드 전 주의사항

필자는 딥러닝 개발자 또는 전문가가 아닙니다. 공부하는 중도 아니고 공부를 시작하는 중입니다. 그러니 내용중 틀린 부분이 발견된다면, 정정 부탁드립니다. 최근에 딥러닝 환경 구축으로 인해 극심한 스트레스를 받았지만, dl-docker를 통해 쉽게 해결한 기억이 있어 공유합니다. 딥러닝을 배우기 위한 개발 환경설정은 본 글로 충분하다고 느낍니다.

## [](#docker-for-mac-설치하자)Docker for mac 설치하자

[https://docs.docker.com/docker-for-mac/](https://docs.docker.com/docker-for-mac/) 에 접속하여 Docker for mac을 다운로드하여 설치해준다. 일반 어플리케이션을 설치하듯 쉽게 설치가 가능하다. 설치가 완료되면 실행을 시킨 뒤 터미널을 열어

    docker --version

명령어를 이용하여 정상적으로 설치되었는지 확인한다.

## [](#all-in-one-docker를-사용해보자)All in one docker를 사용해보자

### [](#all-in-one-docker란?)All in one docker란?

All in one docker(이하 dl-docker)는 딥러닝을 위한 docker image이다. 딥러닝을 위한 프레임워크들을 아주 잘 정리되있어서, dl-docker만으로 딥러닝 프레임워크를 테스트 해보는 데 충분했다. 아쉬운 단점은 **GPU Version 은 리눅스에서만 지원 하며 맥에서는 CPU 버전밖에 사용할 수 없다.**

### [](#dl-docker를-받고-jupyter-접속하기)dl-docker를 받고 jupyter 접속하기

Docker가 설치되어 있다면, 아래의 명령어를 통해 간단하게 dl-docker를 받으실 수 있습니다.

    jelly$ docker pull floydhub/dl-docker:cpu

    cpu: Pulling from floydhub/dl-docker

    6c953ac5d795: Pull complete ====
    3eed5ff20a90: Pull complete 
    ...
    ...
    ffa9a85a3cc7: Pull complete 
    Digest: sha256:377e9443b323ff2346d33b096f3bd4b7ae0a707823dd8430e093cccf59e021e9
    Status: Downloaded newer image for floydhub/dl-docker:cpu

위와 같이 docker pull이 완료되었다면 아래의 명령어로 jupyter를 실행 시킨 뒤

    docker run -it -p 8888:8888 -p 6006:6006 floydhub/dl-docker:cpu jupyter notebook

브라우저를 열어 localhost:8888에 접속하여 정상적으로 주피터가 실행되는 지 확인합니다. 실행이 정상적으로 된다면 신나게 코딩을 합시다.

#### [](#마치며)마치며

dl-docker에는 여러가지 딥러닝 프레임워크가 많으므로 잘 활용하시길 바랍니다. 다음에는 파이참과 docker를 활용한 리모트 개발을 포스팅할 예정입니다.
