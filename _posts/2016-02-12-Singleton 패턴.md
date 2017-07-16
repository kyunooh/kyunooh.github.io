---
layout: post
title: "Singleton 패턴"
date: 2016-02-12 00:00:00 +0900
categories: Know-how 
---


### 5\. Singleton 패턴

프로그램을 실행 할 때 대부분 많은 인스턴스가 생성된다. 예를들어 String 클래스의 인스턴스는 문자열 1개에 대해서 1개가 생성되기 때문에 문자열이 1000개 등장하는 프로그램이라면 1000개의 인스턴스가 만들어진다. 하지만 **'클래스의 인스턴스가 단 하나만 필요'한 경우**도 있는데, 그것은 **시스템 안에서 1개밖에 존재하지 않는 것**을 프로그램으로 표현하고 싶을 때이다. 예를 들어 컴퓨터 자체를 표현한 클래스, 현재의 시스템 설정을 표현한 클래스, 윈도우 시스템을 표현한 클래스 등이다. 물론 주의하여 new MyClass()가 1회만 실행되도록 작성하면 MyClass의 인스턴스는 1개만 생성된다. 하지만 프로그래머가 주의하여 1개만 생성하는것이 아니라

지정한 클래스의 인스턴스가 '절대로' 1개밖에 존재하지 않는 것을 '보증'하고 싶을때

인스턴스가 1개밖에 존재하지 않는 것을 프로그램 상에서 표현하고 싶을 때

는 어떻게 해야할까? 위와 같이 한개밖에 존재하지 않는 것을 보증하는 패턴을 **Singleton 패턴**이라고 한다. singleton이란 '요소를 1개밖에 가지고 있지 않은 집합'을 의미한다. 인스턴스가 한 개 밖에 없기 때문에 이와 같은 이름이 붙었다.

#### 예제 프로그램

Singleton 예제에서는 Main 클래스를 포함하여 총 두가지 클래스 밖에 없지만 주의해서 보자.

##### Singleton 클래스

Singleton 클래스에서는 인스턴스를 1개 밖에 만들 수 없으며, singleton은 static 필드(class 변수)로서 Sindleton 클래스의 인스턴스에서 초기화 된다. 이 초기화는 Singleton 클래스를 로드할 때 1회만 실행된다. Singleton 클래스의 생성자는 private로 되어 있다. 이것은 Singleton 클래스 외부에서 생성자의 호출을 금지하기 위해서이다. 만약 외부클래스에서 new Singleton() 과 같이 사용할경우 컴파일 에러가 발생한다. 처음부터 프로그래머가 주의해서 new 하지 않도록 하면 생성자를 private로 할 필요는 없다. 하지만 그렇다면 Singleton패턴의 의미가 사라진다. Singleton 패턴은 프로그래머가 실수 해도 인스턴스가 1개만 생성되도록 보증하는 패턴이며, 그렇기에 private로 해둘 필요가 있다. 예제에서는 생성자 안에 인스턴스를 생성한다고 메시지를 표시한다. Singleton 클래스의 유일한 인스턴스를 얻는 메소드로는 getInstance가 준비되어 있다. 꼭 이름이 getInstance 일 필요는 없지만, 인스턴스를 얻을 메소드는 필요하다.

<pre>//인스턴스가 1개만 존재하는 클래스
public class Singleton {
	private static Singleton singleton = new Singleton();
	private Singleton() {
		System.out.println("인스턴스를 생성했습니다.");
	}

	public static Singleton getInstance(){
		return singleton;
	}
}
</pre>

##### Main 클래스

Singleton 클래스를 이용하는 클래스이다. singleton 클래스의 getInstance 메소드를 사용해서 Singleton의 인스턴스를 얻고 있다. getInstance 메소드는 두번 호출하고, 반환값은 각각 obj1, obj2에 대입되고, 확실히 같은 인스턴스인지 검사한다.

<pre>public class Main {
	public static void main(String[] args) {
		System.out.println("Start.");
		Singleton obj1 = Singleton.getInstance();
		Singleton obj2 = Singleton.getInstance();
		if(obj1 == obj2){
			System.out.println("obj1과 obj2는 같은 인스턴스입니다.");
		} else {
			System.out.println("obj1과 obj2는 다른 인스턴스입니다.");
		}
		System.out.println("End.");
	}
}
</pre>

