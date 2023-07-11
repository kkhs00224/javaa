<h2>오버라이드(Override)와 오버로딩(Overloading) 그리고 상속(Inheritance)과 다형성(Polymorphism)</h2>
<hr>
<h6 style="color:gray;">❗해당 글은 제가 공부했던 기억(도서 및 포스트 등)과 개인적 의견이 섞여 작성되었기 때문에 틀리거나 제대로 설명하지 못한 부분이 있을 수 있습니다. 의견 남겨주시면 감사히 여기며 수정하도록 하겠습니다.❗</h6>
<h3>1. 오버라이딩(Override)</h3>
<p>자바 공부를 하면서 헷갈릴 수 있는 두 개의 용어에 대해 복습해본다.</p>
<p>
  먼저
  <br>
  (1) 오버라이딩은 부모 클래스 혹은 구현 인터페이스에 존재하는 메소드를 새롭게 정의하는 것을 말한다. 예를 들면,
  <pre>
  public class ParentClass {
    public void hello(){
        System.out.println("Hello, I'm your father(Mother)");
    }
  }</pre>
  <p><b>ParentClass</b>라는 이름의 클래스가 존재하고, </p>
  <pre>
    public interface YourInterface {
      public void helloo();
   }</pre>
   
  <p><b>YourInterface</b>라는 인터페이스가 존재할 때, 이를 상속, 구현한 클래스 <b>ChildClass</b>가 있다.</p>
  
  <pre>  
    public class ChildClass extends ParentClass implements YourInterface{
      @Override
      public void hello(){
          System.out.println("I'm your son(daughter)");
      }
  
      @Override
      public void helloo() {
          System.out.println("I have implemented!");
      }
  
      public static void main(String[] args) {
          ParentClass pc = new ParentClass();
          ParentClass cc = new ChildClass();
          YourInterface yi = new ChildClass();
          
          pc.hello();
          cc.hello();
          yi.helloo();
      }
    }
    </pre>

  <p>
    <b>ChildClass</b>에는 각각 <b>ParentClass</b> 클래스에서 상속받은 매소드, <b>YourInterface</b> 인터페이스에서 구현한 매소드에 <code>@override</code> 어노테이션이 붙어 있다.
    <br>
    그리고 main 메소드를 통해 이를 실행해보면,
<pre>
  Hello, I'm your father(Mother)
  I'm your son(daughter)
  I have implemented!
