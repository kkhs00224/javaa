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
- 자바는 JVM(Java Virtual Machine)의 일부인 가비지 컬렉터(Garbage Collector)에 의해 처리된다.
  가비지 콜렉터를 통해 주기적으로 가비지를 모으고, 더 이상 도달할 수 없는 객체를 제거하고, 아직 실행 가능한 객체를 재구성해 메모리를 보다 효율적으로 사용한다.

### 2.1 자바의 가비지 컬렉션 종류

HotSpot JVM(Java Virtual Machine)은 가비지 컬렉터를 가지고 있으며, 그 중 어떤 가비지 컬렉터 구현체를 사용할지는 사용 가능한 하드웨어 리소스와 응용 프로그램의
성능 요구 사항에 따라 결정된다. 가비지 컬렉터 구현체의 종류를 보면,   

- **Serial Garbage Collector**: 단일 스레드에서 모든 가비지 컬렉션을 실행한다. 일시 중지 시간이 길지만, 리소스 사용이 적어 싱글 프로세서에서 가장
  사용하기 좋다.
- **perallel Garbage Collector**: 직렬 가비지 컬렉터(Serial Garbage Collector)와 다중 스레드를 통해 작업을 수행할 수 있다.
- **Concurrent Mark Sweep(CMS) Garbage Collector**: JDK9에서 사용이 중지되고, JDK14에서 제거된 구현체. 어플리케이션이 수행되는 동안에 가비지 컬렉터를 작동하여 가비지 컬렉션의
  중지 시간을 줄였다.
- **Garbage First (G1) Garbage Collector**: JDK9 이후 기본값이 되었다. CMS GC를 대체한 향상된 가비지 컬렉터. 많은 양의 메모리에 엑세스하는 멀티 프로세서에 적합하다.
- **ZGC**: JDK11에서 실험적으로 등장하였고, JDK15에서 제공됐다. 멀티 테라바이트 힙(Multi-retabyte heaps)을 가지는 어플리케이션에 확장 가능한 초저 지연(Ultra-low latency) GC이다.

### 2.2 힙 메모리(Heap Memory)
힙 메모리는 JVM에서 할당하여 제어하는 시스템 메모리이다. JVM이 사용 가능한 힙 메모리의 크기는 -Xms<value> 및 -Xmx<value> 인수로 제어된다. -Xms<value>는 초기 힙 크기 및 최소 힙 크기를
설정하고, -Xmx<value>는 최대 힙 크기를 설정한다. 예를 들면,

```
java -Xms512m Main
java -Xmx2g Main
```
위의 코드를 명령 프롬프트에 실행하면 사용하면 JVM에게 초기 힙 크기를 512MB로 설정하고, 최대 힙 크기를 2GB로 설정하도록 지시한다. 만약 실제로 명령 프롬프트에서 실행해보았다면,
``` java Main ```을 통해 기본값으로 되돌릴 수 있는 것 같다.   
만약 힙 메모리가 가득 찬 상태에서 새로운 객체를 할당하려고 할 때, JVM은 ```java.lang.OutOfMemoryError``` 예외가 발생시킨다.   
대부분의 자바 가비지 컬렉터 구현체에서 힙 메모리는 객체의 '나이(age)'를 기반으로 영역이 나누어진다. 영역의 수와 유형은 구체적인 구현체에 따라 다르다.

#### 2.2.1 Generational Garbage Collection
자바의 가비지 컬렉터는 대부분 Generational Garbage Collection이다. 객체가 짧은 수명을 가지는 것을 기반으로 하며, 이에 따라 젋은 세대(young generation)와 오래된 세대(old generation) 두 영역으 힙을 나눈다. 할당 시, 객체는 젋은 세대에서 시작하여 더 이상 도달할 수 없는지 주기적으로 확인한다. 그리고 객체가 가비지 컬렉션 주기를 충분한 시간 살아남는다면, 확인을 덜 하는 오래된 세대로 복사된다.   

Generational Garbage Collector는 제거 대상 객체가 더 많이 포함된 힙의 하위 집합을 스캔하여 CPU 시간을 더욱 생산적으로 사용할 수 있다. CPU는 일시 중지 시간을 줄이거나 처리량을 향상시키거나, 메모리 사용량을 줄이는 데 사용 수 있다.




- - - 
#### 📖 참고문서
*  [자바의 가비지 컬렉션 소개](https://dev.java/learn/jvm/tool/garbage-collection/intro/)   
