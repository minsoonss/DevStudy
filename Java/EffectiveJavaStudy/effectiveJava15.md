# 클래스와 멤버의 접근 권한을 최소화하라

어설플게 설계된 컴포넌트와 잘 설계된 컴포넌트의 가장 큰 차이는 바로 클래스 내부 데이터와 내부 구현 정보를 외부 컴포넌트로부터 얼마나 잘 숨겼느냐다 (정보은닉 캡슐화)

-> 오직 API를 통해서만 다른 컴포넌트와 소통하며 서로의 내부 동작에는 전혀 개의치 않음

정보 은닉의 장점

- 시스템 개발 속도를 높임 -> 각 컴포넌트를 병렬로 개발할 수 있기 때문
- 시스템 관리 비용을 낮춤 -> 각 컴포넌트를 더 빨리 파악하여 디버깅할 수 있고, 다른 컴포넌트로 교체하는 부담도 적기 때문
- 정보 은닉 자체가 성능을 높여준다? X -> 성능 최적화에 도움을 준다. -> 완성된 시스템을 프로파일링해 최적화할 컴포넌트를 정한 다음, 다음 컴포넌트에 영향을 주지 않고 해당 컴포넌트만 최적화 가능
- 소프트웨어 재사용성 높임 -> 외부에 거의 의존하지 않고 독자적으로 동작할 수 있는 컴포넌트라면 그 컴포넌트와 함께 개발되지 않은 낯선 환경에서도 유용하게 쓰일 가능성 높음
- 큰 시스템을 제작하는 난이도를 낮춰줌 -> 시스템 전체가 아직 완성되지 않은 상태에서도 개별 컴포넌트의 동작 검증 가능

### 기본원칙

**모든 클래스와 멤버의 접근성을 가능한 한 좁힌다.**

소프트웨어의 동작에 문제가 없는선에서 가장 낮은 접근 수준 부여

### 접근지정자의 종류

- private
  - 멤버를 선언한 톱 레벨 클래스에서만 접근 가능
- package-private
  - 소속된 패키지내에서 모두 접근 가능
- protected
  - package-private을 포함하며 상속받은 하위 클래스에서 접근 가능
- public
  - 모든 곳에서 접근가능



클래스의 공개 API를 세심히 설계한 후 그 외의 모든 멤버는 private으로 만들어야함

그 후 같은 패키지의 다른 클래스가 접근해야 하는 멤버에 한하여 package-private으로 풀어줌

**public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다**

​	final이 아닌 인스턴스 필드를 public으로 선언하면 그 필드에 담을 수 있는 값을 제한할 힘을 잃게됨

**public 가변 필드를 갖는 클래스는 일반적으로 스레드 안전하지 않다.**

​	필드가 수정될 때(락 획득 등) 다른 작업을 할 수 없게 됨

**클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안 됨**

​	길이가 0이 아닌 배열은 모두 변경 가능

```java
public static final Thing[] VALUES = {...};
```

위 코드는 보안 허점이 있다.

해결 방법

1. public 을 private으로 변경 후 public 불변 리스트를 추가하는 것

```java
private static final Thing[] PRIVATE_VALUES={...};
public static final List<Thing> VALUES = 
    Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```

2. 배열을 private으로 만들고 그 복사본을 반환하는 public 메서드 추가(방어적 복사)

```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final Thing[] values() {
    return PRIVATE_VALUES.clone();
}
```

