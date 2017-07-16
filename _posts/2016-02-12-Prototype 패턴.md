---
layout: post
title: "Prototype 패턴"
date: 2016-02-12 00:00:00 +0900
categories: Know-how 
---


### 6\. Prototype 패턴

Something 인스턴스를 작성할 때는

<pre>new something()</pre>

와 같이 작성한다. new 라는 Java 언어의 키워드를 사용해서 클래스 이름을 반드시 지정해야 합니다. 그러나 클래스 이름을 지정하지 않고 인스턴스를 생성할 때도 있습니다. 다음과 같은 경우에는 클래스로부터 인스턴스를 만드는 것이 아니라 인스턴스를 복사해서 새로운 인스턴스를 만듭니다.

종류가 너무 많아 클래스로 정리되지 않는 경우 취급하는 오브젝트의 종류가 너무 않아 각각을 별도의 클래스로 만들어 다수의 소스파일을 작성해야 하는 경우

클래스로부터 인스턴스 생성이 어려운 경우 생성하고 싶은 인스턴스가 복잡한 작업을 거쳐 만들어지기 때문에 클래스로부터 만들기가 매우 어려운 경우이다. 예를 들어 그래픽 에디터 등에서 사용자가 마우스를 조작해서 만든 도형을 나타내는 인스턴스가 있다고 가정합니다. 사용자의 조작으로 생성되는 인스턴스를 프로그래밍해서 만드는 것은 곤란하다. 사용자의 조작으로 생성되는 인스턴스를 다시 만들고 싶은 경우에 지금 인스턴스를 일단 저장해 둔뒤 만들고 싶을 경우게 그것을 복사한다.

framwork와 생성할 인스턴스를 분리하고 싶은 경우 인스턴스를 생성할 때의 framework 를 특정 클래스에 의존하지 않도록 만들고 싶은 경우이다. 이와 같은 경우에는 클래스 이름을 지정해서 인스턴스를 만드는 것이 아니라 이미 '모형'이 되는 인스턴스를 등록해 두고, 그 등록된 인스턴스를 생성한다.

'인스턴스로부터 다른 인스턴스를 만드는 것'은 '복사기를 사용해서 서류를 복사하는 일'과 비슷합니다. 원래의 서류를 어떻게 만들었는지 몰라도 복사기로 같은 종류의 서류를 몇 장이라도 만들 수 있습니다. 이 장에서는 '클래스로부터 인스턴스를 생성하는 것'이 아니라 '인스턴스로부터 별도의 인스턴스를 만드는 **Prototype 패턴**'에 대해서 배워보자. 또 Java 언어에서 clone 메소드와 cloneable 인터페이스의 사용방법에 대해 알아보자. (1번 패턴에서 소개한 Iterator 패턴도 사실 현재 java 인터페이스를 사용하여 구현할 수 있지만, 왠지 책에는 따로 설명되어 있지 않다.)

#### 예제 프로그램

Prototype 패턴을 사용한 예제 프로그램을 보자. 다음 프로그램은 문자열을 테두리 선으로 감싸거나 밑줄을 표시하는 프로그램이다. 'Product' 인터페이스와 'Manager' 클래스는 "framework 패키지"에 있고 인스턴스를 복제한다. Manager 클래스는 createclone을 호출하지만 구체적으로는 어느 클래스의 인스턴스를 복제하는지는 관여하지 않는다. Product인터페이스를 구현하고 있는 클래스라면 그 인스턴스를 복제할 수 있다. MessageBox 클래스와 UnderlinePen클래스는 모두 Product 인터페이스를 구현하고 있는 클래스이다. 이 인스턴스를 만들어서 Manager 클래스에 등록해 두면 필요할 때 복제 할 수 있다. (클래스 다이어그램 및 기타 정보는 책 105p, 106p 참고)

##### Product 인터페이스

Product 인터페이스는 "java.lang.Cloneable 인터페이스를 상속"하고 있으며, 복제를 가능하게 합니다. 이 인터페이스를 구현하고 있는 클래스의 인스턴스는 Clone 메소드를 사용해서 자동적으로 복제가 가능하다. use 메소드는 '사용하기'위한 것이다. '사용하기'가 무엇을 의미하는지는 하위 클래스의 구현에 맡겨져 있다. createClone 메소드는 인스턴스를 복제하기 위한 것이다.

