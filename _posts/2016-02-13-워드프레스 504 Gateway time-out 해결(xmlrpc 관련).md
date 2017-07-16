---
layout: post
title: "워드프레스 504 Gateway time-out 해결(xmlrpc 관련)"
date: 2016-02-13 00:00:00 +0900
categories: Server, etc 
---


## 워드프레스에서 504 에러 발생 (xmlrpc)

어제 5시부터 갑자기 블로그 접속이 안되기 시작하면서 504 에러가 발생하였다. 서버 상태에 문제가 있는줄 알고 확인해보았지만, 메모리 사용량도 스왑을 건드릴정도는 아니었다. 어제는 서버가 굉장히 느려져서 어떤 작업도 제대로 할 수 없는 상황이었는데 오늘 오전에 로그 확인결과 워드프레스의 xmlrpc.php 파일을 이용한 공격이었다. 현상은 504 에러가 발생하거나, 서버가 느려진다. 워드프레스 취약점으로 현재 해외 커뮤니티뿐만 아니라, 국내에서도 많이 발생하는 듯하다.

### 해결 방법

해결 방법은 해당파일에 접근하는 것을 .htaccess에서 수정하는 방법(apache 기준)과 파일이름을 변경하는 것이다. xmlrpc를 사용하지 않는 사람이라면 xmlrpc.php 이름을 변경하는 것을 추천한다. xmlrpc.php는 워드프레스 폴더 최상단에 위치해있고, ftp 또는 mv 명령으로 변경해주자.   설마 내 블로그에 공격따위가 들어올거라는 생각을 못했다. 당연히 내가 서버 설정을 잘못해서 그랬을 것이라고 생각했는데, 다음부터는 공격에도 염두를 두고 빠르게 취약점을 처리해 나가야 겠다. 참고 자료 :  [http://ilgob.com/ilgob_server/150671](http://antti.vilpponen.net/2013/08/26/how-to-mitigate-a-wordpress-xmlrpc-php-attack/ "nginx 파일이름 변경") , [http://antti.vilpponen.net/2013/08/26/how-to-mitigate-a-wordpress-xmlrpc-php-attack/](http://antti.vilpponen.net/2013/08/26/how-to-mitigate-a-wordpress-xmlrpc-php-attack/ "nginx apache 변경") nginx에서 504 에러에 관련한 설정 팁 : [http://www.nginxtips.com/504-gateway-time-out-using-nginx/](http://www.nginxtips.com/504-gateway-time-out-using-nginx/ "nginx 504 변경")