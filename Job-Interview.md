# Job Interview

### Java & Spring 지식정리

1. OOP ?

   추상화, 캡슐화, 상속, 다형성과 같은 특징을 가짐

   실재하는것을 객체로 표현, 그것들의 상호 작용을 표현

   객체의 상태를 표현 - 변수, 객체의 행동을 표현 - 메서드

   하나의 객체의 청사진과 같은 구조를 표현한것이 클래스,  new 키워드를 이용하여 소프트웨어상에 실체화 하면 그것이 인스턴스다.

2. 객체지향?

   실재하는 것을 객체로 표현, 그 객체들의 상호작용을 코드로 만든것

3. 직렬화? 역직렬화?

   직렬화 : 자바 객체를 외부 통신 시 통신할 수 있는 byte 데이터로 변경하는 것

   역 직렬화 : 바이트 형태의 데이터를 객체로 변경

   java.io.Serializable을 상속받아야함

4. 자바 프로그램 실행과정

   1. 개발자가 작성한 자바코드를 컴파일러가 바이트코드로 변경(.java -> .class)
   2. 클래스로더가 클래스파일을 메모리에 올림
   3. 실행엔진이 로드된 클래스파일을 한줄씩 읽음(interpret)
   4. 각 각의 데이터들이 저장소에 저장이됨(heap, stack, method, PC register, native method stack)
   5. method area에 있는 main()을 실행

5. JVM

   구성

   - class loader
   - execution engine
   - interpret
   - JIT compiler
   - GC(Garbage Collector)

6. GC

   힙영역에서 안쓰는 객체들을 지우고 메모리를 회수

    Young Generation : 최초로 new object가 생성되었을 경우 heap memory에서 위치하는 공간. MinorGC가 발생하면 unreachable object는 삭제되고, surviving object(참조되고 있는 객체들)은 Old generation으로 이동한다.

   Old Generation : Long surviving object들이 머무는 공간. Old generation이 가득차게되면 MajorGC가 발생한다.

   MinorGC : Young generation에서 발생하는 GC

   MajorGC : Old Generation에서 발생하는 GC

   Full GC : Heap memory전체를 clear하는 작업

   Stop the World Event : MinorGC 발생시 Stop the world event가 발생한다. MinorGC를 수행할때는 모든 application thread가 중지하고, 예외는 없다.

   Permanent generation : Java application에 필요한 클래스의 메타데이터를 자장하는 곳. Java8을 기준으로 Permanent generation은 heap memory가 아닌 native memory로 관리됨.

   - 종류
       - Serial GC
           - minor GC, major GC, 모두 순차적으로 진행
           - deletion and compacting 수행
       - Parallel GC
           - GC수행시 멀티스레드 사용
           - deletion and compacting 수행
       - CMS Collector
           - deletion and compacting 수행안함
       - G1(권장)
           - java7 부터 가능
           - 여러 CPU와 아주 큰 memory에서 효과적인 GC
           - Java 9 에서 default GC(이전까지 parallel GC)

7. Annotaition

   데이터 유효성과 같은 검사를 쉽게하게 됨, 코드가 깔끔해짐

   메타데이터의 비중이 가장 큼

   - 메타데이터 : 데이터를 위한 데이터, 한 데이터에 대한 설명(자신의 정보를 담고 있는 데이터)

8. Reflection

   객체를 통해 클래스의 정보를 분석해냄

9. BeanFactory(Spring Container) VS ApplicationContext

   인스턴스화 시점이 다르다.

   ApplicationContext 는 BeanFactory를 상속받았다

   BeanFactory - lazy loading

   ApplicationContext - pre loading

   Spring문서에는 웬만하면 ApplicationContext를 사용하라고 권장

   ApplicationContext가 좀 더 확장된 인터페이스, ApplicationContext 내부에 새로운 빈팩토리를 가지고있다?

10. Comparator VS Comparable

    - Comparable - 타입이 같은 다른 오브젝트와 비교하기 위한 인터페이스
    - Comparator - 이미 정의된 방법과 다르게 비교하기 위한 인터페이스

11. Redis

    caching 용 in-memory data structure

    - key-value 로 구성

12. Spring

    1. Bean Life Cycle

       - 컨테이너에서 빈 설정정보(Bean Configuration)를 읽는다
       - 컨테이너에서 빈 조립설명서(Bean Definition)를 생성한다
       - 이제 컨테이너에서 빈을 생성하고 초기화, 사용, 소멸 단계로 수행되는데 이후는 코드를 통해 확인한다.

       - 순서
         1. Aware - reflection??
         2. InitializingBean - 빈이 생성된 직후 행동(후처리)
         3. BeanPostProcessor - applicationContext가 관리하는 모든 빈에 대해 전 후 시점으로 수행할 프로세스 등록
         4. DisposableBean - ApplicationContext가 소멸될 때 빈 리소스를 정리할 수 있게하는 확장포인트

    
