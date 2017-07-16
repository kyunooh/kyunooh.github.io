---
layout: post
title: "Abstract Factory 패턴"
date: 2016-02-11 00:00:00 +0900
categories: Know-how 
---


### 8\. Abstract Factory 패턴

'추상적인 공장' 이라는 표현에 당황하지말자. Abstract Factory패턴에서는 '추상적인 공장'뿐만 아니라 '추상적인 부품'이나 '추상적인 제품'도 등장하기 때문이다. 추상적인 공장에서는 추상적인 부품을 조합해서 추상적인 제품을 만든다. Abstract Factory **부품의 구체적인 구현에는 주목하지 않고 인터페이스(API)에 주목한다. 그리고 인터페이스(API) 만을 사용해서 부품을 조립하고 제품으로 완성**한다. [Template Method 패턴](http://jasns.cafe24.com/178 "Templete Method 패턴")이나 [Builder 패턴](http://jasns.cafe24.com/227 "Builder 패턴")에서는 하위 클래스의 레벨에서 구체적인 구현을 실행한다. Abstract Factory 패턴에서도 하위 클래스 단계에서 구체적인 구현을 실행한다. 하위 클래스 단계에서는 구체적인 공장이 등장하고 구체적인 부품을 조합해서 구체적인 제품을 만든다. 그럼 추상적인 공장의 구체적인 예제 프로그램을 살펴보자.

#### 예제 프로그램

계층구조를 가진 Link 페이지를 HTML 파일로 만드는 프로그램이다. (html 소스를 사용시 블로그 레이아웃이 깨지는 문제가 있어서 페이지 예제는 생략하고, 프로그램에 대한 자세한 내용과 클래스 다이어그램은 책을 참고하자 137~140p)  
**모든 클래스의 패키지에 유의하면서 보자**

##### 추상적인 부품 : Item 클래스

Link와 Tray의 상위 클래스이다. 이것은 Link와 Tray를 동일시하기 위한 클래스이다. caption 필드는 이 항목의 '목차'를 표시한다. makeHTML메소드는 추상 메소드로 하위 클래스에서 구현되도록 한다. 이 메소드를 호출하면 HTML의 문자열이 반환값이 된다.

<pre>package org.jellydiss.patternpractice.abstractfactory.factory;

//Link와 Tray를 통일적으로 취급하기 위한 클래스 
public abstract class Item {
	protected String caption;

	public Item(String caption) {
		this.caption = caption;
	}

	public abstract String makeHTML();
}
</pre>

##### 추상적인 부품 : Link 클래스

Link 클래스는 HTML의 하이퍼링크를 추상적으로 표현한 클래스이다. upl 필드는 링크되는 곳의 URL을 저장하기 위한 것이다. Link 클래스에서는 추상 메소드가 전혀 등장하지 않는 것처럼 보이지만, 상위 클래스(Item)의 추상 메소드(makeHTML)가 구현되어 있지 않으므로 Link 클래스도 추상 클래스이다.

<pre>package org.jellydiss.patternpractice.abstractfactory.factory;

//추상적인 부품 : HTML의 Link를 나타내는 클래스
public abstract class Link extends Item {
	protected String url;

	public Link(String caption, String url) {
		super(caption);
		this.url = url;
	}
}
</pre>

##### 추상적인 부품 : Tray 클래스

Tray 클래스는 복수의 Link나 Tray를 모아서 합친 것을 표시한 클래스이다. Link와 Tray는 add 메소드를 사용해서 모은다. 'Link나 Tray'라는 부분을 표현하기 위해 add 메소드에서는 Link와 Tray의 상위 클래스인 Item을 인수로 갖는다. Tray 클래스도 Item클래스의 추상 메소드 makeHTML을 상속하고 있지만 구현은 하지 않았다. 그러므로 Tray 클래스는 추상 클래스가 된다.

<pre>package org.jellydiss.patternpractice.abstractfactory.factory;

import java.util.ArrayList;

//추상적인 부품 : Link나 Tray를 모은 클래스 
public abstract class Tray extends Item	{
	protected ArrayList tray = new ArrayList();

	public Tray(String caption) {
		super(caption);
	}
	public void add(Item item){
		tray.add(item);
	}

}
</pre>

##### 추상적인 부품 : Page 클래스

Page 클래스는 HTML페이지 전체를 추상적으로 표현한 클래스이다. Link나 Tray가 추상적인 '부품'이라면 Page 클래스는 추상적인 '제품'에 해당합니다. title을 페이지의 제목, author는 페이지의 저자를 표현하기 위한 필드입니다. 저자 이름은 생성자에서 인수로 지정한다. 페이지에는 add 메소드를 사용해서 Item(즉, link 혹은 Tray)을 추가한다. 추가한 것이 이 페이지에서 표시된다. output 메소드 안에서는 제목을 기초로 파일명을 결정하고 makeHTML의 메소드를 사용해서 자신의 HTML의 내용을 파일에 기술하고 있다.

<pre>package org.jellydiss.patternpractice.abstractfactory.factory;

import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;
import java.util.ArrayList;

//추상적인 제품 : HTML의 Page를 나타내는 클래스 
public abstract class Page {
	protected String title;

	protected String author;

	protected ArrayList content = new ArrayList();

	public Page(String title, String author){
		this.title =title;
		this.author = author;
	}

	public void add(Item item){
		content.add(item);
	}

	public void output(){
		try {
			String filename = title + ".html";
			Writer writer = new FileWriter(filename);
			writer.write(this.makeHTML()); // 추상메소드를 호출 
			writer.close();
			System.out.println(filename + "을 작성했습니다.");
		} catch (IOException e){
			e.printStackTrace();
		}
	}

	public abstract String makeHTML(); // Templete Method 패턴을 사용한다.

}
</pre>

##### 추상적인 공장 : Factory 클래스

추상적인 부품과 추상적인 제품의 소스를 살펴보았다. 이번에는 추상적인 공장이다. getFactory 메소드는 클래스 이름을 지정해서 구체적인 공장의 인스턴스를 작성한다. 인수의 classname에는 예를 들어 "listfactory.ListFactory" 와 같이 작성할 구체적인 공장의 클래스 이름을 문자열로 지정한다. getFactory안에서는 Class 클래스의 forName 메소드를 사용해서, 그 클래스를 동적으로 읽는다. 그리고 newInstance 메소드를 이용해서, 그 클래스의 인스턴스를 한개 작성한다. 이것이 getFactory의 반환값이 된다.  
Class 클래스는 java.lang 패키지에 속하는 클래스로서 '클래스를 표현하는 클래스'이다. forName은 java.lang.Class의 클래스 메소드(static 메소드)이고, newInstance는 Java.lang.Class의 인스턴스 메소드이다.  
getFactory 메소드의 안에서는 구체적인 공장의 인스턴스를 만들지만, 반환값은 추상적인 공장이라는 점에 주의하자.  
createLink, createTray, createPage의 각 메소드는 그 추상적인 공장에서 부품이나 제품을 작성할 때 이용하는 메소드이다. 이것은 모두 추상메소드로 되어있고, 실제의 구체적인 부품이나 제품의 작성은 Factory의 하위 클래스에게 맡기고 있다. 단, 메소드의 이름과 시그니처만은 확실히 정해져 있다.

<pre>package org.jellydiss.patternpractice.abstractfactory.factory;

// 추상적인 공장을 나타내는 클래(Link, Tray, Page를 만든다)
public abstract class Factory {

	public static Factory getFactory(String classname){
		Factory factory = null;
		try {
			factory = (Factory)Class.forName(classname).newInstance();
		} catch(ClassNotFoundException e) {
			System.out.println("클래스 " + classname + " 이 발견되지 않습니다.");
		} catch(Exception e ){
			e.printStackTrace();
		}

		return factory;
	}

	public abstract Link createLink(String caption, String url);
	public abstract Tray createTray(String caption);
	public abstract Page createPage(String title, String author);
}
</pre>

##### 공장을 사용해서 부품을 조합하고 제품 만들기 : Main 클래스

추상적인 부품, 제품, 공장의 소스코드를 살펴보았으므로, 이번에는 Main 클래스를 살펴보자. 여기에는 추상적인 공장을 사용해서 추상적인 부품을 제조하고, 추상적인 제품을 조립하고 있다. import되어 있는 것이 factory 패키지뿐인 점에서 알 수 있듯이 이 클래스에서는 구체적인 부품, 제품, 공장을 전혀 이용하지 않는다.  
구체적인 공장의 클래스 이름은 커맨드 라인에서 지정한다. 예를들어 listfactory 패키지의 ListFactory 클래스를 사용하려면 커맨드 라인에서

<pre>java Main org.jellydiss.patternpractice.abstractfactory.tablefactory.TableFactory 
</pre>

라고 입력해야한다. 이 인수(args[0])를 기초로 getFactory에서 공장을 만들고 변수 factory에 대입한다. 이후 factory를 사용해서 Link와 Tray를 만들고, Tray 안에 Link나 Tray을 넣고, 마지막으로 Page를 만들어 output을 실행한다. (여담으로 지금은 야후 코리아에 접속이 불가하다. ㅇㅇ)

<pre>package org.jellydiss.patternpractice.abstractfactory;

import org.jellydiss.patternpractice.abstractfactory.factory.*;

public class Main {
	public static void main(String[] args) {
		if(args.length != 1){
			System.out.println("Usage: java Main class.name.of.ConcreteFactory");
			System.out.println("Example 1: java Main listfactory.ListFactory");
			System.out.println("Example 2: java Main tablefactory.TableFactory");
			System.exit(0);
		}
		Factory factory = Factory.getFactory(args[0]);

		Link joins = factory.createLink("중앙일보", "http://www.joins.com/");
		Link chosun = factory.createLink("조선일보", "http://www.chosun.com/");

		Link us_yahoo = factory.createLink("Yahoo!", "http://www.yahoo.com/");
		Link kr_yahoo = factory.createLink("Yahoo!korea", "http://www.yahoo.co.kr/");
		Link excite = factory.createLink("Excite", "http://www.excite.com/");
		Link google = factory.createLink("Google", "http://www.google.com/");

		Tray traynews = factory.createTray("신문");
		traynews.add(joins);
		traynews.add(chosun);

		Tray trayyahoo = factory.createTray("Yahoo!");
		trayyahoo.add(us_yahoo);
		trayyahoo.add(kr_yahoo);

		Tray traysearch = factory.createTray("검색엔진");
		traysearch.add(trayyahoo);
		traysearch.add(excite);
		traysearch.add(google);

		Page page = factory.createPage("LinkPage", "젤리디스");
		page.add(traynews);
		page.add(traysearch);
		page.output();
	}
}
</pre>

##### 구체적인 공장 : ListFactory 클래스

지금까지는 계속 추상적인 측면의 코드를 살펴보았는데, 이번에는 생각을 바꿔서 구체적인 측면의 살펴보자. 우선 listfactory 패키지의 공장, ListFactory 클래스이다 ListFactory 클래스에서는 Factory 클래스의 추상 메소드 createLink, createTray, createPage를 구현하고 있다. 여기에서는 단순하게 listLink, ListTray, ListPage를 new하고 있다.(프로그램에 따라서는 여기에서 new 하는 것이 아니고, Prototype 패턴을 사용해서 clone을 실행하는 경우도 있다.

<pre>package org.jellydiss.patternpractice.abstractfactory.listfactory;
import org.jellydiss.patternpractice.abstractfactory.factory.*;

//구체적인 공장을 나타내는 클래스(ListLink, ListTray, ListPage를 만든다.)
public class ListFactory extends Factory {
	public Link createLink(String caption, String url){
		return new ListLink(caption, url);
	}

	public Tray createTray(String caption){
		return new ListTray(caption);
	}

	public Page createPage(String title, String author){
		return new ListPage(title, author);
	}
}
</pre>

##### 구체적인 부품 : ListLink 클래스

ListLink 클래스는 Link클래스의 하위 클래스이다. 구현해야할 메소드는, 상위클래스에서 추상 메소드였던 makeHTML 이다. ListLink에서는 <li> 태그와 <a> 태그를 사용해서 HTML의 일부분을 작성하고 있다. 마치 한쌍으로 이루어진 볼트와 너트가 잘 맞는 것처럼, 이 HTML의 일부분도 다른 ListTray나 ListPage와 잘 조합되도록 기술되어 있다.

<pre>package org.jellydiss.patternpractice.abstractfactory.listfactory;

import org.jellydiss.patternpractice.abstractfactory.factory.Link;

//구체적인 부품 : HTML의 Link를 나타내는 클래스 
public class ListLink extends Link{
	public ListLink(String caption, String url){
		super(caption, url);
	}
	public String makeHTML(){
		return " </pre>

[" + caption + "](\)

\n"; } }

##### 구체적인 부품 : ListTray 클래스

ListTray 클래스는 Tray 클래스의 하위 클래스이다. 여기에 makeHTML 이 어떻게 구현되고 있는지 주목하자. tray필드 안에는 HTML 로 출력해야할 Item들이 모여있다. 그것들을 HTML의 태그로 표현하는 것이 이 makeHTML 메소드의 사명이다.  
처음에 <li>를 사용해서 목차(caption)를 출력하고, 다음에 <ul>과 <li>를 사용해서 각각의 Item들을 출력합니다. 출력결과는 일단 StringBuffer에 모아지고 마지막에 toString에서 String으로 변환한다.

그러면 각각의 Item들은 어떻게 HTML화하는 것일까요? 물론 각각  Item의 makeHTML을 호출해야 한다. 변수 item의 내용이 실제로 ListLink의 인스턴스인지  ListTray의 인스턴스인지를 신경 쓸 필요는 없습니다. 단지 간단하게

<pre>item.makeHTML()
</pre>

이라는 식으로 사용하면 된다. 여기에서 **변수 item의 내용이 실제로 무엇인지를 조사해서 switch문이나 if문을 사용하는 프로그램을 작성해서는 안된다. 그것은 매우 비객체지향적인 프로그램이 되버린다.** 변수 item은 Item형이고 item클래스에서는 makeHTML이라는 메소드가 선언되고 있다. 그리고 ListLink나 ListTray는 모두 Item 클래스의 하위 클래스다. 그러므로 안심하고 makeHTML 메소드를 호출하면 된다. 남은 것은 item이 makeHTML 메소드를 조화롭게 처리해 주는 것이다. 어떻게 처리하는지는 item이라는 하나의 인스턴스(오브젝트)가 알고 있다. 이것이 객체지향의 이점이다.  
여기에서 사용되고 있는 java.util.Iterator라는 클래스는 Iterator 패턴에서 배운 것과 기능적으로는 같지만, 여기에서는 Java의 클래스 라이브러리로 제공되고 있는 것입니다. java.util.ArrayList 클래스에서 java.util.Iterator 클래스를 만들기 위해서는 iterator라는 메소드가 사용된다.

<pre>package org.jellydiss.patternpractice.abstractfactory.listfactory;

import java.util.Iterator;

import org.jellydiss.patternpractice.abstractfactory.factory.Item;
import org.jellydiss.patternpractice.abstractfactory.factory.Tray;

//구체적인 부품 : Link나 Tray를 모은 클래스
public class ListTray extends Tray {

	public ListTray(String caption) {
		super(caption);
	}

	public String makeHTML() {
		StringBuffer buffer = new StringBuffer();
		buffer.append("</pre>

\n"); buffer.append(caption + "\n"); buffer.append("

\n"); Iterator it = tray.iterator(); while (it.hasNext()) { Item item = (Item) it.next(); buffer.append(item.makeHTML()); } buffer.append("

\n"); buffer.append("\n"); return buffer.toString(); } }

##### 구체적인 제품 : ListPage 클래스

ListPage는 Page 클래스의 하위 클래스이다. makeHTML 메소드는 이미 이했했을것이다. ListPage는 필드의 내용을 사용해 페이지를 구성하고 있다. 저자 이름은(author)은

<address>태그를 사용해 표현하고 있다.

<pre>package org.jellydiss.patternpractice.abstractfactory.listfactory;

import java.util.Iterator;

import org.jellydiss.patternpractice.abstractfactory.factory.Item;
import org.jellydiss.patternpractice.abstractfactory.factory.Page;

//구체적인 제품 : HTML의 Page를 나타내는 클래스
public class ListPage extends Page{
	public ListPage(String title, String author){
		super(title, author);
	}

	public String makeHTML() {
		StringBuffer buffer = new StringBuffer();
		buffer.append("</pre>

</address>

\n"); return buffer.toString(); } }

#### 예제 프로그램에 별도의 구체적인 공장 추가하기

각 클래스의 상속 관계등이 크게 다르지 않고 목록에서 테이블로 변경한 것임으로 소스는 책을 참조토록 하고 포스팅은 생략한다. 아래는 메인클래스 실행 후 만들어진 html 파일의 화면이다.

#### Abstract Factory 패턴의 등장인물

AbstractProduct(추상적인 제품)의 역할  
AbstractProduct는 AbstractProduct 역할에 의해 만들어지는 추상적인 부품이나 제품의 인터페이스(API)를 결정한다. 예제 프로그램에서는 Link 클래스, Tray 클래스, Page 클래스가 이 역할을 한다.

AbstractFactory(추상적인 공장)의 역할  
AbstractFactory는 AbstractProduct 역할의 인스턴스를 만들어 내기위한 인터페이스(API)를 결정한다. 예제 프로그램에서는 Factory 클래스가 이 역할을 한다.

Client(의뢰자)의 역할  
Client는 AbstractFactory 역할과 AbstractProduct 역할의 인터페이스(API)만을 사용해서 주어진 역할을 실행한다. Client는 구체적인 부품이나 제품, 공장에 대해서는 모른다. 예제 프로그램에서 Main 클래스가 이 역할을 한다.

ConcreteProduct(구체적인 제품)의 역할  
ConcreteProduct는 AbstractProduct 역할의 인터페이스(API)를 구현한다. 예제 프로그램에서는 패키지마다 다음과 같은 클래스가 이 역할은 한다.  
listfactory 패키지 > ListLink, ListTray, ListPage 클래스  
tablefactory 패키지 > TableLink, TableTray, TablePage 클래스

ConcreteFactory(구체적인 공장)의 역할  
ConcreteFactory는 AbstractFactory 역할의 인터페이스(API)를 구현한다. 예제프로그램에서는 패키지마다 다음과 같은 클래스가 이 역할을 한다.  
listfactory > ListFactory 클래스  
tablefactory > TableFactory 클래스

#### 사고 넓히기

##### 구체적인 공장을 새로 추가하는 것은 간단하다

Abstract Factory 패턴에 구체적인 공장을 새로 추가하는 것은 간단하다. '간단'하다는 것은 어떤 클래스를 만들고, 어떤 메소드를 구현하면 좋은지가 확실하다는 의미다. 예를 들어, 예제 프로그램에 다시 새로운 구체적인 공장을 추가한다고 가정한다. 해야할 일은 Factory, Link, Tray, Page의 하위 클래스를 만들고 각각의 추상 메소드를 구현하는 일이다. 즉, factory 패키지의 클래스가 가지고 있는 추상적인 부분을 구체화 해가는 일이다. 이때 아무리 구체적인 공장을 추가해도 추상적인 공장(또 구체적인 공장의 버그를 수정해도)추상적인 공장이나 Main 부분을 수정할 필요는 전혀 없다.

##### 부품을 새로 추가하는 것은 곤란하다.

Abstract Factory패턴에 새로 부품을 추가하는 일에 대해 생각해보자. 예를 들어 factory패키지에 화상을 표시하는 Picture 라는 부품을 추가했다고 가정하자. 그러면 이미 존재하는 구체적인 공장 전부에 Picture에 대응하는 수정을 해야한다. 예를 들어 listfactory 패키지 라면 'listFactory 클래스에 createPicture 메소드를 추가', '새롭게 ListPicture 클래스를 작성'같은 수정을 해야한다. 이미 만들어진 구체적인 공장이 많으면 많을수록 고된 작업이 된다.  
……

책에 서술된 인스턴스를 만드는 다양한 방법은 생략한다.

……  

8-1 Tray 클래스에서는 tray 필드가 protected로 정의 되고 하위 클래스에서 참조할 수 있도록 되어있다. 이것을 private로 했을 경우의 장점과 단점을 각각 지적해 주십시오.  
내가 생각한 답 : 캡슐화에 대한 이점을 얻을수 있으며 하위 클래스에서 tray 필드에 접근하기 위한 메소드(getTray 등)이 필요하다.

책 해설 : private로 한 경우의 장점은 Tray의 하위 클래스가 tray필드의 구현에 의존한 코드가 아니라는 점이다. private 로 한 경우의 단점은 적절한 액세스용 메소드를 새롭게 작성해야 한다는 점이다. 일반적으로 protected 필드를 사용하기 보다는 필드를 private로 해서 엑세스용 메소드를 만드는 편이 안전한 프로그램이 된다.

8-2 예제 프로그램의 Factory 클래스에 'Yahoo! (http://www.yahoo.com/)의 링크만 구현되는 페이지를 작성하는 구상 메소드'

<pre>public Page createYahooPage()
</pre>

를 정의하십시오(페이지의 저자와 제목은 모두 "Yahoo!"로 합니다). 이때 구체적인 공장이나 구체적인 부품은 어떻게 수정해야 할까요?

내가 생각한 답 : 여러가지 답안이 나올 수 있을듯 보인다. 난 아래와 같이 메소드를 추가하여, Templete Method 패턴을 이용하여 처리했다.

<pre>	public Page createYahooPage(){
		return createPage("Yahoo!", "Yahoo!");
	}
</pre>

책 해설 : Factory와 Main 클래스의 변경이 필요하다.  
Factory (아래의 메소드를 추가한다.)

<pre>public Page createYahooPage(){
		Link link = createLink("Yahoo!", "http://www.yahoo.com/");
		Page page = createPage("Yahoo!", "Yahoo!");
		page.add(link);
		return page;
	}
</pre>

Main

<pre>public class Main {
	public static void main(String[] args) {
		if(args.length != 1){
			System.out.println("Usage: java Main class.name.of.ConcreteFactory");
			System.out.println("Example 1: java Main listfactory.ListFactory");
			System.out.println("Example 2: java Main tablefactory.TableFactory");
			System.exit(0);
		}
		Factory factory = Factory.getFactory(args[0]);
		Page page = factory.createYahooPage();
		page.output();
	}
}
</pre>

8-3 ListLink 클래스의 생성자를 보면 다음과 같이 되어있다.

<pre>public ListLink(String caption, String url) {
    super(caption, url);
}
</pre>

즉, 하고 있는 일은 상위 클래스의 생성자를 호출하고 있을 뿐이다. 특별한 처리가 필요 없다면, 왜 일부러 ListLink의 생성자를 정의하고 있는 것일까요?

내가 생각한 답 : 생성자를 따로 만들지 않을경우 기본 생성자(Default Constructor)가 생기게 되고 상위 클래스의 필드를 초기화 해주고 있다.

책 해설 : Java에서는 생성자가 상속되지 않기 때문이다. 상위 클래스에서 Link(String caption, Stringurl)라는 생성자가 있더라도 ListLink 쪽에서 ListLink(String caption, String url)라는 생성자를 정의해두면

<pre>new ListLink("Yahoo!", "http://www.yahoo.com/")
</pre>

와 같이 호출할 수는 없습니다. 컴파일시에 에러가 발생합니다.

8-4 Page 클래스는 Tray 클래스와 비슷한 역할을 하고 있습니다. 그런데 왜 Page 클래스를 Tray 클래스의 하위 클래스로 하지 않았을까요?

내가 생각한 답 : Page 클래스는 부품을 사용해 제품을 만드는 클래스인데 현재 Page가 Tray를 상속할시 제품이 부품을 상속하는 이상한 구조가 되어버린다....

책 해설 : Page는 Tray에 add할수 없기(HTML의 문법상 부적절) 때문입니다. 만약 Page 클래스를 Tray 클래스의 하위 클래스로 하면, Page도 Item의 하위 클래스로 되어 add의 대상이 된다. 그 대신에 Page 클래스에서 makeHTML을 선언해야한다. makeHTML 메소드를 포함하는 Java의 인터페이스 HTMLable 다음과 같이 작성하고, Item 클래스와 Page 클래스는 HTML을 implements하도록 변경하면 보다 깨끗하게 정리할 수 있다.

<pre>public interface HTMLable {
    public abstract String makeHTML();
}</pre>

\n"); buffer.append("

\n"); buffer.append("

# " + title + "

\n"); buffer.append("

\n"); Iterator it = content.iterator(); while (it.hasNext()) { Item item = (Item)it.next(); buffer.append(item.makeHTML()); } buffer.append("

\n"); buffer.append("

* * *

<address>" + author + "</address>

"); buffer.append("