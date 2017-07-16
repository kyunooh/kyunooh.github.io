---
layout: post
title: "Factory Method 패턴"
date: 2016-02-11 00:00:00 +0900
categories: Know-how 
---


### 4\. Factory Method 패턴

인스턴스 생성을 위한 공장을 [Template Method](http://jasns.cafe24.com/178 "Templete Method 패턴") 패턴으로 구성한 것이 Factory Method 패턴이다. Factory Method 패턴에서는 인스턴스를 만드는 방법을 상위 클래스 측에서 결정 하지만 구체적인 클래스 이름까지는 결정하지 않는다. 구체적인 내용은 모두 하위 클래스 측에서 수행 되며, 인스턴스 생생을 위한 골격(framework)과 실제의 인스턴스 생성을 분리해서 생각이 가능하다.

#### 예제 프로그램

신분증명서 카드(ID 카드)를 만드는 공장을 소재로하여, **Product** 클래스와 **Factory**클래스는 **framework라는 패키지**에 **IDCard** 클래스와 **IDCardFactory** 클래스는 구체적인 내용을 구현하여 idcard라는 패키지에 속한다.

인스턴스 생성의 framework 측(framework 패키지)

구체적인 내용을 구현하고 있는 측(idcard 패키지)

상세한 클래스 다이어 그램과 그내용은 책을 참고하자(p84,85)

##### Product 클래스

framework 패키지의 Product 클래스는 '제품'을 표현한 클래스이다. 이 클래스는 추상메소드 use만이 선언 되어있고, 구체적인 use의 구현은 모두 Product의 하위 클래스에 맡긴다.

<pre>package org.jellydiss.patternpractice.factorymethod.framework;

//추상메소드 use만 정의되어 있는 추상 클래스
public abstract class Product {
	public abstract void use();
}
</pre>

##### Factory 클래스

Factory 클래스는 Template Method를 사용한다.

<pre>//메소드 create를 구현하고 있는 추상 클래스 
package org.jellydiss.patternpractice.factorymethod.framework;

public abstract class Factory {

	//Product의 인스턴스를 생성해주고 제품을 만들어 등록하는 식으로 구현되어져 있다.
	public final Product create(String owner){
		Product p = createProduct(owner);
		registerProduct(p);
		return p;
	}

	// 아래의 두 메소드 모두 하위클래스에서 구현해준다.
	protected abstract Product createProduct(String owner); // 제품을 만드는 역할을 
	protected abstract void registerProduct(Product product); // 제품을 등록하는 역할을 함
}
</pre>

##### IDCard 클래스

framework와 패키지가 다르다는 것을 유의하여 클래스를 살펴보자.

<pre>package org.jellydiss.patternpractice.factorymethod.idcard;

import org.jellydiss.patternpractice.factorymethod.framework.Product;

//메소드 use를 구현하고 있는 클래스
public class IDCard extends Product {
	private String owner;
	IDCard(String owner){
		System.out.println(owner + "의 카드를 만듭니다.");
		this.owner = owner;
	}

	public void use(){
		System.out.println(owner + "의 카드를 사용합니다.");
	}
	public String getOwner(){
		return owner;
	}

}
</pre>

##### IDCardFactory 클래스

Factory 클래스의 두 추상 메소드를 구현하여 사용하는 클래스 (책이 이전에 쓰여져서 ArrayList와 List를 사용시에 제네릭에 관하여 컴파일러에서 경고하지만 패턴을 익히는 데 문제가 없으므로 일단 수정 없이 책처럼 작성하였다.)

<pre>package org.jellydiss.patternpractice.factorymethod.idcard;

import java.util.ArrayList;
import java.util.List;

import org.jellydiss.patternpractice.factorymethod.framework.Factory;
import org.jellydiss.patternpractice.factorymethod.framework.Product;

public class IDCardFactory extends Factory{
	private List owners = new ArrayList();

	protected Product createProduct(String owner){ // Factory 클래스의 createProduct 구현 
		return new IDCard(owner);
	}
	protected void registerProduct(Product product){ // Factory 클래스의 registerProduct 구 
		owners.add(((IDCard)product).getOwner());
	}
	public List getOwners(){
		return owners;
	}
}
</pre>

##### Main 클래스

IDCard 를 만들어 사용해보는 클래스

<pre>package org.jellydiss.patternpractice.factorymethod;

import org.jellydiss.patternpractice.factorymethod.framework.Factory;
import org.jellydiss.patternpractice.factorymethod.framework.Product;
import org.jellydiss.patternpractice.factorymethod.idcard.IDCardFactory;

public class Main {
	public static void main(String[] args) {
		Factory factory = new IDCardFactory();
		Product card1 = factory.create("홍길동");
		Product card2 = factory.create("이순신");
		Product card3 = factory.create("강감찬");
		card1.use();
		card2.use();
		card3.use();
	}
}

</pre>

#### Factory Method 패턴의 등장인물

Product(제품)의 역할 예제 프로그램에서 Product 클래스의 해당하며, Factory Method 패턴에서 인스턴스가 가져야할 인터페이스를 결정하는 것은 추상클래스이다. 구체적인 내용은 ConcreteProduct 역할이 결정한다.

Creator(작성자)의 역할 예제 프로그램에서 Factory 클래스에 해당하며, 실제로 생성하는 ConcreteProsucd 역할에 가지고 있는 정보가 없다. Creator 역할이 가지고 있는 정보는 Prosuct 역할과 인스턴스 생성의 메소드를 호출하면 Product가 생성된다는 것 뿐이다. 예제에서는 createProduct 메소드가 인스턴스 생성을 위한 메소드가 된다. **new를 사용해서 실제의 인스턴스를 생성하는 대신에, 인스턴스 생성을 위한 메소드를 호출해서 구체적인 클래스 이름에 의한 속박에서 상위 클래스를 자유롭게 만든다.**

ConcreteProduct(구체적인 제품)의 역할 예제프로그램에서 IDCard 클래스에 해당한다.

ConcreteCreator(구체적인 작성자)의 역할 예제 프로그램에서 IDCardFactory에 해당한다.

#### 사고 넓히기

framework를 사용해서 전혀다를 '제품'과 '공장을 만든다고 가정해보자. 예를들어 TV의 클래스 Television과 TV공장 TelevisionFactory를 만든다고 하면, 이런경우 framework패키지를 impoit한 별도의 Television 패키지를 만들게 된다. 여기에서는 framework 패키지의 내용을 수정하지 않아도 전혀 다른 제품과 공장을 만들수 있다. 즉, framwork 패키지의 내용을 수정할 필요는 없다. 생각해보면, framework 패키지 안에서는 idcard 패키지를 import하지 않는것을 알수있다. Product 클래스나 Factory 클래스 안에는 구체적인 클래스 이름이 없으며, 따라서 새로운 클래스를 동일한 framework 에서 생성할 경우에도 framework 패키지의 내용을 수정할 필요가 전혀 없다. 이것을 'framework 패키지는 idcard 패키지에 **의존하고 있지 않다**'라고 표현한다. …… 그외 패턴 사용에 대한 주의점 등은 책을 참고 하고 포스팅에는 생략한다. …… 4-1 예제 프로그램에서는 IDCard 클래스의 생성자는 public이 아닙니다. 이것은 무엇을 나타내고 있습니까? 내가 생각한 답 : Factory에 의해서만 호출이 가능하고 직접 호출을 막기위해 책 해설 : (내가 생각한 답과 동일) 4-2 예제프로그램의 IDCard 클래스에 카드의 인증번호를 붙이고, IDCardFactory 클래스가 인증번호와 소지자의 대응표를 갖도록 수정해 보십시오. 내가 생각한 답 : IDCard

<pre>public class IDCard extends Product {
	private String owner;

	private int serialNumber;

	IDCard(String owner){
		System.out.println(owner + "의 카드를 만듭니다.");
		this.owner = owner;
		this.serialNumber = 1;
	}

	IDCard(String owner, int serialNumber){
		System.out.println(owner + "의 카드를 만듭니다.");
		this.owner = owner;
		this.serialNumber = serialNumber;
	}

	public void use(){
		System.out.println(owner+ serialNumber + "의 카드를 사용합니다.");
	}
	public String getOwner(){
		return owner;
	}

	public int getSerialNumber(){
		return serialNumber;
	}

}
</pre>

IDCardFactory

<pre>public class IDCardFactory extends Factory{
	private List owners = new ArrayList();

	protected Product createProduct(String owner){ // Factory 클래스의 createProduct 구현 
		if(owners.isEmpty()){
			return new IDCard(owner);
		}
		return new IDCard(owner, owners.size()+1);
	}
	protected void registerProduct(Product product){ // Factory 클래스의 registerProduct 구 
		owners.add(((IDCard)product).getOwner());
	}
	public List getOwners(){
		return owners;
	}
}
</pre>

책 해설 : IDCard

<pre>public class IDCard extends Product {
	private String owner;

	private int serialNumber;

	IDCard(String owner, int serialNumber){
		System.out.println(owner + "의 카드를 만듭니다.");
		this.owner = owner;
		this.serialNumber = serialNumber;
	}

	public void use(){
		System.out.println(owner + "의 카드를 사용합니다.");
	}
	public String getOwner(){
		return owner;
	}

	public int getSerialNumber(){
		return serialNumber;
	}

}

</pre>

IDCardFactory (책에는 getDatabase가 Hashtable로 선언 되었는데 컴파일 에러가 발생하여 HashMap으로 수정하였다.)

<pre>public class IDCardFactory extends Factory{
	private HashMap dataabase = new HashMap();
	private int serialNumber = 100;

	protected Product createProduct(String owner){ // Factory 클래스의 createProduct 구현 
			return new IDCard(owner, serialNumber++);
	}
	protected void registerProduct(Product product){ // Factory 클래스의 registerProduct 구현 
		IDCard card = (IDCard)product;
		dataabase.put(new Integer(card.getSerialNumber()), card.getOwner());
	}

	public HashMap getDatabase(){
		return dataabase;
	}
}
</pre>

4-3 Product 클래스의 하위 클래스에서는 생성자에서 반드시 '제품의 이름'을 인수로 부여하도록 다음과 같이 Product 클래스를 정의 했습니다. 그러나 컴파일할 때 에러가 발생했습니다. 이유가 무엇입니까?

<pre>public abstract class Product {
	public abstract Product(String name);
	public abstract void use();
}</pre>

내가 생각한 답 : 생성자는 abstract 로 호출이 불가해서(?)... 책 해설 : (내가 생각한 답과 동일)