---
layout: post
title: "FTPClient doCommand와 문자 인코딩 확인"
date: 2016-02-13 00:00:00 +0900
categories: Know-how 
---


## FTPClient를 이용해 특수한 FTP 명령 사용하기

FTPClient는 apache-commons-net 라이브러리중의 일부이다.

FTPClient에선 기본적으로 FTP통신에서 사용하는 명령(commnad)들을 메소드로 구현해 두었지만

모든 명령이 구현되어 있지는 않다.

이런 특수한 명령의 경우 FTPClient안에 doCommand 메소드를 사용하면 해결이 가능하다. 

예를 들어, option 값을 주는 명령 OPTS에 "utf8 on"이라는 파라미터를 주고 싶으면

`ftpClient.doCommand("OPTS", "utf8 on");`

위와 같은 방법으로 사용하면 된다.

## doCommand를 이용해 문자 인코딩 확인

**<span class="marker">아래의 방법은 서버에서 클라이언트의 요청을 로그로 확인 할수 있을때만 사용이 가능하다.</span>**

보통 자바에서 인코딩을 아래와 같이 확인한다고 생각된다. 

(참고 : [http://gwons.blogspot.kr/2014/11/java.html](http://gwons.blogspot.kr/2014/11/java.html))

<pre>String originalStr = <span style="color:#6a8759">"Å×½ºÆ®"</span><span style="color:#cc7832">;</span> <span style="color:#808080">// 테스트</span> String [] charSet = {<span style="color:#6a8759">"utf-8"</span><span style="color:#cc7832">,</span><span style="color:#6a8759">"euc-kr"</span><span style="color:#cc7832">,</span><span style="color:#6a8759">"ksc5601"</span><span style="color:#cc7832">,</span><span style="color:#6a8759">"iso-8859-1"</span><span style="color:#cc7832">,</span><span style="color:#6a8759">"x-windows-949"</span>}<span style="color:#cc7832">;</span> 
<span style="color:#cc7832">for</span> (<span style="color:#cc7832">int</span> i=<span style="color:#6897bb">0</span><span style="color:#cc7832">;</span> i<charSet.<span style="color:#9876aa">length</span><span style="color:#cc7832">;</span> i++) {
    <span style="color:#cc7832">for</span> (<span style="color:#cc7832">int</span> j=<span style="color:#6897bb">0</span><span style="color:#cc7832">;</span> j<charSet.<span style="color:#9876aa">length</span><span style="color:#cc7832">;</span> j++) {
        <span style="color:#cc7832">try</span> {
            System._out_.println(<span style="color:#6a8759">"["</span> + charSet[i] +<span style="color:#6a8759">","</span> + charSet[j] +<span style="color:#6a8759">"] = "</span> + <span style="color:#cc7832">new</span> String(originalStr.getBytes(charSet[i])<span style="color:#cc7832">,</span> charSet[j]))<span style="color:#cc7832">;
</span> <span style="color:#cc7832"></span> } <span style="color:#cc7832">catch</span> (UnsupportedEncodingException e) {
            e.printStackTrace()<span style="color:#cc7832">;
</span> <span style="color:#cc7832"></span> }
    }
}</pre>

위와 같은 소스를 살짝 변경하여, doCommand를 이용해서 서버에 찍히는 로그로 인코딩을 확인하게끔 구현할 수 있다.

<pre>String testStr = <span style="color:#6a8759">"테스트_해볼_문자열"</span><span style="color:#cc7832">;</span> <span style="color:#808080">// 테스트 할 문자열</span> String [] charSet = {<span style="color:#6a8759">"utf-8"</span><span style="color:#cc7832">,</span><span style="color:#6a8759">"euc-kr"</span><span style="color:#cc7832">,</span><span style="color:#6a8759">"ksc5601"</span><span style="color:#cc7832">,</span><span style="color:#6a8759">"iso-8859-1"</span><span style="color:#cc7832">,</span><span style="color:#6a8759">"x-windows-949"</span>}<span style="color:#cc7832">;</span> 
<span style="color:#cc7832">for</span> (<span style="color:#cc7832">int</span> from = <span style="color:#6897bb">0</span><span style="color:#cc7832">;</span> from < charSet.<span style="color:#9876aa">length</span><span style="color:#cc7832">;</span> from++) {
    <span style="color:#cc7832">for</span> (<span style="color:#cc7832">int</span> to = <span style="color:#6897bb">0</span><span style="color:#cc7832">;</span> to < charSet.<span style="color:#9876aa">length</span><span style="color:#cc7832">;</span> to++) {
        <span style="color:#cc7832">try</span> {
            ftpClient.doCommand(<span style="color:#6a8759">"TEST"</span><span style="color:#cc7832">,</span><span style="color:#6a8759">"["</span> + charSet[from] +<span style="color:#6a8759">","</span> + charSet[to] +<span style="color:#6a8759">"] = "</span> <span style="color:rgb(204, 120, 50)">// 커맨드를 보내 로그를 찍게 끔 한다.</span> <span style="color:#6a8759"></span> + <span style="color:#cc7832">new</span> String(testStr.getBytes(charSet[from])<span style="color:#cc7832">,</span> charSet[to]))<span style="color:#cc7832">; 
</span> <span style="color:#cc7832"></span> } <span style="color:#cc7832">catch</span> (UnsupportedEncodingException e) {
            e.printStackTrace()<span style="color:#cc7832">;
</span> <span style="color:#cc7832"></span> }
    }
}</pre>

서로 인코딩 방식이 같음에도 불구하고 한글이 깨지거나 한다면 위와 같은 방법으로 확인해보자!

+ 위의 방법은 확인을 위한 것이지 **절대 charset 변환이 아니라는 것**을 명심하자. 

(참고 : [http://blog.javarouka.me/2011/09/new-string.htm](http://blog.javarouka.me/2011/09/new-string.htm)[l](http://blog.javarouka.me/2011/09/new-string.html))

_<tt><samp>P.S. 얼른 하나의 문자 인코딩이 모든 문자인코딩을 통합해서 더이상 인코딩 걱정없이 프로그래밍 할 날을 꿈꿔본다 ㅠㅠ</samp></tt>_