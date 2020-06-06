`REST API`란 단어를 자주 보게 되지만 무슨 내용인지 정확하게 알지 못해서 낭패를 보곤 했습니다. 더 낭패를 보기 전에 알아놓는 게 좋겠다 싶어서 무엇인지 조사하여 정리해 보았습니다.

---

## 1. API

`API`는 [위키피디아](https://ko.wikipedia.org/wiki/API)에선 다음과 같이 표현하고 있습니다.

> API(Application Programming Interface, 응용 프로그램 프로그래밍 인터페이스)는 응용 프로그램에서 사용할 수 있도록, 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻한다. 주로 파일 제어, 창 제어, 화상 처리, 문자 제어 등을 위한 인터페이스를 제공한다.

정리하면 `어떤 다른 프로그램이 사용할 수 있도록 제공하는 인터페이스`를 뜻합니다. `인터페이스`는 어떤 다른 사용자(프로그램이나 객체)가 자신이 제공하는 기능을 자세하게 알지 못해도 사용할 수 있도록 추상적으로 표현해 놓은 것입니다.


## 2. REST

[REST](https://ko.wikipedia.org/wiki/REST)는 다음 단어들의 준말입니다.

1. `RE`presentational
2. `S`tate
3. `T`ransfer

State는 '상태'고 Transfer는 '전송'이니까 `Representation한 상태 전송`을 의미합니다.

### 2.1. Representation (표현)

구글에 검색해본 결과, Respresentation이 무엇인지 [RFC7231](https://tools.ietf.org/html/rfc7231)에 나와있었습니다. 내용 중 일부를 발췌해보았습니다.

> For the purposes of HTTP, a "representation" is information that is intended to reflect a past, current, or desired state of a given resource, in a format that can be readily communicated via the protocol, and that consists of a set of representation metadata and a potentially unbounded stream of representation data.

> HTTP의 목적상, "표현"은 프로토콜을 통해 쉽게 통신할 수 있는 형식으로 주어진 자원의 과거, 현재 또는 원하는 상태를 반영하는 정보이며, 일련의 표현 메타데이터 및 잠재적으로 제한되지 않은 표현 데이터 스트림으로 구성된다. (구글번역 + 약간 수정)

위 글에 따르면 Representation, 즉 `표현`은 `프로토콜을 통해 쉽게 통신할 수 있는 형식을 가지고 주어진 자원(Resource)의 상태를 나타내는 정보`입니다. 


## 3. REST API

모두 정리하면 REST API란 `어떤 프로토콜을 이용하여 쉽게 통신 가능하도록 표현한 정보 상태를 전송하는 API`입니다. 따라서 이러한 정의만 따르고 있다면 모두 REST API가 될 수 있습니다.

> 필딩의 REST 아키텍처 형식을 따르면 HTTP나 WWW이 아닌 아주 커다란 소프트웨어 시스템을 설계하는 것도 가능하다. 또한, 리모트 프로시저 콜 대신에 간단한 XML과 HTTP 인터페이스를 이용해 설계하는 것도 가능하다. (REST, 위키백과)

그치만 저희가 만드는 대부분의 애플리케이션은 `HTTP 프로토콜`을 이용하겠죠?


## 4. REST API의 특징

REST API를 구현하기 위해선 다음과 같은 특징이 있다고 합니다.

1. 클라이언트-서버 구조
   - 클라이언트와 서버는 서로 `분리`되어 있어야 하고 `단일 인터페이스`를 사용해야 합니다.

2. 단일 인터페이스
   - 일관된 인터페이스를 사용하여 어떤 클라이언트라도 사용할 수 있어야 합니다.

3. 무상태 (Stateless)
   - 클라이언트가 보내는 요청이 이전에 보낸 요청과는 `독립적`이어야 합니다.

4. 캐시 처리 가능
   - HTTP를 이용하기 때문에 캐시 기능을 그대로 사용할 수 있습니다.

5. 계층화
   - 종단 간의 통신에 보안 계층, 프록시 등 다른 계층이 존재할 수 있습니다.

6. 자체 표현 구조
   - 메시지만 보고도 무슨 내용인지 이해할 수 있습니다.


## 5. REST API의 구현

REST API를 구현할 떄는 다음과 같은 규칙을 따릅니다.

-  자원(resource)의 위치는 `URI`로 나타낸다.
-  자원(resource)에 대한 행위는 `HTTP Method`로 나타낸다.
   -  GET(취득), POST(등록), PUT(수정), DELETE(삭제)

예를 들어, `유저 데이터(resource)`를 다룬다고 생각해보면 이렇게 표현할 수 있습니다.

1. 전 유저 정보 취득 - 유저(users)를 취득(GET)
    ```
    GET /users
    ```

2. 특정 유저 정보 취득 - 유저(users) 한 명(id)를 취득(GET)
    ```
    GET /users/{id}
    ```

3. 특정 유저 정보 등록 - 유저(users) 한 명(id)를 등록(POST)
    ```
    POST /users/{id}

    { "name": "홍길동", "age": 30 }
    ```

4. 특정 유저 정보 수정 - 유저(users) 한 명(id)를 수정(PUT)
    ```
    PUT /users/{id}

    { "name": "김길동", "age": 40 }
    ```

5. 특정 유저 정보 삭제 - 유저(users) 한 명(id)를 삭제(DELETE)
    ```
    DELETE /users/{id}
    ```


## 6. 참고
- https://ko.wikipedia.org/wiki/API
- https://ko.wikipedia.org/wiki/REST
- https://tools.ietf.org/html/rfc7231
- https://meetup.toast.com/posts/92
