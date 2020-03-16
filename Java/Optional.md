# Optional

어떤 문서를 읽다가 프로그래밍 언어들이 `null`을 없애는 방향으로 나아간다(?)는 말을 들은적 있다. 학교다닐때 ‘null과 0 이 다르다.’ 는 사실로 친구들과 지식교환을 했었던 기억이 있지만 null 을 없애다니...

그럼 `null`을 없애는것에 대해 java도 뭔갈 하지않을까? 라는 의문으로 검색 했더니 `Optional`을 찾을 수 있었다.

Java 8 에서 `Optional`을 사용할 수 있다.

아래 참고페이지에서 보면 `null`을 창시한 사람은 `null`을 만든게 가장 큰 실수라 했다.

`null`을 통해 느낄 수 있는 가장 큰 문제점

1. 런타임에 NullPointerException 예외 발생
2. NPE(NullPointerException) 방어를 위해서 들어간 `null` 체크 로직으로 가독성과 유지 보수성 감소

### Optional ???

`java.util.Optional<T>` 은 함수형 언어의 접근에서 영감을 받아 java 8 에서 도입

`Optional`은 존재할 수도 있지만 안 할 수도 있는 객체

- 참고 [https://www.daleseo.com/java8-optional-befor](https://www.daleseo.com/java8-optional-before/)e/