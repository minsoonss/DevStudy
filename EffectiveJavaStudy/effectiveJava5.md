# 자원을 직접 명시하지말고 의존 객체 주입을 사용하

많은 클래스들이 하나 이상의 자원에 의존한다.
사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글톤 클래스는 적합하지 않다.

## 비추천 방법1
```java
public class SpellChekcer {
    private static final Lexicon dictionary = ...;
    
    private SpellChekcer() {}
}
```
## 비추천 방법2
```java
public class SpellChekcer {
    private static final Lexicon dictionary = ...;
    
    private SpellChekcer(...) {}
    public static final SpellChekcer INSTANCE = new SpellChekcer(...);
}
```
위의 두 방법은 사전을 단 한종류만 쓸 수 있다.

## 추천 - 의존 주입
인스턴스를 생성할 때 필요한 자원을 생성자의 파라미터로 넘겨주는 방식을 사용하면 유연하게 해결 가능
```java
public class SpellChekcer {
    private final Lexicon dictionary;
    
    public class SpellChecker (Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }   
}
```

위 패턴의 쓸만한 변형으로는 생성자에 자원 팩토리를 넘겨주는 방식이 있다. <br/>
팩토리란 호출할 때 마다 특정타입의 인스턴스를 반복해서 만들어주는 객체를 말한다. <br/>
즉 팩토리 메소드 패턴을 구현한 것이다.<br />
자바 8에서 소개한 Supplier<T>가 팩토리를 표현한 완벽한 예다. <br />
Supplier<T> 를 입력으로 받는 메소드는 일반적으로 `한정적 와일드 카드(아이템 31)` 타입을 이용해 매개변수를 제한해야 한다. <br />

```java
Mosaic create(Supplier<? extends Tile> tileFactory) {...}
```

의존 객체 주입이 유연성과 테스트를 용이하게 하지만 의존성이 수천개나 되는 프로젝트에서는 어지럽다
이런 어지러움을 `Spring Framework`와 같은 프레임웍이 해소해준다.
