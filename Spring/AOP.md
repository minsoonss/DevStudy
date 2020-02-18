# AOP

**A(spect) O(riented) P(rogramming)** - 관점 지향 프로그래밍 => OOP를 보완하는 수단

어떠한 관점을 기준으로 모듈화 => 공통된 로직을 묶겠다?? ex) 데이터베이스 연결과 커밋 혹은 롤백(@Transactional)

#### 주요 개념

- Aspect - 흩어진 관심사(CrossCutting concerns)를 모듈화 한 것
- Target - Aspect를 적용하는 위치(클래스, 메소드 등)
- Advice - 어떤 행위를 해야하는가에 대한 것
- Join point - Advice가 끼어들 지점(메소드 실행시점?)
- PointCut - Join point의 상세 내용

#### AOP Implementations

- 자바
  - AspectJ
  - Spring AOP

#### AOP 적용 방법

- 컴파일 - Java file을 Class file로 만들때
  - 별도의 컴파일을 거쳐야함
- 로드 타임 - Class file을 로딩할 때 =>  Load Time Weaving
  - 클래스 로딩시 부하
  -  load time weaver를 설정 해줘야함
- 런타임 - Spring 에서 Bean을 만들 때 ProxyBean을 만들어 처리
  - 제일 많이 쓰임
  - 주로 Spring AOP를 사용할 때 쓰는 방법
  - 최초 Bean을 만들때 비용 발생