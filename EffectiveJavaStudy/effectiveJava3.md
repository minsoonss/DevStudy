# private 생성자 또는 enum 타입을 사용해서 싱글톤으로 만들 것
싱글톤이란?  인스턴스를 오직 하나만 생성할 수 있는 객체

ex) 함수와 같은 무상태 객체, 설계상 유일해야하는 시스템 컴포넌트

## 싱글톤 만드는 방법 1
```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    
    private Elvis() {...}
    public void leaveTheBuilding(){...}
}
```
- private 생성자는 public static final 필드인 INSTANCE를 초기화할 때 한번만 호출
- 예외 - reflection을 통한 AccessibleObject.setAccessible을 사용해 객체 호출
```java
Constructor<Elvis> constructor = Elvis.class.getConstructor();
constructor.setAccessible(true);
//대강 요런 느낌
```
## 싱글톤 만드는 방법 2
```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    
    private Elvis() {...}
    public Elvis getInstance() {
        return INSTANCE;
    }
    public void leaveTheBuilding(){...}
}
```
- Elvis.getInstance 는 항상 같은 객체를 반환
- 방법 1 의 장점
    - 해당 클래스가 싱글톤임이 API에 명백히 드러남
    - 간결함
- 방법 2의 장점
    - API를 바꾸지 않고 언제든 싱글톤이 아니게 바꿀 수 있음
    - 정적 팩토리를 `제네릭 싱글톤 팩토리`로(아이템 30) 만들 수 있음
    - static 팩토리 메소드를 `Supplier<Elvis>`에 대한 `메소드 레퍼런스`로 사용할 수도 있다.
        ```java
          Supplier<Elvis> supplier = Elvis.getInstance();
        ```
**방법1과 방법2를 사용하여 만들때 직렬화(Serialization)의 문제와 직면하게 된다**
- readResolve() 메소드 선언하여 해결
```java
private Object readResolve() {
    return INSTANCE;
}
```
## 싱글톤 만드는 방법 3 - 비추천
- enum 타입으로 선언
- 제일 바람직하긴 하지만... 이렇게까지... 너무 부자연스럽다
- Enum외의 다른클래스를 상속해야한다면 불가능한 방법
    