# 생성자 대신 static 팩토리 메소드를 고려해 볼 것

## 장점 1 : 이름을 가질 수 있다.
static 팩토리 메소드를 이용할 경우 반환되는 객체에 대한 이해가 쉽다.

ex) BigInteger.probablePrime - 소수인 BigInteger를 반환 
```java    
    public static Foo withName(String name) {
        Foo foo = new Foo();
        foo.setName(name);
        return foo;
    }
```

## 장점 2 : 반드시 새로운 객체를 만들 필요가 없다.
불변(immutable) 클래스(아이템 17)인 경우나 매번 새로운 객체를 만들 필요가 없는 경우에 미리 만들어둔 인스턴스 또는 캐시해둔 인스턴스를 반환할 수 있다. `Boolean.valueOf(boolean)` 메소드는 아예 객체를 생성하지 않는다.

## 장점 3 : 리턴 타입의 하위 타입 인스턴스를 만들 수도 있다.
클래스에서 만들어 줄 객체의 클래스를 선택하는 유연함이 있다. ?인터페이스 지향?
`java.util.Collections`가 좋은 예

## 장점 4 : 리턴하는 객체의 클래스가 입력 매개변수에 따라 매번 다를 수 있다.
`EnumSet` 클래스는 public static 메소드 `allOf()`, `of()` 등을 제공하지만 enum 타입의 개수에 따라 `RegularEnumSet` 또는 `JumboEnumSet`으로 달라진다.

## 장점 5 : 리턴하는 객체의 클래스가 public static 팩토리 메소드를 작성할 시점에 반드시 존재하지 않아도 된다.
장점 3, 4와 비슷한 개념, 유연성!!
`서비스 프로바이더` 프레임워크의 근본, 책에서는 `JDBC`를 예로 든다.

`의존성 주입(Dependency Injection)`과 비슷하다고 생각하면 될듯하다.

## 단점 1 : public 또는 protected 생성자 없이 static public 메소드만 제공하는 클래스는 상속할 수 없다.
딱하 단점이라 할 수 없다.
`Collections`를 상속할 필요가 있는건가?

## 단점 2 : 프로그래머가 static 팩토리 메소드를 찾는게 어렵다.
생성자는 Javadoc 상단에 모아서 보여주지만 static 팩토리 메소드는 특별히 다뤄주지 않는다.

예외) `SpringApplication.run()`

우리가 개발할때 Javadoc에서 다뤄주면 되는것 아닌가??

## 참고
- https://github.com/keesun/study/blob/master/effective-java/item1.md