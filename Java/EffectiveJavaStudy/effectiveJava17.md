# 변경 가능성을 최소화하라

### 불변 클래스

그 인스턴스 내부 값을 수정할 수 없는 클래스 ex) `String`, `BigInteger`, `BigDecimal`

불변 클래스는 가변 클래스보다 설계와 구현이 쉬우며, 오류가 생길 여지도 적고 안전함

#### 불변클래스로 만드는 규칙

- 객체의 상태를 변경하는 메서드를 제공하지 않음
- 클래스를 확장할 수 없도록 함
  - 생성자를 private으로
  - 혹은 final로 선언
- 모든 필드를 final로 선언
- 모든 필드를 private으로 선언
- 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 함

```java
//예시
public final class Complex{
     private final double re;
     private final double rm;
     public Complex(double re, double im){
          this.re = re;
          this.im = im;
     }
     public double realPart(){ return re; }
     public double imagiaryPart() { return im; }

     public Complexd add(Complex c) {
          return new Complex(re + c.re, im + c.im);
     }
     ...
}
```

위 코드를 살펴보면 add 메서드는 자신을 수정하는 것이 아닌 더 한 결과를 새로운 객체를 만들어 반환함

#### 불변 객체의 장단점

- 장점
  - 불변 객체는 단순함
  - 불볍 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요 없음
  - 불변 객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부데이터를 공유할 수 있음
  - 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많음
    - 값이 바뀌지 않는 구성요소들로 이뤄진 객체라면 그 구조가 아무리 복잡하더라도 불변식을 유지하기 훨씬 수월
    - ex) 불변 객체는 맵의 키와 집합(Set)의 원소로 쓰기 적합
  - 불변 객체는 그 자체로 실패 원자성('메서드에서 예외가 발생한 후에도 그객체는 여전이 유효한 상태여야 한다'는 성질) 제공
- 단점
  - 값이 다르면 반드시 독립된 객체로 만들어야 함 -> 생성비용 높음
- 단점 해결책
  - package-private으로 가변 동반 클래스를 만듬(String - StringBuilder)

#### 불변 클래스를 만드는 또 다른 설계법

상속 불가한 클래스로 만들어야함

1. 클래스를 final로 선언하는 방법

2. 모든 생성자를 private, package-private으로 만들고 public 정적 팩터리를 제공하는 방법(훨씬 유연함)

   ```java
   private(double re, double im){
        ...
   }
   public static Complex valueOf(double re, double im){
        return new Complex(re, im);
   }
   ```

   BigInteger, BigDecimal 은 final 로선언이 안되서 상속이 가능하도록 잘못 선언되어있다. 따라서 안전하게 변경 불가능한 객체로 사용하려면 상속되어있는 객체인지 확인해야 한다.

   ```java
   public static BigInteger safeInstance(BigInteger val){
        if (val.getClass() != BigInteger.class)
             // val 이 상속이 되어 잘못사용되어진 객체라면
             return new BigInteger(val.toByteArray());
        reutrn val;
   }
   ```

3.  초반에 적어 놓은 내용에 따르면 '모든 필드가 `final` 이고 어떤 메서드도 그 객체를 수정할 수 없어야한다' 고 돼있음

   성능을 위해 완화 가능

   ​	-> 어떤 메서드도 객체의 상태 중 외부에 비치는 값을 변경할 수 없다.

   ​		-> 어떤 불변 클래스는 계산 비용이 큰 값을 나중에 계산하여 final이 아닌 필드에 캐시 해놓기도 한다.

   ​		-> ex) hashCode **지연 초기화 기법**