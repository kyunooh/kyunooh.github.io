## 문제 상황
![Unable to import maven porject](https://i.imgur.com/5Hn516K.png)
계속 위와 같은 메시지가 발생하여, maven의 오류라고 생각하고 있었다. 위에서 말하는 logs도 처음에 maven의 로그를 보라는 건 줄 알았다.하지만 커맨드로 하면 잘 된다. 즉 위의 로그는 IntelliJ의 로그를 의미하는 것이다.

상단 메뉴의 `Help >> Show Log in Finder(Explorer)`을 통해 log를 보면 다음과 같은 에러를 볼 수 있다.
```java
2018-02-21 11:20:53,806 [91110472]   INFO - figurations.GeneralCommandLine - Cannot run program "/Users/work/Library/Application Support/JetBrains/Toolbox/apps/IDEA-U/ch-0/173.4548.28/IntelliJ IDEA.app/Contents/jdk/Contents/Home/bin/java" (in directory "/Users/work/Library/Application Support/JetBrains/Toolbox/apps/IDEA-U/ch-0/173.4548.28/IntelliJ IDEA.app/Contents/bin"): error=2, No such file or directory 
java.io.IOException: Cannot run program "/Users/work/Library/Application Support/JetBrains/Toolbox/apps/IDEA-U/ch-0/173.4548.28/IntelliJ IDEA.app/Contents/jdk/Contents/Home/bin/java" (in directory "/Users/work/Library/Application Support/JetBrains/Toolbox/apps/IDEA-U/ch-0/173.4548.28/IntelliJ IDEA.app/Contents/bin"): error=2, No such file or directory
	at java.lang.ProcessBuilder.start(ProcessBuilder.java:1048)
	at com.intellij.execution.configurations.GeneralCommandLine.startProcess(GeneralCommandLine.java:415)
	at com.intellij.execution.configurations.GeneralCommandLine.createProcess(GeneralCommandLine.java:382)
...
...
Caused by: java.io.IOException: error=2, No such file or directory
	at java.lang.UNIXProcess.forkAndExec(Native Method)
	at java.lang.UNIXProcess.<init>(UNIXProcess.java:247)
	at java.lang.ProcessImpl.start(ProcessImpl.java:134)
	at java.lang.ProcessBuilder.start(ProcessBuilder.java:1029)
	... 46 more

```
즉 파일을 찾을 수 없는 게 문제. 예전에는 IntelliJ 안에 내장 JDK가 있었던 것 같은데 현재 EAP를 제외한 가장 최신버전(OSX, 2017.3.4)에는 누락되어 있는 것 같다. 다른 운영체제의 경우는 안해봐서 모르겠다. (툴박스로 설치해서 그런 줄 알았는데, 그냥 다이렉트로 다운 받아 설치한 것도 마찬가지다.) 그 와중에 프로젝트 설정을 다른 JDK로 바꿔도 적용이 안되어서, 마지막으로 해결한 방법은 ...

## 어디까지나 임시적인 해결방법
로컬에 설치된 JDK를 해당 경로에 복사해주는 것으로 문제를 해결할 수 있다.
![Imgur](https://i.imgur.com/91qDNcA.png)
혹시나 해서 겹치는 폴더의 경우는, 폴더 하나 씩 선택해서 따로 옮겨줌. 현재 일주일 정도는 아무 문제 없이 잘 사용중이다.
