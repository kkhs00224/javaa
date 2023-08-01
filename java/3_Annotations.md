# 어노테이션(Annotations)
- - -
###### ❗ 해당 글은 제가 공부했던 기억(도서 및 포스트 등)과 개인적 의견이 섞여 작성되었기 때문에 틀리거나 제대로 설명하지 못한 부분이 있을 수 있습니다. 의견 남겨주시면 감사히 여기며 수정하도록 하겠습니다.❗

◀️ [이전글. 가비지 컬렉션과 JVM](https://github.com/kkhs00224/mystudy/blob/main/java/2_Garbage_Collection.md)   
▶️   

## 1. 어노테이션의 용도
 자바를 사용하면서, 그리고 스프링 프레임워크를 사용하면서 우리는 많은 어노테이션을 만나게 된다. 스프링에서는 해당 프레임워크에서 확장된 개별 어노테이션들을 많이 가지고 있지만, 자바에서의 용도와 구현법을 보며 어노테이션에 대해 알아보도록 하자.   
 우선 어노테이션(Annotation, 주석)은 다음의 세 가지로 용도를 구분해볼 수 있다.

- 컴파일러를 위한 정보 - 컴파일러 오류를 감지하거나 경고를 억제할 수 있다.
- 컴파일 시간(compile-time)과 배포 시간(deployment-time) 처리 - 소프트웨어 도구는 어노테이션(anotation, 주석) 정보를 처리하여 코드, XML 파일 등을 생성할 수 있다.
- 런타임 처리(Runtime processing) - 일부 어노테이션은 런타임을 검사할 수 있다.

 어떤 용도로 쓰이는지 개략적으로 알아봤으니 이제 자세하게 사용할 수 있는 위치, 적용하는 방법, 자바 플랫폼 표준에서 이용할 수 있는 미리 정의된 주석의 유형, 타입 애노테이션을 플러그 가능한 타입 시스템과 함께 더 강력한 검사를 수행하는 코드 작성법, 반복 어노테이션 구현하는 방법 등에 대해 알아보자.

### 1.1 어노테이션의 형식(The Format of an Annotation)
- 골뱅이 기호 문자(The at sign character) '@'가 뒤에 오는 것을 컴파일러에게 알려준다. 따라서 아래 예에서 사용된 어노테이션의 이름은 Override이다. 이처럼 어노테이션을 통해 해당 메서드가 상위 객체로부터 오버라이드된 메서드라는 것을 알 수 있다.
 ```java
 @Override
 void mySuperMethod(){ ... }
 ```

- 어노테이션은 아래 예와 같이 이름이 있거나, 없는 요소(element)를 포함할 수 있고, 그 요소에 대한 값을 지정할 수 있다. 또한 ```value```라는 이름으로 지정한 오직 하나의 값만 존재하는 경우, value를 생략하여 값만 저장할 수 있다.
 ```java
 @Author(
   name = "Kim H.S.",
   date = "3/27/2003"
 )
 class MyClass { ... }
 
 @SuppressWarnings(value = "unchecked") // value에 저장한 "unchecked"
 void myMethod() { ... }
 
 @SuppressWarnings("unchecked") // value라는 이름을 생략하여 값만 적을 수 있다.
 void myMethod() { ... }
 ```
  위의 경우들과 다르게 요소를 가지고 있지 않다면, ```@Override``` 어노테이션처럼 뒤의 괄호를 생략할 수 있다.   

-  같은 선언 내에서 여러 개의 어노테이션을 사용할 수 있다.
 ```java
 @Author(name = "Kim H.S.")
 @EBook
 class MyClass { ... }
 ```
- 같은 타입의 어노테이션을 여러 개 가지고 있는 경우, repeating annotation(자바 SE 8 이상 지원)이 호출된다.
 ```java
 @Author(name = "Kim H.S.")
 @Authot(name = "Kim Repeat")
 class MyClass { ... }
 ```
 Repeating annotations에 대해서는 뒤에서 더욱 자세하게 다뤄보도록 한다.   
   
 어노테이션의 타입은 자바 SE API의 ```java.lang```나 ```java.lang.annotation``` 패키지에 정의된 타입 중에서 사용할 수도 있고, 사용자가 직접 어노테이션 타입을 정의할 수도 있다.   
  위의 예시에서 예를 들자면 "Override", "SuppressWarnings" 타입이 자바에서 미리 정의된 어노테이션이고, "Author", "Ebook" 어노테이션이 사용자 정의(custom) 어노테이션 타입이다.

## 2. 어노테이션의 사용 위치(Where Annotations Can Be Used)
어노테이션은 클래스, 필드, 매서드 등의 선언에 적용될 수 있다, 선언에 사용될 때는 주로 관례적으로 한 줄에 표현되는 경우가 많았다.   
하지만 자바 SE 8에서, 어노테이션은 타입의 사용에도 적용할 수 있게 되어 자신만의, 하나의 줄에 표현되지 않는 경우가 생겼다. 아래의 예시들이 타입 어노테이션을 호출하는 형식이다.   

- 클래스 인스턴스 생성 표현식(Class instance creation expression)
  ```java
  new @Interned MyObject(); 
  ```
- 타입 캐스트(type cast)
  ```java
  myString = (@NonNull String) str;
  ```
- 구현 절(implements clause)
  ```java
  class UnmodifiableList<T> implements
   @Readonly List<@Readonly T> { ... }
  ```
- 예외 던지기 선언(Thrown exception declaration)
  ```java
  void monitorTemperature() throws
   @Critical TemperatureException { ... }
  ```

@Interned 어노테이션의 사용은 객체 동일성(==) 사용을 유효하게 만드는 최적화 어노테이션이라고 한다. 이를 사용하면 ```new String()```로 선언되어 힙 영역에서 독립적인 객체 주소를 참조하게 되어 "==" 비교가 불가하고 ```strObj1.equal(strObj2)```을 사용해 문자열 비교를 해야하게 만든다. 이를 힙 영역의 String pool에 위치시키게 만들어 "==" 동등 비교가 가능하게 만드는 방법으로 ```strObj.intern()```의 반환값을 사용하는 방법이 있는데, @Interned 타입 어노테이션을 사용해 이를 실행하는 것으로 보인다.   
하지만 실제 코드를 실행해보면(Java17로 실행했음) @Intered 어노테이션이 포함되어 있지 않음을 알 수 있다. Java8에서도 이 어노테이션이 포함되어 있지 않다고 하며, [Chechker Framework의 어노테이션 매뉴얼](https://checkerframework.org/manual/#annotating-libraries)을 보면 알 수 있듯이 Checker Framework을 추가해야 사용할 수 있다. 해당 프레임워크는 IDE에서 혹은 Gradle, Maven 등 빌드 도구를 통해 추가해서 사용해야 한다.
 @Readonly, @Critical의 경우도 Java SE에서 실행이 불가능gkek.
즉, 표준 버전에 기본적으로 정의되어 있지 않고 프레임워크를 추가해야 사용할 수 있다는 것인데, 공식 문서의 튜토리얼 예제에 해당 타입 어노테이션을 사용한 이유가 무엇인지 궁금해진다.

## 3. 어노테이션 타입 선언(Declaring an Annotation Type)
많은 어노테이션들이 코드의 주석(comment)를 대체한다.

아래와 같이 중요 정보들을 주석(comment)로 만들어 놓는다고 하자.
```java
public class WorkClass {

   // Author: Kim H.S.
   // Date: 2023-07-22
   // Current revision: 2
   // Last modified: 2023-07-30
   // By: Kim H.S.
   // Reviewers: Lee, Park, Beak

   // class code goes here
   // ...

}
```
위 주석의 메타데이터를 어노테이션을 정의해 대체할 수 있다. 아래의 코드가 어노테이션 타입을 정의하는 문법이다.
```java
@Documented
@interface ClassPreamble {
    String author();
    String date();
    int currentRevision() default 1;
    String lastModified() default "N/A";
    String lastModifiedBy() default "N/A";
    // Note use of array
    String[] reviewers();
}
```
어노테이션 타입은 인터페이스 정의와 비슷하게 "interface"라는 키워드를 사용하지만, 해당 키워드 앞에 "@"를 사용해 "@interface"로 정의한다. 어노테이션 타입은 인터페이스의 형태 중 하나이다.   
   
앞선 예제들에서 요소(element)들을 선언한 어노테이션들이 있다. 매소드와 비슷하게 보일 수 있는데, 어노테이션은 기본값을 정의할 수 있다는 점을 알아두자.   
   
어노테이션 타입을 정의한 후 아래와 같이 값을 채워 넣어 사용할 수 있다.
```java
@ClassPreamble(
        author = "Kim H.S.",
        date = "2023-07-22",
        currentRevision = 2,
        lastModified = "2023-07-30",
        lastModifiedBy = "Kim H.S.",

        reviewers = {"Lee", "Park", "Beak"}
)
public class WorkClass {

   // class code goes here
   // ...

}
```
주의할 점으로, @ClassPreamble를 Javadoc으로 생성된 문서에 나타나게 하려면, 아래와 같이 @Documented 어노테이션을 붙여야 한다.
```java
import java.lang.annotation.Documented;

@Documented
@interface ClassPreamble {

  // Annotation element definitions

}

```

## 4. 사전 정의 어노테이션 타입(Predefined Annotation Types)
자바에서 미리 정의된 어노테이션 타입으로는 java.lang에 정의된 ```@Deperecated```, ```@Override```, ```@SuppressWarnings```이 있다.

- @Deprecated
  ```@Deprecated``` 어노테이션은 더 이상 사용하면 안 되거나, deprecated로 표시된 요소(element)를 나타낸다. 컴파일러는 프로그램에서
  ```@Deprecated``` 어노테이션을 가진 메소드, 클래스, 필드를 사용할 때마다 경고를 만들어낸다. 요소(element)가 더 이상 사용되지 않을 때(deprecated),  Javadoc의 ```@deprecated``` 태그를 사용해 문서화 해야 한다.
  아래 예시에서 Javadoc 주석(comment)과 어노테이션이 같이 쓰인 것은 두 개가 개념적으로 관련되어 있기 때문이다. 또한, Javadoc 태그에서는 소문자 d로 시작되었고, 어노테이션에서는 대문자 D로 시작된 것을 알아둬야 한다.
  ```java
  // Javadoc comment follows
  /**
   * @deprecated
   * explanation of why it was deprecated
   */
  @Deprecated
  static void deprecatedMethod() { ... }
  ```
  자바 SE 9에서 ```forRemoval``` 속성이 ```@Deprecated``` 어노테이션에 추가되었다. forRemoval은 해당 어노테이션이 달린 요소가 향후 버전에서 제거될지 여부를 나타낸다. 기본값은 false이다.



- - - 
#### 📖 참고문서
*  [자바 어노테이션 튜토리얼 문서](https://dev.java/learn/annotations/)
*  [자바 타입 어노테이션, CSAIL 디지털 아카이브](http://publications.csail.mit.edu/abstracts/abstracts07/jsr308/jsr308.html)
*  [@Interned, @NonNull 어노테이션 오류 질문](https://stackoverflow.com/questions/20197440/java-8-type-annotations-and-jsr-308)
