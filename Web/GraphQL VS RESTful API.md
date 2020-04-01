# GraphQL VS RESTful API

GraphQL - facebook에서 만든 Query Language

REST - REpresentational State Transfer

### GraphQL 의 탄생 배경

- RESTful API로는 다양한 기종에서 필요한 정보들을 일일히 구현하는 것이 힘듬
- ex) ios, android 에서 필요한 정보들이 조금씩 다름 해당 부분 별로 api를 구현하는 것이 힘듬

### GraphQL 과 RESTful의 차이

1. Endpoint 차이
    - GraphQL - 1개의 endpoint
    - Rest API - N개의 endpoint
2. 응답의 구조
    - GraphQL - 요청시 쿼리문에 따라 응답의 구조가 바뀜(endpoint 동일 가능성 높음)
    - Rest API - endpoint 마다 응답의 구조가 바뀜, 같은 endpoint 에서는 같은 구조로 응답이 옴

### GraphQL의 장점

1. HTTP 요청의 횟수를 줄일 수 있다.

    원하는 정보를 하나의 Query에 모두 담아 요청하는 것이 가능

2. HTTP 응답의 크기를 줄일 수 있다.

    필요한 부분만 부분적으로 요청 가능

### GraphQL의 단점

1. File 전송 등 Text 만으로 하기 힘든 내용들을 처리하기 복잡하다.
2. 고정된 요청과 응답만 필요할 경우 요청의 크기가 커진다.

### GraphQL VS RESTful

- GraphQL
    - 서로 다른 모양의 다양한 요청들에 대해 응답할 수 있어야 할 때
    - 대부분의 요청이 CRUD에 해당할 때
- RESTful
    - HTTP와 HTTPS 에 의한 Caching을 잘 사용하고 싶을 때
    - File 전송 등 단순한 Text로 처리되지 않는 요청이 있을 때
    - 요청의 구조가 정해져 있을 때

참고 - [https://www.holaxprogramming.com/2018/01/20/graphql-vs-restful-api/](https://www.holaxprogramming.com/2018/01/20/graphql-vs-restful-api/)