<pre>package org.jellydiss.patternpractice.prototype.framework;

//추상 메소드 use와 createClone이 선언되어 있는 인터페이스
public interface Product extends Cloneable {
	public abstract void use(String s);
	public abstract Product createClone();
}
</pre>

##### Manager 클래스

Manager 클래스는 Product 인터페이스를 이용해서 인스턴스의 복제를 실행하는 클래스이다. showcase 필드는 인스턴스의 '이름'과 '인스턴스'의 대응관계를 java.util.HashMap으로 표현하였다. register메소드에서 제품의 이름과 Product 인터페이스가 주어지면 그 한 쌍을 showcase에 등록한다. 이때 인수에 전달되는 Product 형의 실제 클래스는 모르지만 Product 인터페이스를 구현한 클래스의 인스턴스(즉, use 메소드나 createClone 메소드를 호출할 수 있는 인스턴스)라는 걸 알 수 있다. Product 인터페이스나 Manager 클래스의 소스에 MessageBox 클래스나 UnderlinePen 클래스의 클래스 이름이 전혀 나오지 않는점에 주의하자. 클래스 이름이 나오지 않는 다는건 **Product와 Manager는 그들의 클래스와는 독립적으로 수정**할 수 있다는 것을 의미한다. **소스안에 클래스의 이름을 쓰면 그 클래스와 밀접한 관계가 생긴다.** (이번 예제에서도 책과 맞추기 위하여 제네릭을 따로 사용하지 않았다.)

<pre>package org.jellydiss.patternpractice.prototype.framework;
import java.util.*;

//createClone을 사용해서 인스턴스를 복제하는 클래스
public class Manager {
	private HashMap showcase = new HashMap();

	public void register(String name, Product proto){
		showcase.put(name, proto);
	}

	public Product create(String protoname){
		Product p = (Product)showcase.get(protoname);
		return p.createClone();
	}
}

</pre>

##### MessageBox 클래스

하위 클래스의 동작은 소스를 보고 파악하기 바란다.(동작에 대한 상세 설명은 108p ~ 110p) 아래의 소스에서 clone메소드를 사용시 예외처리를 하였는데 사실 Product 인터페이스는 Cloneable 인터페이스를확장한 것이기 때문에 CloneNotSupportedException이 발생하지 않는다. 또한 Cloneable 인터페이스는 단지 표식을 위해 사용하기 때문에 내부에서 선언하고 있는 메소드는 없다. Java 에서 clone 메소드는 자신의 클래스(혹은 하위 클래스)에서만 호출할 수 있기 때문에 다른 클래스의 요청으로 복제를 하는 경우에는 createClone과 같은 다른 메소드를 이용해서 clone을 기술할 필요가 있다.

<pre>package org.jellydiss.patternpractice.prototype;

import org.jellydiss.patternpractice.prototype.framework.Product;

//문자열을 테두리로 표시하는 클래스. use와 createClone을 구
public class MessageBox implements Product{
	private char decochar;

	public MessageBox(char decochar){
		this.decochar = decochar;
	}

	public void use(String s){
		int length = s.getBytes().length;
		for (int i = 0; i < length + 4; i++){
			System.out.print(decochar);
		}
		System.out.println();
		System.out.println(decochar + " " + s + " " + decochar);
		for(int i = 0; i < length + 4; i++){
			System.out.print(decochar);
		}
		System.out.println();
	}

	public Product createClone(){
		Product p = null;
		try {
			p = (Product)clone();
		} catch (CloneNotSupportedException e){
			e.printStackTrace();
		}
		return p;
	}
}
</pre>

##### UnderlinePen 클래스

UnderinePen 클래스는 MessageBox와 거의 같은 동작을 한다. 기능적으로만 다르기 때문에 이 역시 설명을 생략하고 상세한 것은 책을 참고하자.

<pre>package org.jellydiss.patternpractice.prototype;

