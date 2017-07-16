---
layout: post
title: "Decorator 패턴"
date: 2016-02-12 00:00:00 +0900
categories: Know-how 
---


## 12.Decorator 패턴

스펀지 케이크에 크림을 바르면, 크림 케이크가 됩니다. 이 위에 딸기를 얹으면 딸기 케이크, 초콜릿을 바르면 초코케이크, 생일 초를 꽂으면 생일 케이크가 됩니다. 위에 나열한 케이크 모두 처음에는 다 같은 스펀지 케이크 입니다. 그러나 다양한 장식(?)을 하면 다양한 케이크가 만들어집니다. 이처럼 오브젝트(객체)도 케이크와 비슷한 경우가 있습니다. 우선 스펀지 케이크와 같은 중심이 되는 오브젝트가 있습니다. 이 오브젝트에 장식이 되는 기능을 하나씩 추가하면 좀더 목적에 맞는 오브젝트가 완성됩니다. 이와 같이 오브젝트에 장식을 해 나가는 디자인 패턴을 'Decorator 패턴'이라고 합니다.

### 예제 프로그램

문자열 주변에 장식을 만들어서 표시하는 예제 프로그램을 만듭니다. 장식이란 -, +, | 과 같은 특수문자로 표현하는 것을 말합니다. (클래스 다이어그램과 더 자세한 사항등은 [책](http://jellyms.kr/140 "Iterator 패턴")을 참조하자 213p)

#### Display 클래스

Display 클래스는 복수 행으로 구성되는 문자열을 표시하기 위한 추상 클래스다. getColumns와 getRows는 각각 가로의 문자수와 세로의 문자수를 얻기 위한 메소드다. 이것은 추상 메소드로서 하위 클래스에서 구현되어야 한다.(subclass responsibility) getRowtext는 지정한 행의 문자열을 얻는 메소드다. 이것도 추상 메소드로서 하위 클래스에서 구현되어야 한다. show는 모든 행을 표시하는 메소드다. 이 안에서는 getRows 메소드로 행수를 취득하며 getRowText 메소드로 표시해야 할 문자열을 취득하고 for 루프를 사용해서 모든 형을 표시한다. show는 getRows와 getRowText라는 추상 메소드를 사용한 [Template Method](http://jellyms.kr/178 "Templete Method 패턴")패턴으로 되어있다.

<pre>public abstract class Display {
	public abstract int getColumns();
	public abstract int getRows();
	public abstract String getRowText(int row);
	public final void show() {
		for (int i = 0; i < getRows(); i++) {
			System.out.println(getRowText(i));
		}
	}
}</pre>

#### StringDisplay 클래스

Display 클래스만 봐서는 이해하기 힘들기 때문에 하위 클래스이 StringDisplay 클래스를 살펴보자. StringDisplay 클래스는 1행의 문자열을 표시하는 클래스다. StringDisplay 클래스는 Display 클래스의 하위 클래스이기 때문에, Display 클래스에서 선언된 추상메소드를 구현할 책임이 있다. string 필드는 표시할 문자열을 저장한다. StringDisplay 클래스에서 표시하는 것은 string 필드의 내용 1행 뿐이기 때문에 getColumns는 string.getBytes().length의 값을 반환하고 getRows는 1을 반환한다. 또한 getRowText는 0번째의 값을 취할 때만 string 필드를 반환ㄱ한다. 서두의 케이크 이야기와 비교해보면 이 StringDisplay 클래스는 생일 케이크의 중심에 있는 스펀지 케이크가 된다.

<pre>public class StringDisplay extends Display{
	private String string;
	public StringDisplay(String string) {
		this.string = string;
	}

	public int getColumns() {
		return string.getBytes().length;
	}

	public int getRows() {
		return 1;
	}

	public String getRowText(int row) {
		if(row == 0){
			return string;
		} else {
			return null;
		}
	}
}
</pre>

#### Border 클래스

Border 클래스는 '장식'을 나타내는 추상 클래스다. 장식을 표시하는 클래스지만 문자열 표시를 실행하는 Display 클래스의 하위 클래스로 정의된다. 여기에서는 단순화하기 위해 메모리 상의 1바이트 문자가 화면 상의 1칼럼에 해당하는 것을 전제로 코딩한다. 즉, 상속에 의해 **장식은 내용물과 동일한 메소드**을 가진다. 구체적으로 말하면 Border 클래스는 getColumns, getRows, getRowText, show의 각 메소드를 상속하고 있다는 의미다. 장식(Border)이 내용물(Display)과 같은 메소드를 가진다는 것은 인터페이스적으로 장식과 내용물을 동일시할 수 있다는 의미다. 장식의 Border 클래스는 Display 형의 display 필드를 가지고 있다. 이것이 장식으로 싸여 있는 '내용물'이다. 그렇지만 display의 내용물이 StringDisplay의 인스턴스라고 단정지을 수 없다. 어쨌든 Border도 Display의 하위 클래스이기 때문에 display 필드의 내용물은 또 다른 장식(Border 클래스의 하위 클래스의 인스턴스)일지도 모른다. 그리고 그 장식도 display 필드를 갖는다. 자, 이제 'Decorator 패턴'의 줄기가 보이기 시작하는가?

<pre>public abstract class Border {
	protected Display display;
	protected Border(Display display){
		this.display = display;
	}
}
</pre>

#### SideBorder 클래스

SideBoredr 클래스는 구체적인 장식의 일종으로 Border 클래스의 하위 클래스다. SideBoredr 클래스는 문자열의 좌우에 정해진 문자(borderChar)로 장식을한다. 예를 들어, borderChar 필드의 값이 '|'라고 하면

<pre>| 내용물 |</pre>

와 같이 '내용물'의 좌우에 지정된 문자가 붙어 show로 표시된다. borderChar 필드는 생성자에서 지정한다. SideBorder는 추상 클래스가 아니다. 왜냐하면 상위 클래스에서 선언되어 있던 추상 메소드가 모두 여기에서 구현되고 있기 때문이다. getColumns는 표시 문자의 가로 문자수를 얻는 메소드다. 문자수를 계산하는 것은 간단하다. 이 장식이 감싸고 있는 '내용물'의 문자수에 좌우의 장식 문자수를 더하면 된다. 내용물의 문자수는 display.getColumns()라는 식으로 얻을 수 있다. display 필드는 Border 클래스에서 protected였기 때문에 하위 클래스에서 직접 이용할 수 있다. 좌우의 장식 문자수를 더해서,

<pre>1 + display.getColumns() + 1</pre>

이 반환 된다. display.getColumns() + 2 라고 해도 상관 없지만, 여기에서는 좌우에 하나씩 더한 것을 확실히 알 수 있도록 기술했다. getColumns 메소드의 작성법을 이해하면 getRows 메소드도 쉽게 이해될 것이다. SideBorder 클래스는 상하 방향과는 무관하기 때문에, 식 display.getRows()의 값이 그대로 getRows 메소드의 값이 된다. getRowText 메소드는 인수에서 지정한 행의 문자열을 얻는다. display.getRowText(row)라는 내용물의 문자열 양측에 borderChar라는 장식 문자를 붙인

<pre>borderChar + display.getRowText(row + borderChar</pre>

가 getRowText의 반환값이 된다.(이것이 진정한 SideBorder의 '장식'이다.)

#### FullBoredr 클래스

SideBorder와 마찬가지로 Border의 하위 클래스이다. 동작이 다를뿐 기능은 같기에 상세 설명을 생략한다.

<pre>public class FullBorder extends Border{
	public FullBorder(Display display) {
		super(display);
	}

	public int getColumns() {
		return 1 + display.getColumns() + 1;
	}

	public int getRows() {
		return 1 + display.getRows() + 1;
	}

	public String getRowText(int row) {
		if (row == 0) {
			return "+" + makeLine('-', display.getColumns()) + "+";
		} else if (row == display.getRows() + 1) {
			return "+" + makeLine('-', display.getColumns()) + "+";
		} else {
			return "|" + display.getRowText(row - 1) + "|";
		}
	}

	private String makeLine(char ch, int count) {
		StringBuffer buf = new StringBuffer();
		for (int i =0; i < count; i++) {
			buf.append(ch);
		}
		return buf.toString();
	}

}
</pre>

#### Main

Main 클래스는 동작 테스트용 클래스다. 여기에서 사용되고 있는 인스턴스 b1~b4의 역할은 각각 다음과 같다. b1: "Hello, world."를 장식 없이 표시 / b2: b1에 대해서 '#'으로 좌우에 장식 / b3:b2에 대해서 전체 장식한 것 / b4:"안녕하세요"에 여러 겹으로 장식

<pre>public class Main {
	public static void main(String[] args) {
		Display b1 = new StringDisplay("Hello, world.");
		Display b2 = new SideBorder(b1, '#');
		Display b3 = new FullBorder(b2);
		b1.show();
		b2.show();
		b3.show();
		Display b4 = 
				new SideBorder(
						new FullBorder(
								new SideBorder(
										new FullBorder(
												new StringDisplay("안녕하세요.")
												),'*'
										)
								)
						, '/');
		b4.show();
	}
}</pre>

[caption id="attachment_660" align="alignnone" width="231"][![Decorator Main 실행화면](http://jellyms.kr/wp-content/uploads/2014/10/DecoratorMain.png)](http://jellyms.kr/wp-content/uploads/2014/10/DecoratorMain.png) Decorator Main 실행화면[/caption]

### Decorator 패턴의 등장인물

Decorator 패턴의 등장인물은 다음과 같다.

Component의 역할 예제에서는 Display 클래스에 해당되며, 기능을 추가할 때 핵심이 되는 역할이다. 서두의 케이크에 비교하면 장식하기 전의 스펀지 케이크에 해당한다. Component 역할은 스펀지 케이크의 인터페이스(API)만을 결정한다.

ConcreteComponent의 역할 예제에서는 StringDisplay 클래스에 해당되며, Component 역할의 인스턴스(API)를 구현하고 있는 구체적인 스펀지 케이크다.

Decorator(장식자)의 역할 예제에서는 Border 클래스에 해당되며, Componen 역할과 동일한 인터페이스(API)를 가지며, 또한 이 Decorator 역할이 장식할 대상이 되는 Component 역할도 가지고 있다. 이 역할은 자신이 장식하고 있는 대상을 알고 있다.

ConcreteDecorator(구체적인 장식자)의 역할 예제에서는 SideBorder와 FullBorder 클래스에 해당되며, 구체적인 Decorator 역할입니다.

### 사고 넓히기

#### 투과적인 인터페이스(API)

Decorator 패턴에서는 장식과 내용물을 동일시하고 있다. 구체적으로 말하면 예제에서는 장식을 나타내는 Border 클래스, 내용물을 나타내는 Display 클래스의 하위 클래스로 되어 있는 곳에서 동일하게 표현되고 있다. 즉 Border, 클래스(및 그 하위 클래스들)는 내용물을 나타내는 Display 클래스와 동일한 인터페이스(API)를 가진다. 장식을 사용해서 내용물을 감싸도 인터페이스(API)는 전혀 감출수 없다. getColumns, getRows, getrowText, show라는 메소드는 감추어지는 일없이 다른 클래스에서 볼 수 있다. 이것을 인터페이스(API)가 '투과적'이라고 한다. 예제에서는 인스턴스 b4와 같이 장식을 많이 사용해서 포함해도 인터페이스(API)는 전혀 바뀌지 않는다. 인터페이스(API)가 투과적이기 때문에 Decorator 패턴에서는 Composite 패턴과 닮은 재귀적인 구조가 등장한다. 즉, 장식이 둘러싸고 있는 '내용물'이 실제로는 다른 '장식'이 되는 구조다. 즉 장식이 둘러싸고 있는 '내용물'이 실제로는 다른 '장식'이 되는 구조다. "양파의 껍질을 벗겨 알맹이가 나왔다고 생각했더니 그것 또한 껍질이었다" 같은 것입니다. Decorator 패턴과 [Composite 패턴](http://jellyms.kr/596 "Composite 패턴")은 재귀적인 구조를 취하는 점에서는 유사하지만 목적은 다릅니다. Decorator 패턴은 장식을 중복해서 기능을 추가해가는 것에 주안점을 두기 때문이다.

#### 내용물을 바꾸지 않고 기능을 추가할 수 있다.

Decorator 패턴에서는 장식도 내용물도 공통의 인터페이스(API)를 가진다. 인터페이스(API)는 공통이지만, 장식하면 장식할수록 기능이 추가된다. Display를 SideBorder로 감싸면 좌우에 새로운 장식문자를 표시할 수 있다. 그리고 FullBorder 로 감싸면 가장자리 전체에 장식을 붙일 수 있다. 이때, 장식되는 상대(내용물)는 수정할 필요가 없다. 즉, 내용물을 변경하지 않고 기능을 추가할 수 있다. Decorator 패턴에서는 위임을 사용하고 있다. '장식'에 대한 요구(메소드의 호출)는 그 '내용물'에 떠넘겨(위임)집니다. 예제에서는 SideBorder의 getColumns 메소드 안에서 display.getColumns()를 호출하며, getRows 메소드에서는 display.getRow()를 호출한다.

#### 동적인 기능을 추가 할 수 있다

Decorator 패턴에서 사용되는 위임은 클래스 사이를 느슨하게 결합한다. 따라서 framework의 소스를 변경하지 않고 오브젝트(객체)의 관계를 변경한 새로운 오브젝트(객체)를 만들 수 있다.

#### 단순한 장식이라도 다양한 기능을 추가할 수 있다

Decorator 패턴을 사용하면 다양한 기능을 추가할 수 있다. 구체적인 장식(ConcreteDecorator 역할)을 많이 준비해 두면, 그것들을 자유롭게 조합해서 새로운 오브젝트를 만들 수 있기 때문이다. 이때, 각각의 장식은 단순해도 상관 없다. 이것은 바닐라, 초콜릿, 딸기, 키위 등의 장식을 자유롭게 선택할 수 있는 아이스크림과 같다. 손님이 주문할지, 안 할지 알수 없는 여러가지 아이스크림을 처음부터 전부 준비해야 한다면 주문을 받는 입장세서 큰 부담이 될 것이다. 가게에서는 다양한 종류의 장식을 준비해 두었다가 순님의  주문에 때라 장식을 해서 손님이 주문한 아이스크림을 만든다. Decorator 패턴은 이와 같은 다양한 요구에 적합하다. (그외 주의사항은 책을 참고하자 222p~225p) 12-1 이 장의 예제 프로그램에 추가하는 형태로 상하에 장식문자가 붙는 UpDownBorder클래스를 만드시오. **Main 클래스**

<pre>public class Main {
	public static void main(String[] args) {
		Display b1 = new StringDisplay("Hello, world.");
		Display b2 = new UpDownBorder(b1, '-');	
		Display b3 = new SideBorder(b2,'*');
		b1.show();
		b2.show();
		b3.show();
		Display b4 = 
						new FullBorder(
								new UpDownBorder(
									new SideBorder(
											new UpDownBorder(
													new SideBorder(
															new StringDisplay("안녕하세요."),'*'
													),'='
											),'|'
										),'/'
									)
								);
		b4.show();
	}
}
</pre>

내가 생각한 답 : **UpDownBorder 클래스**

<pre>public class UpDownBorder extends Border {
	char borderChar;

	public UpDownBorder(Display display, char borderChar) {
		super(display);
		this.borderChar = borderChar;
	}

	public int getColumns() {
		return display.getColumns() ;
	}

	public int getRows() {
		return 1 + display.getRows() + 1;
	}

	public String getRowText(int row) {
		if (row == 0) {
			return   makeLine(borderChar, display.getColumns()) ;
		} else if (row == display.getRows() + 1) {
			return  makeLine(borderChar, display.getColumns())  ;
		} else {
			return  display.getRowText(row - 1)  ;
		}
	}

	private String makeLine(char ch, int count) {
		StringBuffer buf = new StringBuffer();
		for (int i =0; i < count; i++) {
			buf.append(ch);
		}
		return buf.toString();
	}

}
</pre>

12-2 ConcreteComponent 역할로 복수의 문자열을 표시하는 MultiStringDisplay 클래스를 만드시오. MultiStringDisplay는 다음과 같다고 한다. 내가 생각한 답 : 리스트를 이용해서 show와 add를 노가다(?)로 구현하면 되지 않을까? 책 해설 : **MultiStringDisplay**

<pre>public class MultiStringDisplay extends Display {
    private ArrayList body = new ArrayList();
    private int columns = 0;
    public void add(String msg) {
        body.add(msg);
        updateColumn(msg);
    }
    public int getColumns() {
        return columns;
    }
    public int getRows() {
        return body.size();
    }
    public String getRowText(int row) {
        return (String)body.get(row);
    }
    private void updateColumn(String msg) {
        if (msg.getBytes().length > columns) {
            columns = msg.getBytes().length;
        }
        for (int row = 0; row < body.size(); row++) {
            int fills = columns - ((String)body.get(row)).getBytes().length;
            if (fills > 0) {
                body.set(row, body.get(row) + spaces(fills));
            }
        }
    }
    private String spaces(int count) {
        StringBuffer buf = new StringBuffer();
        for (int i = 0; i < count; i++) {
            buf.append(' ');
        }
        return buf.toString();
    }
}
</pre>