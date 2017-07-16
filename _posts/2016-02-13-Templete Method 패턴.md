---
layout: post
title: "Templete Method 패턴"
date: 2016-02-13 00:00:00 +0900
categories: Know-how 
---


### 3\. Templete Method 패턴

템플릿의 기능을 가진 패턴입니다. **상위 클래스쪽에 템플릿에 해당하는 메소드가 정의**되어 있고, 그 메소드의 정의 안에는 **추상 메소드**가 사용되고 있다. 따라서 **상위 클래스의 프로그램만 보면 추상 메소드를 어떻게 호출하고 있는지 알 수 있지만, 최종적으로 어떤 처리가 수행되는지는 알 수 없다.** **추상 메소드**를 **실제 구현하는 것**은 **하위 클래스,** 하위 클래스 측에서 **메소드를 구현하면 구체적인 처리가 결정**된다. 이와 같이 상위 클래스에서 처리의 뼈대를 결정하고, 하위 클래스에서 내용을 결정하는 디자인 패턴을 Templete Method 패턴 이라고 한다.  

#### 예제 프로그램

Templete Method 패턴을 사용한 예제 프로그램으로, 문자나 문자열을 5회 반복하여 표시하는 프로그램이다. (클래스 다이어 그램등, 상세한것은 책 71,72p 참고) (실제 동작이 어렵지는 않으므로 동작에 대한 상세한 설명은 생략한다. 소스만 봐도 충분히 이해가 가능하다.)

##### AbstractDisplay 클래스

AbstractDisplay 클래스는 open, print, close, display라는 메소드를 가지고 있다. 이중에서 open, print, close 는 추상 메소드이고 display 메소드 만이 구현되어 있다.

<pre>//메소드 display만 구현되고 있는 추상 클래스
public abstract class AbstractDisplay { 
	// 하위 클래스에 구현을 맡기는 추상 메소드들
	protected abstract void open();
	protected abstract void print();
	protected abstract void close();

	//추상 클래스에서 구현되고 있는 메소드 display
	public final void display(){
		open(); 					// open 한 뒤 
		for (int i = 0; i < 5; i++){// print를 5번 반복하고
			print(); 
		}
		close(); // ... 마지막으로 close한다. 이것이 display 메소드에서 구현되고 있는 내용.
	}

}
</pre>

##### CharDisplay

AbstractDisplay 클래스에서 추상 메소드였던 open, print, close를 구현한 하위 클래스이다.

<pre>//AbstractDisplay의 메소드를 실제 구현하고 있는 클래스
public class CharDisplay extends AbstractDisplay{
	private char ch; // 표시해야할 문자

	public CharDisplay(char ch){
		this.ch = ch;
	}

	protected void open(){ // 상위 클래스에서는 추상메소드이지만 오버라이드해서 구현한다. 
		System.out.print("<<"); // 개시 문자열 "<<" 을 표시한 	} 	 	protected void print(){// 상위 클래스에서는 추상메소드이지만 오버라이드해서 구현한다.  		System.out.print(ch); // 필드에 ch 문자를 표시한다. 	} 	 	protected void close(){// 상위 클래스에서는 추상메소드이지만 오버라이드해서 구현한다.  		System.out.println(">>"); // 종료 문자열 ">>" 을 표시한다.
	}
</pre>

##### StringDisplay

open과 close 메소드에서 printLine을 호출하여 받아진 문자열만큼 위아래 틀을 잡는다. print 메소드에서는 문자열을 | 로 감싸서 출력한다.

<pre>//AbstractDisplay의 메소드를 실제 구현하고 있는 클래스
public class StringDisplay extends AbstractDisplay{
	private String string;
	private int width;
	public StringDisplay(String string){
		this.string = string;
		this.width = string.getBytes().length;
	}
	protected void open(){
		printLine();
	}

	protected void print(){
		System.out.println("|" + string + "|");
	}

	protected void close(){
		printLine();
	}

