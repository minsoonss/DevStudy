# 다 쓴 객체 참조를 해제하라

### 메모리 직접 관리

자바에 GC(가비지 콜렉터)가 있기에 메모리 관리에 더 이상 신경 쓰지 않아도된다고 오해할 수 있는데... 진짜 오해다

```java
public class Stack {
  private Object[] elements;
  
  private int size = 0;
  
  private static final int DEFAULT_INITIAL_CAPACITY = 16;
  
  public Stack() {
    this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
  }
  
  public void push(Object e) {
    this.ensureCapacity();
    this.elements[size++] = e;
  }
  
  public Object pop() {
    if(size == 0) {
      throw new EmptyStackException();
    }
    
    return this.elements[--size];
    //위의 한줄에서 메모리 누수 발생
    //사이즈를 줄이긴하지만 그 위치에는 여전히 값이 존재
  }
  
  private void ensureCapacity() {
    if(this.elements.length == size) {
      this.element = Arrays.copyOf(elements, 2 * size + 1);
    }
  }
}
```

보통 위와 같은 코드들은 테스트를 통해서는 에러를 발견하기 쉽지 않다. 그러나 해당 프로그램을 오래 사용할 경우 GC의 활동과 메모리의 사용량이 늘어나 성능이 저하될 것이며, 심한 경우 `디스크 페이징` 혹은 `OutOfMemoryError` 가 위 발생할 수도 있다. 

- 디스크 페이징? 프로그램 중 자주 사용되지 않는 부분의 작업 메모리를 하드디스크로 옮기는 방식을 통해 메모리공간을 증가시키기 위한 방법

위의 주석과 같은 이유로 메모리 누수가 존재한다.

그래서 아래와 같이 코드를 수정 해야한다.

```java
public Object pop() {
  if(size == 0) {
    throw new EmptyStackException();
  }
  
  Object value = this.elements[--size];
  this.elements[size] = null;
  return value;
}
```

위의 경우 해당 위치에 있는 객체를 다시 꺼내는 경우에 `NullPointerException`이 발생할 수 있긴 하지만, 그 자리에 있는 객체를 비우지 않고 실수로 잘못된 객체를 돌려주는 것 보다는 괜찮다. 하지만 `null`이 반환되어 에러를 빨리 포착하는 것이 훨씬 유익하다. 하지만 다 쓴 객체를 모두 `null` 처리 하는데에 혈안이 될 필요는 없다. 프로그램을 지저분하게 만들 뿐이다. `null` 처리를 하는 경우는 지극히 예외 경우여야 한다. 다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위(scope) 밖으로 밀어내는 것이다. 여기서 scope은 {}(중괄호) 로 묶여진 블록 범위다

그럼 언제 레퍼런스를 `null`로 설정해야 하는가? **메모리를 직접 관리 할 때** 객체를 프로그래머가 직접관리할 때는 프로그램이 이 객체가 유효한 객체인지 판단을 못한다. 그러니 프로그래머가 `null` 처리를 해줘야 한다.

`캐시`도 똑같은 경우이다. 객체 레퍼런스를 캐시에 넣어놓고, 이 사실을 까맣게 잊은 채 한참동안 캐시에 놔두는 일은 빈번하다. 그것의 해결책은 `WeakHashMap`이다.  `WeakHashMap`은 `캐시의 키`에 대한 레퍼런스가 캐시 밖에서 필요 없어지면 해당 엔트리를 캐시에서 자동으로 비워준다.

> 백기선님 코드 참조

```java
public class CacheSample {
  public static void main(String[] args) {
    Object key1 = new Object();//strong reference - new를 통해 만든것은 strong reference 이다.
    Object value1 = new Object();
    
    Map<Object, Object> cache = new WeakHashMap<>();
    cache.put(key1, value1);
    //put 하는 경우 key가 weak reference로 삽입된다.
    //key1이라는 Object를 삭제하게 되면 cache에서 key1 은 GC의 대상이 된다.
  }
}
```

캐시를 만들 때 보통 엔트리의 유효기간을 정하는 일은 쉬운 일이 아니다. 때문에 시간이 지날수록 앤트리의 가치를 떨어뜨리는 방식을 흔히 사용한다.

1.  `ScheduledThreadPoolExecutor`같은 백그라운드 쓰레드를 활용
2. `LinkedHashMap`의 `removeEldestEntry`를 사용
3. 더 복잡한 방식은 추후 공부하기 `java.lang.ref`패키지를 직접활용??

- `WeakReference`에 대한 학습 필요

`Listener` 와 `callback`또한 메모리 누수의 주범이다.

클라이언트가 `callback`을 등록하고 해지하는 방법을 제공하지 않는다면, 계속 쌓이기만 할 것이다. 이것 역시 `WeakHashMap` 을 이용해서 해결 할 수 있다.



## 결론

메모리 누수의 주범

1. stack
2. cache
3. callback or listener