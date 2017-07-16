---
layout: post
title: "Composite 패턴"
date: 2016-02-12 00:00:00 +0900
categories: Know-how 
---


## 11.Composite 패턴

컴퓨터의 파일 시스템에는 '디렉터리'가 있다. 디렉터리 안에는 파일이 있거나 다른 디렉터리(하위 디렉터리)가 있기도 한다. 또 그 하위 디렉터리 안에는 다른 파일 혹은 하위 디렉터리가 있기도 하다. 이런 '상자안의 상자'로 된 구조, 즉 재귀 적인 구조를 만들어 낸다.

디렉터리와 파일은 서로 다르지만 모두 '디렉터리 안에 넣을 수 있는 것'이다. 디렉터리와 파일을 합해서 '디렉터리 엔트리' 라고 부르기도 한다. 디렉터리 엔트리라는 이름으로 디렉터리와 파일을 같은 종류로 간주하고 있다.

예를 들어, 어떤 디렉터리 안에 무엇이 있는지를 차례대로 조사한다고 가정하자. 이때, 차례로 조사하는 것은 하위 디렉터리일지도 모르고 파일일지도 모른다. 한마디로 말해 디렉터리 엔트리'를 차례대로 조사하는 것이다.

디렉터리와 파일을 모아서 디렉터리 엔트리로 취급하듯이 그릇과 내용물을 같은 종류로 취급하면 편리한 경우가 있다. 그릇 안에는 내용물을 넣을 수도 있고 더욱 작은 그릇을 넣을 수도 있다. 그리고 그릇 안에 그릇 안에 그릇 안에.... 이런식으로 '상자안의 상자'처럼 된 구조, 즉 재귀적인 구조를 만들 수 있다. 이장에서 배울 **'Composite 패턴' 은 그릇과 내용물을 동일시해서 재구적인 구조를 만들기 위한 디자인 패턴이다. **composite는 '혼합물', '복합물'이라는 의미이다.

### 예제 프로그램

Composite 패턴의 예제 프로그램으로 파일과 디렉터리를 도식적으로 표현한 프로그램을 작성해보자. 파일을 나타내는 클래스가 File 클래스이고 디렉터리를 나타내는 클래스가 Directory 클래스이며 두 가지 클래스를 하나로 모은 형태의 상위 클래스가 Entry 클래스다. Entry 클래스는 디렉터리 엔트리를 나타내는 클래스로서 File과 Directory를 동일시하는 클래스다. (클래스 다이어그램은 책을 참조하자 197p)

#### Entry 클래스

Entry 클래스는 추상 클래스로서 디렉터리 엔트리를 표현하며 하위 클래스인 File 클래스와 Directory 클래스를 만든다.

디렉터리 엔트리는 이름을 가지고 있다. 이름을 얻기 위한 메소드로서 getName을 준비하며 하위 클래스에서 구현을 한다. 또한 디렉터리 엔트리는 크기를 가지고 있다. 크기를 얻기 위한 getSize 메소드를 준비하며 이것도 하위 클래스에서 구현한다.

디렉터리 안에 파일이나 디렉터리를 넣는 메소드는 add다. 그러나 add를 구현하는 것은 디렉터리를 나타내는 하위 클래스인 Directory 클래스의 계층이 된다. Entry 클래스의 계층에서는 예외를 제공해서 에러가 발생하도록 한다. 여기에서는 예외를 제공하고 있지만 add의 구현 방법은 다양하게 변형시킬 수 있다. 자세한 사항은 'add 메소드의 구현 방법'에서 설명한다.

printList 메소드는 '종류'를 표시하는 메소드다. 인수 없는 printList()와 인수 있는 printList(String) 두 가지 메소드가 있다. (오버로드[overload]에 대한 설명은 생략한다.)  printList()는 public으로 공개되고, printList(String)는 protected로 Entry**의 하위 클래스에서만 사용하도록 되어있다.**