	private void printLine(){
		System.out.print("+");
		for (int i = 0; i < width; i++){
			System.out.print("-");
		}
		System.out.println("+");
	}
}
</pre>

##### Main 클래스

CharDisplay 클래스와 StringDisplay 클래스의 인스턴스를 만들어 display 메소드를 호출한다. (아래의 소스 주석을 참조하자)

<pre>public class Main {
	public static void main(String[] args) {
		// 'H'를 가진 CharDisplay 인스턴스를 1개 생성
		AbstractDisplay d1 = new CharDisplay('H');

		//각각 "Hello,world."와 "안녕하세요." 인스턴스를 1개씩 생
		AbstractDisplay d2 = new StringDisplay("Hello, world.");
		AbstractDisplay d3 = new StringDisplay("안녕하세요.");

		d1.display(); //d1, d2, d3 모두 같은 AbstractDisplay의 인스턴스이기 때문 
		d2.display(); //상속한 display 메소드를 호출할수 있다.
		d3.display(); //실제 동작은 하위 클래스에서 결정한
	}
}
</pre>

#### Template Method 패턴의 등장인물

AbstractClass(추상 클래스)의 역할 예제에서는 AbstractDisplay에 해당하는 클래스로서 AbstractClass는 템플릿 메소드를 구현하고, 템플릿 메소드 안에 사용하고 있는 추상메소드를 선언한다. 이 추상 메소드는 ConcreteClass 역할에 의해 구현된다.

ConcreteClass(구현 클래스)의 역할 예제에서 CharDisplay, StringDisplay에 해당하는 클래스로서 AbstractClass 역할에서 정의되어 있는 추상 메소드를 구체적으로 구현한다. 구현한 메소드는 AbstractClass의 템플릿 메소드에서 호출된다.

#### 사고 넓히기

##### 로직을 공통화 할수 있다.

Template Method 패턴을 사용할시 상위클래스에 템플릿 메소드에서 알고리즘이 기술되어 있으므로, 하위 클래스측에서는 일일이 알고리즘을 기술할 필요가 없어진다. 예를들어 Template Method 패턴을 사용하지 않고, 컨트롤 CV 신공으로 ConcreteClass 역할을 만들었다고 하자. 그때 만약 버그가 발견되어 수정된다고 하면 만들었던 ConcreteClass1, ConcreteClass2, ConcreteClass3 을 모두 수정해야 하지만 Templete Method 패턴으로 작성시에는 템플릿 메소드만 수정하면 된다.

##### 상위 클래스와 하위 클래스의 연계

Template Method 패턴에서는 상위 클래스가 깊은 관계로 작동하고 있고, 하위클래스가 구현된 메소드 호출이 상위 클래에서 어느 타이밍에 이루어 지는지 이해하고 있어야 한다. 따라서 상위 클래스의 소스가 없다면 하위클래스의 구현이 어려울수도 있다. …… 그외 패턴 사용에 대한 주의점 등은 책을 참고 하고 포스팅에는 생략한다. …… 3-1 java.io.InputStream 클래스에서는 Template Method 패턴이 사양되고 있습니다. 사양서(JDK의 API 레퍼런스)를 읽고 java.io.InputStream의 하위 클래스에서 구현할 메소드는 무엇인지 조사하십시오. 내가 생각한 풀이: api 문서를 확인한 결과 abstract int read() 메소드가 있었는데 read 메소드를 하위클래스에 맡기는 듯 보인다. 책 해설: 하위 클래스에서 구현할 것을 요청받고 있는 것은 java.io.InputStream의 read() 메소드입니다. read() 메소드는 java.io.InputStream의 템플릿 메소드인 read(byte[] b, int off, int len)에 의해 반복 호출된다. 결국'1바이트 읽기'라는 처리의 구체적인 내용은 하위 클래스에게 맡기고, java.io.InputStream 쪽에서는 '지정한 바이트를 배열의 지정 위치로 읽어들인다'는 처리의 템플릿을 만들게 됩니다. 3-2 예제 프로그램의 AbstractDisplay클래스의 메소드는

<pre>public final void display(){
.......
}
</pre>

으로 구현되어 있습니다. 여기에서 final은 무엇을 나타내고 있습니까? 내가 생각한 풀이: final로 선언함으로서 하위 클래스에서 해당 메소드를 변경치 못하게 하려는 의도 같다. 책 해설: display 메소드를 하위 클래스에서 오버라이드할 수 없는 것을 나타내고 있습니다. ...(중략)... Gof 책에는 템플릿 메소드는 오버라이드 하지 말아야 한다고 쓰여있습니다. 메소드의 오버라이드를 금지하려면 예제 프로그램처럼 final 선언을 하면 됩니다. 3-3 예제 프로그램에서 open, print, close 메소드를 상속관계 밎 동일 패키지에 있는 클래스에서만 호출할 수 있도록 하고, 관계 없는 다른 클래스에서는 호출하지 않도록 합니다. 어떻게 하면 될까요? 내가 생각한 풀이: 추상메소드를 protected로 선언하면 될것 같은데.... 책 해설: (내가 생각한 풀이와 동일) 3-4 Java의 인터페이스는 추상 클래스와 매우 비슷합니다. 인터페이스도 추상 메소드의 집합이지만, Template Method 패턴에서는 AbstractClass 역할에 인터페이스를 사용할 수 없습니다. 왜 그럴까요? 내가 생각한 풀이: 인터페이스는 추상 메소드만 선언할 수 있으므로 템플릿 메소드 선언이 불가하다. 책 해설: (내가 생각한 풀이와 동일)