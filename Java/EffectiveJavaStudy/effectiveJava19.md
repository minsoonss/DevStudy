# 상속을 고려해 설계하고 문서화 하라. 그러지 않았다면 상속을 금지하라

상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지(자기사용) 문서로 남겨야 한다

- 재정의 가능 메서드라면 그 사실을 호출하는 메서드의 API 설명에 적시
- 어떤 순서로 호출하는지
- 각각의 호출 결과가 이어지는 처리에 어떤 영향을 주는지

API 문서의 메서드 설명 끝에서 "Implementation Requirements"로 시작하는 절 - 메서드의 내부 동작 방식 설명



### 상속용 클래스 테스트

직접 하위 클래스를 만들어보는 것이 '유일'하다.

꼭 필요한 `protected` 멤버를 놓쳤다면 하위 클래스를 작성할 때 그 빈자리가 확연히 드러난다.

거꾸로, 하위 클래스를 여러 개 만들 때까지 전혀 쓰지지 않는 `protected` 멤버는 사실 `private` 이었어야 할 확률이 크다.



### 상속을 허용하는 클래스의 지켜야 할 제약

1. 상속용 클래스의 생성자는 직접적으로든 간접적으로든 재정의 가능 메서드를 호출하면 안됨

   ```java
   public class Super {
       //잘못된 예
       public Super() {
           overrideMe();
       }
       
       public void overrideMe(){
       }
   }
   ```

   ```java
   public final class Sub extends Super {
       private final Instant instant;
   
       Sub() {
           instant = Instant.now();
       }
   
       @Override public void overrideMe() {
           System.out.println(instant);
       }
   }
   
   Sub sub = new Sub();
   sub.overrideMe();
   ```

2. private, final, static 메서드는 재정의가 불가능하니 생성자에서 안심하고 호출

3. clone과 readObject 모두 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안 됨 (생성자와 비슷한 효과를 낸다.)

4. Serializable을 구현할 때 readResolve나 writeReplace 메서드는 private이 아닌 protected로 선언

   private로 선언한다면 하위 클래스에서 무시되어 문제 발생



### 상속용이 아니라면 상속을 금지

금지 방법

1. 클래스를 final로 선언

2. 모든 생성자를 private, package-private으로 선언

   public static factory 구현