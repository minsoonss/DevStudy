# 불필요한 객체를 만들지 말자

기능적으로 동일한 객체를 새로만드는 대신 객체 하나를 재사용하는 것이 적절하다.
재사용시 더 빠르고 스타일리쉬하다. 'immutable Object'는 항상 재사용할 수 있다.

## 문자열 객체 생성

자바의 문자열, String을 new로 생성하면 항상 새로운 객체를 만들게 된다. 아래와 같이 String 객체를 생성하는 것이 올바른 방법이다.
```java
String s = "bikini";
```
문자열 리터럴을 재사용하기 때문에 해당 자바 가상 머신에 동일한 문자열 리터럴이 존재한다면 그 리터럴을 재사용한다.
ex)
```java
String name = "soonmin";
String name2 = "soonmin";

System.out.println(name == name2);
//true
```

## static 팩토리 메소드 사용하기

자바 9에서 deprecated 된 Boolean.valueOf(String) 같은 static 팩토리 메소드를 사용할 수 있다.
생성자는 반드시 새로운 객체를 만들어야 하지만 팩토리 메소드는 그렇지 않다.

## 무거운 객체

만드는데 메모리나 시간이 많이 드는 객체(비싼 객체)를 반복적으로 만들어야 한다면 캐시해두고 재사용할 수 있는지 고려하는 것이 좋다.
ex) 문자열이 로마 숫자를 표현하는지 확인하는 정규식 코드
```java
static boolean isRomanNumeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
```
But `String.matches`는 내부적으로 `Pattern`객체를 만들어 쓰는데 그 객체를 만들려면 정규 표현식으로 유한 상태 기계로 컴파일 하는 과정이 필요하다 -> 비싼객체!!!

성능을 개선하려면 `Pattern` 객체를 미리 만든다.
```java
public class RomanNumber {

    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

    static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }

}
```
위 코드의 문제점은 만약 `isRomanNumeral` 을 사용하지 않는다면 `ROMAN`이라는 패턴을 필요없이 만든셈이 된다.

## 어댑터
불변 객체인 경우에 안정하게 재사용하는 것이 매우 명확하다. 하지만 몇몇 경우에 분명하지 않은 경우가 있다. 오히려 반대로 보이기도 한다.
어댑터를 예로 들면, 어댑터는 인터페이스를 통해서 뒤에 있는 객체로 연결해주는 객체라 여러개 만들 필요가 없다.

`Map` 인터페이스가 제공하는 `keySet`은 `Map`이 뒤에 있는 `Set` 인터페이스의 뷰를 제공한다. `keySet`을 호출할 때마다 새로운 객체가 나올거 같지만 사실 같은 객체를 리턴하기 때문에 리턴 받은 `Set` 타입의 객체를 변경하면, 결국에 그 뒤에 있는 `Map` 객체를 변경하게 된다.
ex) 참고 백기선님 github
```java
public class UsingKeySet {

    public static void main(String[] args) {
        Map<String, Integer> menu = new HashMap<>();
        menu.put("Burger", 8);
        menu.put("Pizza", 9);

        Set<String> names1 = menu.keySet();
        Set<String> names2 = menu.keySet();

        names1.remove("Burger");
        System.out.println(names2.size()); // 1
        System.out.println(menu.size()); // 1
    }
}
``` 

## 오토박싱

불필요한 객체를 생성하는 또 다른 방법으로 오토박싱이 있다.
오토박싱은 프로그래머가 프리미티브 타입과 박스 타입을 섞어 쓸 수 있게 해주고 박싱과 언박싱을 자동으로 해준다.

**오토박싱은 프리미티브 타입과 박스 타입의 경계가 안보이게 해주지만 그렇다고 그 경계를 애주진 않는다.**
```java
public class AutoBoxingExample {

    public static void main(String[] args) {
        long start = System.currentTimeMillis();
        Long sum = 0l;
        for (long i = 0 ; i <= Integer.MAX_VALUE ; i++) {
            sum += i;
        }
        System.out.println(sum);
        System.out.println(System.currentTimeMillis() - start);
    }
}
```
**불필요한 오토박싱을 피하려면 박스 타입 보다는 프리미티브 타입을 사용해야 한다.**
#그렇다고 객체 만드는 것을 너무 기피하지 마라