[caption id="attachment_197" align="alignnone" width="386"][![Singleton Main 클래스 실행화면](http://jasns.cafe24.com/wp-content/uploads/2014/08/스크린샷-2014-08-06-오후-10.18.38.png)](http://jasns.cafe24.com/wp-content/uploads/2014/08/스크린샷-2014-08-06-오후-10.18.38.png) Singleton Main 클래스 실행화면[/caption]

#### Singleton 패턴의 등장인물

Singleton의 역할 Singleton 패턴에는 Singleton의 역할만이 존재한다. Singleton 역할은 유일한 인스턴스를 얻기 위한 static 메소드를 가지고 있다. 이 메소드는 언제나 동일한 인스턴스를 반환한다.

#### 사고 넓히기

##### 왜 제한할 필요가 있는가

Singleton 패턴에서는 인스턴스의 수를 제한하고 있다. 왜 일부러 제한적인 프로그래밍을 하는 것일까? '제한을 한다는 것'은 '전제가 되는 조건을 늘린다'는 의미다. 복수의 인스턴스가 존재하면 인스턴스들이 서로 영향을 미치고, 뜻하지 않은 버그가 발생할 가능성이있다. 그러나 인스턴스가 1개밖에 없다라는 보증이 있으면 그 전제조건 아래에서 프로그래밍할 수 있다.

##### 유일한 인스턴스는 언제 생성되는가

예제 프로그램 실행결과를 보면 'Start'라고 표시된 뒤 '인스턴스를 생성했습니다.' 라고 표시된다. 프로그램의 실행 개시 후 '최초로 getInstance메소드를 호출'했을때 'Singleton클래스는 초기화' 된다. 그리고 이때 'static 필드의 초기화가 이루어지고 유일한 인스턴스'가 만들어진다. 다음 TicketMaker 클래스는 getNextTicketNumber 메소드를 호출할 때마다 1000, 1001, 1002, ...라는 수를 순서대로 반환하기 위한 것입니다. Ticket의 번호나 일련번호를 생성할 때 사용합니다. 이 TicketMaker 클래스는 현재 상태라면 인스턴스를 몆 개라도 만들 수 있습니다. 여기에 Singleton 패턴을 적용해서 인스턴스가 하나만 만들어지도록 하십시오.

<pre>public class TicketMaker {
	private int ticket = 1000;
	public int getNextTicketNumber(){
		return ticket++;
	}
}
</pre>

내가 생각한 답안

<pre>public class TicketMaker {
	private static TicketMaker ticketMaker = new TicketMaker();

	private int ticket = 1000;

	private TicketMaker(){}

	public static TicketMaker getInstance(){
		return ticketMaker;
	}

	public int getNextTicketNumber(){
		return ticket++;
	}
}
</pre>

책 해설 : Singleton 패턴의 주제에서는 조금 벗어나지만, 여기에서는 getNextTicketNumber를 **synchronized 메소드로 했습니다**. 이것은 getNextTicketNumber 메소드가 복수의 스레드에서 호출되어도 올바로 작동시키기 위한 조치입니다. 만약, synchronized를 사용하지 않으면 복수의 스레드에 대해서 같은 값을 반환할 위험이 있다.

<pre>public class TicketMaker {
	private static TicketMaker ticketMaker = new TicketMaker();

	private int ticket = 1000;

	private TicketMaker(){}

	public static TicketMaker getInstance(){
		return ticketMaker;
	}

	public synchronized int getNextTicketNumber(){
		return ticket++;
	}
}
</pre>

5-2 인스턴스 개수가 3개로 한정되어 있는 클래스 Triple을 만드십시오. 인스턴스에는 0, 1, 2라는 번호가 붙어있고 getInstance(int id)에서 id번의 인스턴스가 얻어진다고 가정합니다.(이와 같은 클래스의 예는 제 10장 strategy 패턴의 Hand 클래스에도 나옵니다) 내가 생각한 답안 : 하드코딩해서 억지로 만들 수는 있을 것 같은데 공개하기 부끄러울 정도라 안쓸란다... (막상 해설을 봐보니 내가 생각한거와 크게 다르지 않았다... 너무 책에 기대를 많이 했나보다.) 책 해설 :

<pre>public class Triple {
	private static Triple[] triple = new Triple[]{
		new Triple(0),
		new Triple(1),
		new Triple(2)
	};

	private int id;

	private Triple(int id){
		System.out.println("The instance " + id + " is created.");
	}
	public static Triple getInstance(int id){
		return triple[id];
	}

	public String toString(){
		return "[Triple id=" + id +"]";
	}

}
</pre>

5-3 어떤 사람이 Singleton 클래스를 아래와 같이 만들었다. 그러나 엄밀하게는 Singleton 패턴이 아닌데 왜 그럴까? 내가 생각한 답 : 싱글톤 패턴은 클래스에 접근할때 바로 메모리에 올라가는 데 위와 같은 소스의 경우 null인 상태로 접근도 가능해서 그런것이 아닐까 생각된다. 책 해설: 다수의 스레드에서 거의 동시에 Singleton.getInstance메소드가 호출되었을 때, 다수의 인스턴스가 생성될 가능성이 있기 때문이다. (이번 챕터는 멀티쓰레드 경험이 별로 없는 나로써 synchronized 키워드에 대해서 신선한 경험을 한 것 같다.)

<pre>private static Singleton singleton = null;
	private Singleton() {
		System.out.println("인스턴스를 생성했습니다.");
	}
	public synchronized static Singleton getInstance(){
		if (singleton == null){
			singleton = new Singleton();
		}
		return singleton;
	}
}
</pre>