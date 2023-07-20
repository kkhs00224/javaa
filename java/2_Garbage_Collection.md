# 가비지 컬렉션과 JVM(Java Virtual Machine)
- - -
###### ❗ 해당 글은 제가 공부했던 기억(도서 및 포스트 등)과 개인적 의견이 섞여 작성되었기 때문에 틀리거나 제대로 설명하지 못한 부분이 있을 수 있습니다. 의견 남겨주시면 감사히 여기며 수정하도록 하겠습니다.❗

◀️ [이전글. 오버라이딩(Overriding)과 오버로딩(Overloading)](https://github.com/kkhs00224/mystudy/blob/main/java/1_Override_Overload.md)   
▶️

##  1. 메모리 관리란?
- 자바로 개발을 하다보면, 새로운 객체가 지속적으로 만들어진다.
  ```java
  class Greeting{
    String message;
    printGreetingMessage(String name){
      message = "Hello " + name; // 새로운 "message" 인스턴스 생성
      System.out.println(message);
    } // 메소드를 벗어나면 참조값이 손실된다.
  }
  ```
위의 예에서 `printGreetingMessage("친구들")`가 시작되면, `message`에 "hello 친구들"이 참조값에 할당된다. 그리고 해당 메소드를 벗어나게 되면 message에 할당된 "hello 친구들"은
이제 다시 사용할 수 없지만 메모리에서 바로 사라지지 않는다. `printGreetingMessage()` 메소드가 실행될 때마다 참조값이 할당되고, 메소드가 끝난 후 사용된 참조값이 메모리상에 남아있게 되면
프로그램이 메모리 부족으로 인해 실행되지 않을 수 있다.   

이를 관리하기 위해 자바는 가비지 컬렉션(Garbage Collection)을 통해 자동으로 메모리를 정리하게 된다. C나 C++ 같은 언어에서는 수동으로 `free`나 `delete`를 호출하여 시스템에서 메모리를 
회수해 재사용 할 수 있도록 한다.    

수동으로 메모리를 관리하는 것의 장단점을 알아보자.

먼저 장점을 보면,   
- 객체 제거 시점을 정확하게 제어할 수 있으며,    
- 백그라운드 프로세스가 실행되지 않으므로 시스템 리소스의 소비가 줄어들고, 메모리 사용량을 모니터링할 필요가 없다.   

단점으로는,   
- 메모리에 객체를 안전하게 할당하고 해제하기 위한 제어 코드를 추가하기 위해서 메모리 시스템을 이해하고 적용하는 데에 많은 시간이 소요될 수 있는데,   
- 이는 비즈니스적으로 의미 있는 작업에 소요될 시간을 방해할 수도 있다.   
- 또한 개발자가 오류 조건을 올바르게 처리하지 못해 객체가 삭제되지 않아 메모리 누수가 발생할 수도 있다.   

이런 시행착오를 겪으며 자동 메모리 관리 기술이 계속 발전해왔고, 현재엔 자바를 포함한 많은 프로그래밍 언어가 가비지 컬렉션으로 메모리를 자동으로 관리한다.

## 2. 자바(Java)의 메모리 관리
- 자바는 JVM(Java Virtual Machine)의 일부인 가비지 콜렉터(Garbage Collector)에 의해 처리된다.
  가비지 콜렉터를 통해 주기적으로 가비지를 모으고, 더 이상 도달할 수 없는 객체를 제거하고, 아직 실행 가능한 객체를 재구성해 메모리를 보다 효율적으로 사용한다.

### 2.1 자바의 가비지 컬렉션
\\<!-- https://dev.java/learn/jvm/tool/garbage-collection/java-specifics/ -->
  

- - - 
#### 📖 참고문서
*  [자바의 가비지 컬렉션 소개](https://dev.java/learn/jvm/tool/garbage-collection/intro/)   