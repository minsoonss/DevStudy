# Spring VS SpringBoot

### Spring

- 스프링은 왜 스프링인가? - 스프링이 생기면서 개발자들의 겨울이 끝났다!
- dependency가 매우 길다
- webconfig 에서 설정하여 configuration 또한 매우 길다

### Spring Boot

- dependency가 짧아짐
- dependency 버전관리도 권장버전으로 설정
- configuration 도 properties 나 yml 파일을 이용하여 더 간단하고 짧다
- embedded server
  - tomcat이 싫을 경우 jetty와 같은 서버를 추가하면 된다.
  - 내장 서블릿 컨테이너 덕분에 jar 파일로 간단 배포