toString 메소드는 인스턴스의 표준적인 문자열 표현을 정의한다. 여기에서는 파일 이름과 크기를 나열해서 표현한다. getName이나 getSize는 추상 메소드이지만 하위 클래스에서 이 두 메소드를 구현하며 toString에서 호출하고 있다([Template Method 패턴](http://jellyms.kr/178 "Templete Method 패턴"))

<pre>public abstract class Entry {
	public abstract String getName();
	public abstract int getSize();

	public Entry add(Entry entry) throws FileTreatmentException {
		throw new FileTreatmentException();
	}

	public void printList() {
		printList(" ");
	}

	protected abstract void printList(String prefix);

	public String toString() {
		return getName() + " (" + getSize() + ")";
	}
}
</pre>

####  File 클래스

File 클래스는 파일을 표현하는 클래스다. Entry 클래스의 하위 클래스로 선언한다. 필드는 두 개이며 파일의 이름을 표현하는 name과 크기를 나타내는 size다. 생성자는 이름과 크기를 제공해서 File의 인스턴스를 만든다. (물론 실제 만들어지지는 않는다.)

getName 메소드와 getSize 메소드는 각각 파일의 이름과 크기를 반환한다.

상위 클래스에서 위임된 printList(String)는 여기에서 구현한다. printList(String)는 prefix와 자신의 문자열 표현을"/"로 구분해서 표시한다. 여기에서는 "/" + this라는 연산을 하고 있지만 문자열과 오브젝트를 더하면 자동적으로 그 오브젝트의 toString 메소드가 호출된다. 이것은 Java 언어의 사양이다. 즉, 다음 식은 모두 동일하다.

<pre>prefix + "/" + this
prefix + "/" + this.toString()
prefix + "/" + toString()
</pre>

추상 메소드는 상위 클래스의 Entry 클래스에서 모두 구현되고 있으므로 File 클래스는 추상 클래스가 아닙니다.

<pre>public class File extends Entry {
	private String name;

	private int size;

	public File(String name, int size) {
		this.name = name;
		this.size = size;
	}

	public String getName() {
		return name;
	}

	public int getSize() {
		return size;
	}

	protected void printList(String prefix) {
		System.out.println(prefix + "/" + this);
	}
}
</pre>

#### Directory 클래스

Directory 클래스는 디렉터리를 표현하는 클래스이며 Entry 클래스의 하위 클래스로 정의되어 있다. 필드는 두 개가 있다. 첫 번째의 필드 name은 디렉터리의 이름을 나타내며 File 클래스와 동일하다. 그러나 Directory에는 크기를 나타내는 필드가 없습니다. 이것은 디렉터리의 크기를 동적으로 계산해서 구하고 있기 때문이다. 두 번째의 필드 directory는 ArrayList로 정의되고 있다. 이 directory가 디렉터리 엔트리를 저장해 두기 위한 필드다. getName 메소드는 name 필드를 반환할 뿐이지만 getSize 메소드는 계산도 실행한다. directory의 요소를 하나씩 꺼내서 그 크기를 합한 것이 반환값이 된다. 예제에서 사용되고 있는 다음 문장에 주의하자.

<pre>get += entry.getSize();</pre>

변수 size에 entry의 크기를 더하고 있지만, 이 entry는 File의 인스턴스일지도 모르고 Directory의 인스턴스일지도 모른다. 두 경우 모두 동일한 메소드 getSize로 크기를 얻을 수 있다. 이것이Composite 패턴의 특징인 '그릇과 내용물의 동일시'를 나타낸다. File의 인스턴스이든, Directory의 인스턴스이든 entry는 어쨌든 entry의 하위 클래스의 인스턴스다. 때문에 getSize를 안심하고 호출할 수 있다. Entry의 하위 클래스로서 다른 클래스가 만들어져도, getSize 메소드를 구현하고 있기 때문에 Directory 클래스의 이 부분을 수정할 필요가 없다. entry가 Directory의 인스턴스인 경우 entry.getSize()라는 식을 평가하면 Directory 안의 엔트리의 크기를 하나하나 더한다. 또 그 안에 디렉터리가 있다면 다시 하위 디렉터리의 getSize를 호출하고.. 이렇게 재귀적으로 getSize 메소드가 호출된다. **Compoiste 패턴의 재귀적 구조가 그대로 getSize라는 메소드의 재구적 호출에 대응하고 있음**을 알 수 있다. add 메소드는 디렉터리 안에 파일이나 디렉터리를 추가하기 위한 것이다. 인수로서 주어진 entry는 그것이 실제로 Directory 클래스의 인스턴스인지, File 클래스의 인스턴스인지 조사할 필요없이

<pre>directory.add(entry);</pre>

로 directory 필드에 추가된다. '추가한다'라는 조작은 ArrayList에 위임하고 있다고 할 수 있다.printList 메소드는 디렉토리의 종류를 표시한다. printList 메소드도 getSize 메소드와 동일하게 printList를 재귀적으로 호출한다. 그 사이에 변수 entry가 File의 인스턴스인지, Directory의 인스턴스인지 조사하지 않는 것도 getSize 메소드의 경우와 동일하다. 그릇과 내용물이 동일시되고 있기 때문이다.

<pre>import java.util.Iterator;
import java.util.ArrayList;

public class Directory extends Entry {
	private String name;
	private ArrayList directory = new ArrayList();

	public Directory(String name) {
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public int getSize() {
		int size = 0;
		Iterator it = directory.iterator();
		while (it.hasNext()) {
			Entry entry = (Entry) it.next();
			size += entry.getSize();
		}

		return size;

	}

	public Entry add(Entry entry) {
		directory.add(entry);
		return this;
	}

	protected void printList(String prefix) {
		System.out.println(prefix + "/" + this);
		Iterator it = directory.iterator();
		while (it.hasNext()) {
			Entry entry = (Entry)it.next();
			entry.printList(prefix + "/" + name);
		}
	}

}
</pre>

#### FileTreatmentException 클래스

FileTreatmentException 클래스는 파일에 대해서 add 메소드를 잘못 호출했을 때 제공되는 예외다. 이 예외는 Java의 클래스 라이브러리에서 제공하는 것이 아니라 이 예제 프로그램을 위해 정의한 클래스다.

<pre>public class FileTreatmentException extends RuntimeException {
	public FileTreatmentException() {
	}

	public FileTreatmentException(String msg) {
		super(msg);
	}

}
</pre>

#### Main 클래스

Main 클래스에서는 다음과 같은 디렉터리 계층을 만든다. 우선 처음에 root, bin, tmp, usr이라는 디렉터리를 만들고 bin의 아래에 vi라는 파일과 latex라는 파일을 넣는다. 그리고 usr 디렉터리안에 Kim, Lee, Park라는 디렉터리를 만들어 각 사람의 파일을 그 아래에 만든다.

<pre>public class Main {
    public static void main(String[] args) {
        try {
            System.out.println("Making root entries...");
            Directory rootdir = new Directory("root");
            Directory bindir = new Directory("bin");
            Directory tmpdir = new Directory("tmp");
            Directory usrdir = new Directory("usr");
            rootdir.add(bindir);
            rootdir.add(tmpdir);
            rootdir.add(usrdir);
            bindir.add(new File("vi", 10000));
            bindir.add(new File("latex", 20000));
            rootdir.printList();

            System.out.println("");
            System.out.println("Making user entries...");
            Directory Kim = new Directory("Kim");
            Directory Lee = new Directory("Lee");
            Directory Park = new Directory("Park");
            usrdir.add(Kim);
            usrdir.add(Lee);
            usrdir.add(Park);
            Kim.add(new File("diary.html", 100));
            Kim.add(new File("Composite.java", 200));
            Lee.add(new File("memo.tex", 300));
            Park.add(new File("game.doc", 400));
            Park.add(new File("junk.mail", 500));
            rootdir.printList();
        } catch (FileTreatmentException e) {
            e.printStackTrace();
        }
    }
}
</pre>

### Composite 패턴의 등장인물

Composite 패턴의 등장인물은 다음과 같다.

Leaf(나뭇잎)의 역할 예제 프로그램에 File 클래스에 해당하며, Leaf는 '내용물'을 표시하는 역할을 하며 내부에는 다른 것을 넣을 수 없다.

Composite(복합체)의 역할 예제 프로그램에 Directory 클래스에 해당하며, Composite는 '그릇'을 나타내는 역할이다. Leaf 역할이나 Composite 역할을 넣을 수 있다.

Component의 역할 예제 프로그램에 Entry 클래스에 해당하며, Leaf 역할과 Composite역할을 동일시하는 역할을 한다. Component는 Leaf 역할과 Composite 역할에 공통적인 상위 클래스로 실현한다.

Client(의뢰자)의 역할 예제 프로그램에 Main 클래스에 해당하며, Composite 패턴의 사용자다. Composite 역할이 포함하고 있는 Component 역할(즉, Leaf 역할이나 Composite 역할)을 부모에 대한 '자식'으로 간주한다. getChild 메소드는 Component 역할로부터 '자식'을 얻기 위한 메소드다.

### 사고넓히기

#### 복수와 단수의 동일시

Composite 패턴은 그릇과 내용물을 동일시하는 패턴이지만, 복수와 단수 역시 동일시 한다고 할 수 있다. 즉, 여러 개를 모아서 마치 하나인 것처럼 취급하는 것이다. 예를 들면 프로그램의 동작 테스트를 생각해보자. Test1에서는 키보드에서의 입력 테스트를 실행하고, Test2에서는 파일에서의 입력 테스트를 실행하고, Test3에서는 네트워크에서의 입력 테스트를 실행한다고 가정하자. Test1,Test2, Test3의 세 개를 합해서 '입력 테스트'로 하고 싶을 때 Composite 패턴을 사용할 수 있다. 복수의 테스트를 모아서 '입력 테스트'로 하고, 또 다른 테스트를 모아서 '출력 테스트'로 하고, 다시 이 둘을 모아서 '입출력 테스트'로 한다. 다음의 주소에서는 테스트 프로그램으로 Composite 패턴을 이용한다. Simple Smalltalk Testing : With Patterns (by Kent Beck) [http://xprogramming.com/testfram.htm](http://xprogramming.com/testfram.htm "Simple Smalltalk Testing : With Patterns (by Kent Beck)") 그외 패턴 사용에 대한 주의사항과 클래스 다이어그램은 책을 참조하자(207p) 11-1 파일 시스템이외에 Composite 패턴이 적용되는 예를 생각해 보십시오. 내가 생각한 답: GUI등을 사용할때 유용하다고 생각 됬다. 예를 들어 AWT를 사용해서 라벨안에 라벨과, 텍스트 박스 등을 넣는식으로 사용 가능하다고 생각된다. 책해설 : HTML의 리스트(ul 요소, ol요소, dl요소)와 테이블 등은 Composite 패턴으로 표현할 수 있다. 11-2 예제 프로그램에 Entry의(하위 클래스의) 인스턴스에서 '풀 패스'를 얻는 기능을 추가하려고 한다. 예를 들어, File의 인스턴스에서

<pre>"/root/usr/Kim/Composite.java"</pre>

라는 문자열을 얻으려고 한다. 이때 예제프로그램의 어떤 클래스를 어떻게 변경하면 좋을까요? 내가 생각한 답 : 여러가지 방법은 떠오르지만 기본적인 설계를 그냥 두고서 하는 방법은 떠오르지 않는다. 책해설 : Entry 쿨래스에 parent 필드(이 인스터스를 가지고 있는 Girectory의 인스턴스)를 저장해보자. 필드 디렉터리(최상위 디렉터리)는 이 parent 필드가 null이 된다고 가정하자. 주어진 인스턴스에서 parent필드를 위쪽 방향으로 가서 풀 패스를 구성한다. Entry 클래스와 Directory 클래스를 수정한다. Directory 클래스에서는 add 메소드에서 parent 필드를 수정한다. Entry 클래스

<pre>public abstract class Entry {
    protected Entry parent; 
    public abstract String getName();
    public abstract int getSize();
    public Entry add(Entry entry) throws FileTreatmentException {
        throw new FileTreatmentException();
    }
    public void printList() {
        printList("");
    }
    protected abstract void printList(String prefix);
    public String toString() {
        return getName() + " (" + getSize() + ")";
    }
    public String getFullName() {                       
        StringBuffer fullname = new StringBuffer();
        Entry entry = this;
        do {
            fullname.insert(0, "/" + entry.getName());
            entry = entry.parent;
        } while (entry != null);
        return fullname.toString();
    }
}</pre>

Directory 클래스

<pre>import java.util.Iterator;
import java.util.ArrayList;

public class Directory extends Entry {
    private String name;
    private ArrayList directory = new ArrayList();
    public Directory(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public int getSize() {
        int size = 0;
        Iterator it = directory.iterator();
        while (it.hasNext()) {
            Entry entry = (Entry)it.next();
            size += entry.getSize();
        }
        return size;
    }
    public Entry add(Entry entry) {
        directory.add(entry);
        entry.parent = this;                
        return this;
    }
    protected void printList(String prefix) {
        System.out.println(prefix + "/" + this);
        Iterator it = directory.iterator();
        while (it.hasNext()) {
            Entry entry = (Entry)it.next();
            entry.printList(prefix + "/" + name);
        }
    }
}
</pre>

Main 클래스

<pre>public class Main {
    public static void main(String[] args) {
        try {
            Directory rootdir = new Directory("root");

            Directory usrdir = new Directory("usr");
            rootdir.add(usrdir);

            Directory youngjin = new Directory("youngjin");
            usrdir.add(youngjin);

            File file = new File("Composite.java", 100);
            youngjin.add(file);
            rootdir.printList();

            System.out.println("");
            System.out.println("file = " + file.getFullName());     
            System.out.println("youngjin = " + youngjin.getFullName());     
        } catch (FileTreatmentException e) {
            e.printStackTrace();

        }
    }
}
</pre>