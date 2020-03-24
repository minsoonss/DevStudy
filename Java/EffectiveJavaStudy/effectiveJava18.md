# 상속보다는 컴포지션을 사용하라

상속은 코드를 재사용하는 강력한 수단이지만, 항상 최선은 아니다. 잘못사용하면 오류를 내기 쉬운 소프트웨어를 만들게 된다.

**메서드 호출과 달리 상속은 캡슐화를 깨뜨린다.** 상위클래스가 어떻게 구현되느냐에 따라 하위클래스의 동작에 문제가 생길 수 있다.

```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;
    
    public InstrumentedHashSet(){}
    
    public InstrumentedHashSet(int initCap, float loadFactor){
    	super(initCap, loadFactor);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}

InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
s.addAll(List.of("틱", "탁탁", "펑"));
```

위의 예시는 오류를 발생시킨다.

`addAll` 호출 시 3이 오를거라 생각하지만 `addAll`은 결국 하위 클래스의 `add`를 호출하게되어 3+1+1+1이 되어 6이된다.

이러한 문제발생을 줄이기 위해 **상속 대신 컴포지션을 사용하자.**

### 컴포지션

새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 함

기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻

**Forwarding** : 새클래스의 인스턴스 메서드들은 기존 클래스의 대응하는 메서드를 호출해 그 결과를 반환

```java
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;
    public ForwardingSet(Set<E> s) { this.s = s; }

    public void clear()               { s.clear();            }
    public boolean contains(Object o) { return s.contains(o); }
    public boolean isEmpty()          { return s.isEmpty();   }
    public int size()                 { return s.size();      }
    public Iterator<E> iterator()     { return s.iterator();  }
    public boolean add(E e)           { return s.add(e);      }
    public boolean remove(Object o)   { return s.remove(o);   }
    public boolean containsAll(Collection<?> c)
                                   { return s.containsAll(c); }
    public boolean addAll(Collection<? extends E> c)
                                   { return s.addAll(c);      }
    public boolean removeAll(Collection<?> c)
                                   { return s.removeAll(c);   }
    public boolean retainAll(Collection<?> c)
                                   { return s.retainAll(c);   }
    public Object[] toArray()          { return s.toArray();  }
    public <T> T[] toArray(T[] a)      { return s.toArray(a); }
    @Override public boolean equals(Object o)
                                       { return s.equals(o);  }
    @Override public int hashCode()    { return s.hashCode(); }
    @Override public String toString() { return s.toString(); }
}
```

```java
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    public int getAddCount() {
        return addCount;
    }
}
```

위 코드를 사용하게되면 다른 Set 인스턴스를 감싸고 있다는 뜻에서 InstrumentedSet 같은 클래스를 `래퍼 클래스` 라 하며, 다른 Set에 계측 기능을 덧씌운다는 뜻에서 `데코레이션 패턴(Decoration pattern)` 이라 함

컴포지션과 전달의 조합은 넓은 의미로 `위임(Delegation)` 이라 부름

`래퍼 클래스`는 단점이 거의 없다. 한 가지, 래퍼 클래스가 콜백(callback) 프레임워크와 어울리지 않는다는 점만 주의하면 된다. 콜백 프레임워크에서는 자기 자신의 참조를 다른 객체에 넘겨서 다음 호출 때 사용하도록 한다.  내부 객체는 자신을 감싸고 있는 래퍼의 존재를 모르니 대신 자신(this)의 참조를 넘기고, 콜백 때는 래퍼가 아닌 내부 객체를 호출하게 된다.

### 상속 하는 경우

- 반드시 하위 클래스가 상위 클래스의 '진짜' 하위 타입인 상황

### 상속할 때 자문

1. API에 아무 결함이 없는가?
2. 결함이 있다면, 이 결함이 클래스의 API까지 전파돼도 괜찮은가?

**컴포지션은 상위 클래스의 결함을 숨길 수 있다. 상속은 그렇지 못하다**