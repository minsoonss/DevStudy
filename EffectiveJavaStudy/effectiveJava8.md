## finalizer와 cleaner 사용을 피하라

**자바의 객체 소멸자**

1. finalizer - 예측할 수 없고 상황에 따라 위험할 수 있어 일반적으로 불필요
2. cleaner - 자바 9 이상에서 finalizer가 deprecate 되고 나왔지만 비교적 덜 위험하고, 느리며 그래서 불필요하다

C++에서의 destructor랑은 다르다. C++의 destructor는 어떤 객체와 연관이는 자원을 반납할 때도 사용하지만, 자바에서는 `try-with-resources`또는 `try-finally` 블럭이 그 역할을 한다.



### 단점 1

cleaner 와 finalizer가 언제 실행될지 모른다. 객체가 필요 없어진 시점에 바로 실행되리라하는 보장이 없다

**즉 finalizer와 cleaner로는 제때 실행되어야할 작업은 절대 할 수 없다.** 예를 들면 파일 닫기 같은것을 맡기면 중대한 오류를 만들 수 있다. 자원 반납이 되지 않아 더 이상 새로운 파일을 열 수 없을 수도 있다.

### 단점 2

자바 언어 명세는 finalizer와 cleaner 는 수행 시점 뿐 아니라 수행되는지 여부도 보장하지 않는다. 그렇기 때문에 **상태를 영구적으로 수정하는 작업에서는 finalizer와 cleaner에 의존하면 안된다** 예를 들어 데이터베이스 같은 공유 자원 락 해제를 finalizer나 cleaner에 맡기게 되면 분산시스템 전체가 서서히 멈출 것이다. Cleaner는 별도의 쓰레드로 동작하기 때문에 그나마 조금 나을 수 있지만 해당 쓰레드가 언제 동작할지는 알 수 없다

`System.gc`나 `System.runFinalization`에 속지 말라. 그걸 실행해도 Finalizer나 Cleaner를 바로 실행한다고 보장하진 못한다. 그걸 보장해주겠다고 만든 `System.runFinalizersOnExit`와 그 쌍둥이 `Runtime.runFinalizersOnExit`은 둘다 망했고 수십년간 지탄을 받아오고있다.

### 단점 3

**finalizer와 cleaner는 심각한 성능문제도 동반한다.** 필자의 컴퓨터에서 `AutoClosable` 객체를 생성하고 수거하기까지 12ns가 걸린 반면 finalizer를 사용하면 550ns가 걸렸다 50배정도가 걸린셈이다. Cleaner를 사용한 경우에는 66ns가 걸렸다 약 5배.

### 단점 4

**finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수도 있다.** 공격 원리는 생성자나 직렬화 과정에서 예외가 발생하면, 이 생성되다 만 객체에서 악의적인 하위 클래스의 finalizer가 수행될 수 있게 된다. 이 finalizer는 정적 필드에 자신의 참조를 할당하여 가비지 컬렉터가 수집하지 못하게 막을 수 있다. 이렇게 일그러진 객체가 만들어지고 나면, 이 객체의 메서드를 호출해 애초에는 허용되자 않았을 작업을 수행하는 건 일도 아니다. **객체 생성을 막으려면 생성자에서 예외를 던지는 것만으로 충분하지만, finalizer가 있다면 그렇지도 않다.** Final 클래스는 상속이 안되니까 근본적으로 이런 공격이 막혀 있으며, 다른 클래스는 `finalize()` 메소드에 `final` 키워드를 사용해서 상속해서 오버라이딩 하는 것을 만을 수 있다.

### 대책!!

`AutoClosable` 을 구현해주고 클라이언트에서 다 쓰고나면 close 메소드를 호출하면 된다(일반적으로 예외가 발생해도 제대로 종료되도록  try-with-resources를 사용해야 한다) close 메소드에서 이 객체는 더 이상 유효하지 않음을 필드에 기록하고, 다른 메서드는 이 필드를 검사해서 객체가 닫힌 후에 불렸다면 `IllegalStateException`을 던지는 것이다.

```java
public class SampleResource implements AutoCloseable {

    private boolean closed;

    private static final Cleaner CLEANER = Cleaner.create();

    private final Cleaner.Cleanable cleanable;

    private final ResourceCleaner resourceCleaner;

    public SampleResource() {
        this.resourceCleaner = new ResourceCleaner();
        this.cleanable = CLEANER.register(this, resourceCleaner);
    }

    private static class ResourceCleaner implements Runnable {

        @Override
        public void run() {
            System.out.println("Clean");
        }
    }

    @Override
    public void close() throws RuntimeException {
        if (this.closed) {
            throw new IllegalStateException();
        }

        closed = true;
        cleanable.clean();
    }

    public void hello() {
        System.out.println("hello");
    }

}
```



### 대체 finalizer와 cleaner는 어디에 쓰는가?

자원 반납에 쓸 `close` 메소드를 클라이언트가 호출하지 않았다는 가정하에, 물론 실제로 Finalizer나 Cleaner가 호출될지 안될지 언제 호출될지도 모르긴 하지만, 안하는 것 보다는 나으니까. 실제로 자바에서 제공하는 `FileInputStream`, `FileOutputStream`, `ThreadPoolExecutor` 그리고 `java.sql.Connection`에는 안전망으로 동작하는 finalizer가 있다.

### 네이티브 피어 정리하기

```
자바 클래스 -> 네이티브 메소드 호출 -> 네이티브 객체 (네티이브 Peer)
```

네이티브 객체는 일반적인 객체가 아니라서 GC가 그 존재를 모른다. 네이티브 피어가 들고 있는 리소스가 중요하지 않은 자원이며, 성능상 영향이 크지 않다면 Cleaner나 Finalizer를 사용해서 해당 자원을 반납할 수도 있을 것이다. 하지만, 중요한 리소스인 경우에는 위에서 언급한대로 `close` 메소드를 사용하는게 좋다.

### Cleaner 예제 코드

```java
public class CleanerSample implements AutoCloseable {

    private static final Cleaner CLEANER = Cleaner.create();

    private final CleanerRunner cleanerRunner;

    private final Cleaner.Cleanable cleanable;

    public CleanerSample() {
        cleanerRunner = new CleanerRunner();
        cleanable = CLEANER.register(this, cleanerRunner);
    }

    @Override
    public void close() {
        cleanable.clean();
    }

    public void doSomething() {

        System.out.println("do it");
    }

    private static class CleanerRunner implements Runnable {

        // TODO 여기에 정리할 리소스 전달

        @Override
        public void run() {
            // 여기서 정리
            System.out.printf("close");
        }
    }

}
```

**주의**

- Cleaner 쓰레드(`CleanerRunner`)는 정리할 대상인 인스턴스 (`CleanerSample`)을 참조하면 안된다. 순환 참조가 생겨서 GC 대상이 되질 못한다.
- Cleaner 쓰레드를 만들 클래스는 반드시 static 클래스여야 한다. non-static 클래스(익명 클래스도 마찬가지)의 인스턴스는 그걸 감싸고 있는 클래스의 인스턴스를 잠조하지 않는다.



### 참고

https://github.com/keesun/study/blob/master/effective-java/item8.md