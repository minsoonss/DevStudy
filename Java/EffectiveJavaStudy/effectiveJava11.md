# equals 를 재정의 하면 hashcode도 반드시 재정의 하라

equals를 재정의하면 hashcode도 무조건 재정의 해야한다

-> HashMap, HashSet 과 같은 클래스 사용시 문제 발생

Object 명세의 일반규약 중 세가지

> - equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 hashCode 메서드는 몇 번을 호출해도 일관되게 항상 같은 값을 반환해야 한다. 
>
>   단, 애플리케이션을 다시 실행한다면 이 값이 달라져도 상관없다.
>
> - equals(Object)가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 한다.
>
> - equals(Object)가 두 객체를 다르다고 판단했더라도, 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없다. 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.

재정의 하지 않을 시 두번째 규약을 어기게 된다.

ex) 

```java
Map<PhoneNumber, String> m = new HashMap<>();
m.put(new PhoneNumber(707, 867, 5309), "Jenny");

String s = m.get(new PhoneNumber(707, 867, 5309));
System.out.println(s);
```

위의 예제는 null을 반환하게 된다 (PhoneNumber 클래스는 아이템 10에서 정의 - hashcode 재정의 하지않음)

책에서는 hashcode를 작성하는 방법을 제시한다

```java
@Override 
public int hashCode() { 
    int result = Short.hashCode(areaCode); 
    result = 31 * result + Short.hashCode(prefix); 
    result = 31 * result + Short.hashCode(lineNum); 
    return result; 
}
// 여기서 31을 곱하는 이유? primeNumber, oddNumber 라서? 전통적으로 그래왔다함
// 17도 가능하지 않을까?
```

Objects에서도 방법을 제공(java 7 부터)

```java
@Override 
public int hashCode() { 
    return Objects.hash(lineNum, prefix, areaCode); 
}
```

**성능을 높인답시고 해시코드를 계산할 때 핵심 필드를 생략해서는 안 된다.**

**hashcode가 반환하는 값의 생성 규칙을 API 사용자에게 자세히 공표하지 말자. 그래야 클라이언트가 이 값에 의지하지 않게 되고, 추후에 계산 방식을 바꿀 수도 있다.** String, Integer 를 포함한 자바 라이브러리의 많은 클래스에서 hashCode 메서드가 반환하는 정확한 값을 알려준다. 바람직하지 않은 실수지만 바로잡기에는 이미 늦었다.