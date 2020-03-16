# equals는 규약을 지켜 재정의 하라

### equals는 함정이 많다 → 웬만하면 재정의하여 사용하지마라

아래에 나열하는 네가지 상황중 하나에 해당된다면 재정의 하지 말것

- 각 인스턴스가 본질적으로 고유하다.

  - 값을 표현하는게 아닌 동작하는 개체를 표현하는 클래스 ex)Thread

- 인스턴스의 논리적 동치성을 검사할 일이 없다.

- 상위클래스에서 재정의한 `equals`가 하위 클래스에서 맞다

  Ex)Map→AbstractMap , Set→AbstractSet, List→AbstractList

- 클래스가 private, package-private 이며 `equals`를 호출할 일이 없다.

  만약 예외를 철저히 피하고싶다면?

      @Override public boolean equals(Object o){
      	throw new AssertionException(); //호출 금지
      }  

### 재정의해야 할 상황?

객체 식별성(object identity;두 객체가 물리적으로 같은가)이 아니라 논리적 동치성을 확인해야 하는데, 상위 클래스의 equals가 논리적으로 동치성을 비교하도록 재정의되지 않았을 때 → 주로 Integer, String과 같은 값 클래스가 여기 해당

두 값 객체를 equals로 비교하는 프로그래머는 객체가 같은지 알고 싶은 것이 아니라 값이 같은지를 알고 싶어 할 것이다.

또한 값 클래스라 해도, 값이 같은 인스턴스가 둘 이상 만들어지지 않음을 보장하는 인스턴스 통제 클래스라면 equals를 재정의하지 않아도 된다. Enum 또한 여기에 해당함 → 어차피 같은 객체가 둘 이상 만들어 지지 않으니 논리적 동치성까지 같이 확인해 준다.

#### 재정의 해야 한다면 일반 규약을 따라야 한다.

equals 메서드는 동치관계를 구현하며, 다음을 만족한다.

- 반사성 : null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true다.
- 대칭성 : null이 아닌 모든 참조 값 x, y에 대해 , x.equals(y)가 true면 y.equals(x)도 true다.
- 추이성 : null이 아닌 모든 참조 값 x, y, z에 대해, x.equals(y)가  true고 y.equal(z)도 true면 x.equals(z)도 true다.
- 일관성 : null이 아닌 모든 참조 값 x, y 에 대해, x.eqauls(y)를 반복해서 호출하면 항상 true나 false를 일관적으로 반환한다.
- null-아님 : null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false다.

### 좋은 equals 메소드 구현 방법

1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인

   자기 자신이면 true를 반환 

   단순한 성능 최적화 용도

2. **instanceof** 연산자로 입력이 올바른 타입인지 확인한다.

   올바른 타입은 equals가 정의된 클래스인 것이 보통이지만, 가끔은 그 클래스가 구현한 특정 인터페이스가 될 수도 있다.

   어떤 인터페이스는 자신을 구현한 다른 클래스끼리도 비교할 수 있도록 equals 규약을 수정하기도 한다.

   Ex)Map, Set, List, Map.Entry

3. 입력을 올바른 타입으로 형변환한다. 앞서 2번에서 instanceof 검사를 했기 때문에 이 단계는 100% 성공한다.

4. 입력 객체와 자기 자신의 대응되는 **핵심** 필드들이 모두 일치하는지 하나씩 검사한다.

   모든 필드가 일치하면 true, 하나라도 다르면 false를 반환한다.

### 마지막 주의 사항

- equals를 재정의할 땐 hashCode도 반드시 재정의한다.
- 너무 복잡하게 해결하려 들지 말자 → 너무 어렵게 파고들다가 문제를 발생시킬 수 있다.
- Object 외의 타입을 매개변수로 받는 equals를 작성하지 마라.