</pre>
    세 개의 문장이 출력된 것을 알 수 있다. 이를 통해 알 수 있는 점은, <code>new</code> 연산자로 생성된 <b>ParentClass</b> 인스턴스는 자신이 가진 메소드를 그대로 출력하고,
    <b>ChildClass</b> 인스턴스는 자신의 클래스에서 부모 클래스가 가진 메소드의 이름으로 다른 문장을 입력하는 메소드를 만들어냈다.
    <br>
    인터페이스의 경우 중괄호({})가 없이 메소드명과 파라미터를 위한 소괄호(())만 존재하는 메소드, 즉 추상화되어 있는 메소드를 <code>implements</code>를 통해 
    <b>ChildClass</b>에 구현이 강제되어 메소드 기능을 구현했다.
    <br>
    상속 관계를 놓고 보자면, 부모 클래스의 메소드를 자식 클래스에서 재정의하여 같은 이름의 다른 기능을 가진 메소드로 덮어씌우는 것이라고 할 수 있다.
    구현 관계의 경우는 구현이 강제된 형식의 메소드를 구현하는 클래스(해당 예제에서는 <b>ChildClass</b>)에서 같은 이름, 파라미터(매개변수)로 구현하였다는 것을 표시하는 것 같다.
    <br>
    사실 상속과 구현 관계를 나누어 설명할 필요가 있나 싶기도 하다. 결국은 같은 이름을 가진 상속/구현 관계의 메소드를 덮어쓰는 것이라고 보면 좋을 것 같다.
  </p>

  <h4>1.1 상속(Inheritance)</h4>
  <p>
    위에서 계속 설명하였던 상속이란 객체지향 프로그래밍 언어의 특징 중 하나라고 할 수 있다.
    <br>
    부모(상위, super) 클래스가 가진 메소드 전역변수(객체변수, static 변수), 생성자 등을 <code>extends</code> 키워드를 통해 자식(하위, sub) 클래스에서 접근, 사용하고 재정의(Override)할 수 있도록 
    만드는 것이다. 이를 통해 같은 계열, 특징(공통점, 공통 변수, 메소드)을 상속받은 여러 객체를 만들어낼 수 있다. 또한, 자바의 경우 단일 상속만 가능하다는 규칙이 있으며, 
    파이썬과 같이 다중 상속이 가능한 언어도 존재한다.
    <br>
    1번 오버라이딩에서 나온 예제 <b>ParentClass</b>와 <b>ChildClass</b>가 이와 같은 상속 관계에 속하며, 오버라이딩을 제한하고 싶다면 부모 클래스에서 메소드를 
    <code>private</code> 접근제어자를 통해 정의하면 된다.
  </p>
  <p>
    자바는 모든 클래스의 상위 클래스가 <b>Object</b> 클래스이며, <b>Object</b> 클래스가 가진 멤버변수와 메소드 등에 대해서는 나중에 따로 다루도록 하겠다.
  </p>

  <h4>1.2 추상화(Abstraction)</h4>
  <p>
    앞서 인터페이스(Interface)에서 사용된 것과 같은 추상화 문법은, 인터페이스 이외에 추상 클래스(Abstract Class)에서도 사용이 된다. 이렇게 추상화된 요소들을 통해
    구현된 클래스들이 공통적으로 구현해야 하는 메소드를 강제하게 만든다. 
    <br>
    추상화도 상속과 같은 객체지향의 특징 중 하나이다.
  </p>

  <h3>2. 오버로딩(Overloading)</h3>
  <p>
    오버로딩은 클래스, 인터페이스 등에서 메소드명에 파라미터 타입, 파라미터의 개수를 다르게 하여 하나의 이름을 가진 메소드를 여러 개 만들어내는 것을 말한다. 
    오버라이딩처럼 따로 어노테이션이 존재하지는 않는다.
    <br>
    예시를 통해 오버로딩에 대해 자세히 알아보자.
  </p>
<pre>
  public class Overloading {
    public void add(){
        System.out.println("What do you want?");
    }
    public void add(int a, int b){
        System.out.printf("%d + %d = %d\n", a, b, a+b);
    }
    public static void main(String[] args) {
        Overloading ol = new Overloading();
        ol.add();
        ol.add(1,2);
    }
  }
</pre>
  <p>
    위의 클래스 <b>Overloading</b>을 보면, <code>add()</code>라는 이름을 가진 메소드를 두 개 가지고 있다. 하나는 파라미터(매개변수)를 하나도 가지지 않고, 하나는 a, b라는 매개변수를 가지고 있다.
    이처럼 하나의 이름을 가졌지만, 매개변수의 개수, 타입에 따라 여러 개를 가질 수 있는 것을 오버로딩이라 한다.
    <br>
    위 클래스의 main 메소드를 실행하면 아래위 같은 결과를 얻는다.
<pre>
  What do you want?
  1 + 2 = 3
</pre>
    만약 이 메소드에 문자와 숫자를 입력하면 문자와 숫자를 받아 계산하는 기능은 없기 때문에 오류가 발생할 것이다.
    <br>
    <code>"안녕"</code>이라는 문자와 <code>123</code>이라는 숫자를 변수로 넣으면 <code>"안녕123"</code>과 같이 출력을 하고 싶다면 어떻게 해야할까?
<pre>
  public void add(String s, int a){
        System.out.printf("%s + %d = %s%n", s, a, s, a);
  }
</pre>
    위의 메소드를 해당 클래스에 추가하고, main 메소드에 <code>ol.add("안녕",123);</code>을 입력한다면 
<pre>
  안녕 + 123 = 안녕123
</pre>
    문자와 숫자의 더하기도 출력하는 메소드로 만들 수 있다.
  </p>
  <p>
    이와 같이 하나의 메소드로도 파라미터에 따라 다양한 기능을 호출하게 만들 수 있다.
  </p>