import org.jellydiss.patternpractice.prototype.framework.Product;

//문자열에 밑줄을 표시하는 클래스. use와 createClone을 구현
public class UnderlinePen implements Product{
	private char ulchar;

	public UnderlinePen(char ulchar){
		this.ulchar = ulchar;
	}

	public void use(String s){
		int length = s.getBytes().length;
		System.out.println("\"" + s + "\"");
		System.out.print(" ");
		for (int i = 0; i < length; i++){
			System.out.print(ulchar);
		}
		System.out.println();
	}
	public Product createClone(){
		Product p = null;
		try {
			p= (Product)clone();
		} catch (CloneNotSupportedException e) {
			e.printStackTrace();
		}
		return p;
	}
}
</pre>

##### Main 클래스

Main 클래스 에서는 우선 Manager의 인스턴스슬 만든다. 그리고 Manager의 인스턴스에 대해서 UnderlinePen의 인스턴스와 MessageBox의 인스턴스에 이름을 붙여서 등록한다.

<pre>package org.jellydiss.patternpractice.prototype;

import org.jellydiss.patternpractice.prototype.framework.Manager;
import org.jellydiss.patternpractice.prototype.framework.Product;

public class Main {
	public static void main(String[] args) {
		// 준비 
		Manager manager = new Manager();
		UnderlinePen upen = new UnderlinePen('~');
		MessageBox mbox = new MessageBox('*');
		MessageBox sbox = new MessageBox('/');
		manager.register("strong message", upen);
		manager.register("warning box", mbox);
		manager.register("slash box", sbox);

		// 생성
		Product p1 = manager.create("strong message");
		p1.use("Hello world.");
		Product p2 = manager.create("warning box");
		p2.use("Hello, world.");
		Product p3 = manager.create("slash box");
		p3.use("Hello, world");

	}
}
</pre>

#### Prototype 패턴의 등장인물

Prototype(원형)의 역할 예제의 Product에 해당하며, 인스턴스를 복사하여 새로운 인스턴스를 만들기 위한 메소드를 "결정"한다.

ConcretePrototype(구체적인 원형)의 역할 예제의 MesssageBox 클래스나 UnderlinePen 클래스에 해당되며, 인스턴스를 복사해서 새로운 인스턴스를 만드는 메소드를 실제로 "구현"한다.

Client(이용자)의 역할 예제의 Manager에 해당되며, 인스턴스를 복사하는 메소드를 이용해서 새로운 인스턴스를 만든다.

#### 사고 넓히기

##### 클래스에서 인스턴스를 만들면 안되는 걸까?

Prototype이 필요한 이유를 알아보기 위해 위에 언급한 경우를 다시 한번 봐보자

종류가 너무 많아 클래스로 정리되지 않는 경우 예제 프로그램에서는 3개의 모형이 등장한다. '~'을 사용해서 밑줄을 긋는 것, '*'을 사용해서 문자열에 테두리를 긋는것, '/'을 사용해서 문자열에 테두리를 긋는 것 예제는 간단한 것이기 때문에 모형이 3개이지만, 얼마든지 많은 종류의 모형을 만들수 있다. 그러나 이것을 모두 각각의 클래스로 만들게 되면 클래스의 수가 너무 많아져서 소스를 관리하기 힘들어진다.

클래스로부터 인스턴스 생성이 어려운 경우 이번 예제로는 실감하기 어렵지만, 마우스를 사용하여 도형에디터와 같은 어플리케이션을 상상하면 이해가 쉽다. 사용자가 조작해서 만든 도형을 나타내는 인스턴스와 같은것을 만든다면, 이때 클래스를 사용하는 것이 아니라 인스턴스를 복사해서 만드는 방법이 간단하다.

framwork와 생성할 인스턴스를 분리하고 싶은 경우 예제 프롬그램에서는 인스턴스의 복사(clone)를 실행하는 부분을 framework 패키지 안에 설정하고 있다. Manager 클래스의 create 메소드에는 클래스 이름 대신 'strong message'나 'slash box'라는 문자열을 인스턴스 생성을 위한 이름으로 제공한다. 이겅은 java가 구비하고 있는 인스턴스 생성기구인 new Something()이라는 형식을 보다 넓게 이용해서 클래스 이름의 속박으로부터 framework를 분리할 수 있다.

