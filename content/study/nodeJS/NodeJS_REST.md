---
title: "NodeJS - REST"
date: 2021-10-26T22:00:00+09:00
categories: ["study"]
tags: ["NodeJS"]
cover: ""
---
## REST
`REST`란 REpresentational State Transfer의 줄임말로, 서버의 자원을 정의하고 자원에 대한 주소를 지정하는 방법이다. 통신 방식 중 하나로 일종의 약속이며, 구체적으로는 `HTTP URI`를 통해 자원을 명시하고 `HTTP Method`를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것을 의미한다. 즉, 어떤 자원에 대한 CRUD Operation 수행 요청을 URI에 보내는 것.

> URI(Uniform Resource Identifier)  
> '통합 자원 식별자'. 인터넷 상에서의 각종 정보들의 고유한 식별자이다. URI의 사전에 정의된 규칙에 따라 자원의 위치를 표시한 것을 URL, 이름을 표시한 것을 URN이라고 한다.

### 제한 조건 / 특징
- Client-Server : 일관적인 인터페이스로 클라이언트와 서버의 명확한 분리.
- Stateless : 각 요청 간 클라이언트의 상태 정보가 서버에 저장되어서는 안됨.
- Cacheable : 응답 내 데이터에 캐시 가능 여부를 표시. 
- Uniform Interface : URI로 지정된 리소스에 균일하고 통일된 인터페이스를 제공해야 함. `HTTP 프로토콜`에 따르는 모든 플랫폼에서 접근이 가능함.
- Layered System : 중개 서버 등을 이용하여 확장성있는 시스템을 구성할 수 있으며 클라이언트는 이를 알 수 없음.
- Code-On-Demand : 서버 측에서 클라이언트에 실행 가능한 로직을 전송하여 일시적으로 기능을 확장할 수 있음.

## REST API
위 `REST`를 준수하여 설계한 아키텍쳐를 `RESTful`하다라고 표현하며, `REST`를 준수하는 API를 `RESTful API` 또는 `REST API`라고 한다. `REST API`는 `REST`를 준수하기 때문에 `URI`를 통해 리소스를 표현하고 `HTTP Method`를 통해 요청하게 된다.

### 설계 가이드

[RESTful API 설계가이드](https://sanghaklee.tistory.com/57) by 이상학의 개발 블로그

### 장단점
1. 장점
   - 자원의 효율성
   - 표준 `HTTP` 사용으로 인한 간단함
   - 확장성
   - 서버와 클라이언트의 분리

2. 단점
   - 표준의 부재
   - 이슈에 대한 정보 파악의 어려움
   - 클라이언트의 지원 여부와 호환