</p>
<h3>3. 다형성</h3>
<p>
  앞서 객체지향의 특성 중 상속과 추상화에 대해 이야기했다. 이외에도 다형성과 캡슐화(Encapsulation)를 합쳐 네 가지의 특징이 대표적인 특징이라고 할 수 있다. 이 중 캡슐화의 경우는 
  다음에 따로 설명하도록 하겠다.
  <br>
  위의 예제들에서 사용한 오버라이딩, 오버로딩도 모두 다형성에 속한다고 볼 수 있다.
  다형성은 크게 타입의 다형성, 매개변수 다형성, 메소드 다형성으로 나누어서 생각해볼 수도 있다.
</p>
<h4>(1)타입의 다형성</h4>
<p>
  먼저 타입의 다형성을 설명하기 위해 오버라이딩을 설명할 때 만들었던 예제 중 일부를 가져오겠다.
<pre>
    ParentClass pc = new ParentClass();
    ParentClass cc = new ChildClass();
</pre>
  부모 클래스와 그를 상속한 자식 클래스를 <code>new</code> 연산자를 통해 변수 안에 인스턴스로 생성했다. 그런데 자식 클래스의 경우 자신의 클래스인 <code>ChildClass</code> 타입으로
  변수를 저장한 것이 아닌 <code>ParentClass</code> 객체 타입으로 변수를 생성했다.
  <br>
  예시에서는 부모 클래스를 상속받는 자식 클래스가 하나밖에 없기 때문에 크게 보이지는 않지만, 부모 클래스를 통해 같은 계열의 여러 자식 클래스를 더욱 간편하게 사용하기 위해 사용된다.
</p>
<p>
  더욱 자세히 알아보기 위해 모든 객체의 상위 객체인 <code>Object</code>를 사용한 예제를 하나 생각해보자.
</p>
<pre>
  public class Many {
    public static void main(String[] args) {
        List<Object> many = new ArrayList<>();
        many.add(1);
        many.add("하나");
        many.add(1.234);
        many.add('a');
        many.add(false);
    }
  }</pre>
  <p>
    Object 객체타입 리스트를 만들어 정수, 실수, 문자, 문자열, 논리형 타입을 모두 추가할 수 있다. 또한 우리가 만든 참조형 객체 타입들도 모두 담아낼 수 있다. 
    물론 위와 같이 Object 객체에 통해 모든 기본형 타입과 String 문자열을 받아내는 방식은 한 가지 타입만 받아야 할 때 다른 타입도 무조건 받을 수 있어 추후 오류를 발생시킬 수도 있다.
    <br>
    돌아가서, 위와 같이 필요한 클래스들을 상위 클래스를 타입으로 묶어 코드를 압축해 코드를 더욱 효율적으로 사용할 수 있게 만드는 것이다.
  </p>
  <h4>(2)매개변수 다형성</h4>
  <p>
    매개변수의 다형성은 말 그대로 매개변수에 부모 클래스를 받도록 함으로써 메서드의 길이를 크게 줄일 수 있다.
  </p>
  <h4>(3)매개변수 다형성</h4>
  <p>
    위에서 다룬 오버라이딩과 오버로딩도 하나의 이름으로 다양한 기능을 지닌 메서드를 만들어내기 때문에 다형성 중의 하나라고 볼 수 있다.
  </p>

  <h3>마무리</h3>
  <p>헷갈릴 수 있는 오버로딩(Overloading)과 오버라이딩(Overriding)의 차이를 알아보며 다형성, 객체지향 언어의 특징을 간단하게 살펴보았다.</p>
  <p>
    복습을 하며 자연스럽게 객체지향 프로그래밍 언어의 특징 중 세 가지도 다시 공부하게 되었는데, 생각보다 어떻게 말로 풀어내야할지 감이 잘 잡히지 않아 길게 작성하지 못했다.
    너무 많은 걸 처음 글에 다 담아내려 해서 체력적으로나 시간적으로 더 힘들었던 부분도 있다.
    <br>
    앞으로 부족했던 것, 헷갈리는 것, 궁금했던 것 모두 탐구하고 정리해나가며 개발자라는 꿈에 한발짝 더 다가가고 싶다.
  </p>