##### 클래스 이름은 속박인가

소스 프로그램 내부에 클래스 이름이 쓰여있으면 문제가 생긴다. 소스코드 내부에 이용하는 클래스 이름을 쓰는것은 당연하다고 생각할수 있지만 객체지향 프로그래밍의 목표 중 하나인 "부품으로써 재이용"이라는 점을 다시 한번 상기할 필요가 있다. 소스 내부에 이용할 클래스의 이름을 쓰는 것이 항상 나쁜 것만은 아니다. 그러나 소스 내부에 이용할 클래스의 이름이 쓰여있으면 그 클래스와 분리해서 재이용할 수 없게 된다. Java 에서는 클래스 파일(.class)만 가지고 있더래도 그 클래스를 재이용할 수 있는지가 중요하다. 다시 말해 소스 파일(.java)이 없어도 재상용할 수 있는지가 포인트이다. …… 그외 패턴 사용에 대한 주의점 등은 책을 참고 하고 포스팅에는 생략한다. …… 6-1 예제 프로그램에서는 MessageBox 클래스와 UnderlinePen 클래스에 같은 동작을 하는 createClone 메소드가 정의되어 있다. 같은 동작을 하는 메소드가 하나의 프로그램에 복수로 정의되어 있는 것은 관리상 권장할 만한 것이 아니므로 이 메소드를 공유하고 싶다고 한다. 어떻게 하면 좋을까? 내가 생각한 답 : Product를 추상 클래스로 만든 뒤 하위클래스에서는 상속받는 식으로 템플릿 메소드 패턴을 이용하여 처리해 준다 **Product**

<pre>public abstract class Product implements Cloneable {
	public abstract void use(String s);
	public Product createClone(){
		Product p = null;
		try {
			p= (Product)clone();
		} catch (CloneNotSupportedException e) {
			e.printStackTrace();
		}
		return p;
	}
}
</pre>

**MessageBox**

<pre>public class MessageBox extends Product{
	private char decochar;

	public MessageBox(char decochar){
		this.decochar = decochar;
	}

	public void use(String s){
		int length = s.getBytes().length;
		for (int i = 0; i < length + 4; i++){
			System.out.print(decochar);
		}
		System.out.println();
		System.out.println(decochar + " " + s + " " + decochar);
		for(int i = 0; i < length + 4; i++){
			System.out.print(decochar);
		}
		System.out.println();
	}

}
</pre>

**UnderlinePen**

<pre>public class UnderlinePen extends Product{
	private char ulchar;

	public UnderlinePen(char ulchar){
		this.ulchar = ulchar;
	}

	public void use(String s){
		int length = s.getBytes().length;
		System.out.println("\"" + s + "\"");
		System.out.print(" ");
		for (int i = 0; i < length; i++){
			System.out.print(ulchar);
		}
		System.out.println();
	}

}
</pre>

책 해설 : 두 가지 방법이 나와 있으며 하나는 위와 같고 다른 하나는 두 클래스의 상위클래스인 ConcreteProduct 클래스를 정의 하여 그 내부에서 createClone 메소드를 구현한다. (두 방법 모두 메소드를 상속에 의해 공유한다.) 6-2 java.lang.Object 클래스는 clone 메소드를 가지고 있다. java.lang.Object는 java.lang.Cloneable 인터페이스를 구현하고 있습니까? 내가 생각한 답 : Object 클래스는 Cloneable 클래스를 구현하고 있지 않으며 그로인해 새로 생성한 클래스에서 clone 메소드를 바로 사용할경우 CloneNotSupportedException을 던진다. 책 해설 : 구현하고 있지 않다. Oblect 클래스가 Cloneable 인터페이스를 구현하고 있다면 어떤 클래스의 인스턴스에서 clone메소드를 호출해도 CloneNotSuttortedException을 제공되지 않는다. (훗 내 답이랑 거의 똑같다. 헿)