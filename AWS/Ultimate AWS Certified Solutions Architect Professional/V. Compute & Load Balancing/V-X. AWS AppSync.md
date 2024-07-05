# **I. AWS AppSync - Overview**

https://docs.aws.amazon.com/ko_kr/appsync/latest/devguide/what-is-appsync.html

* AWS 에서 제공하는 완전관리형 <font color="#92d050">GraphQL</font> 서비스이다
* GraphQL을 사용하면 애플리케이션이 필요한 데이터를 쉽고 정확하게 얻을 수 있다
- 여기에는 하나 이상의 소스에서 데이터를 결합하는 것이 포함된다
    - NoSQL 데이터 저장소, 관계형 데이터베이스, HTTP API...
    - DynamoDB, Aurora, ElasticSearch 등과 통합
    - AWS Lambda를 사용한 사용자 정의 소스
- 실시간 데이터 전송을 위해 WebSocket을 사용하여 클라이언트와 서버간의 양방향 통신을 지원한다
- WebSocket에서 WebSocket 또는<font color="#ffc000">MQTT</font> 프로토콜을 사용하여 실시간으로 데이터 검색
- 모바일 앱의 경우 AppSync는 로컬 데이터 액세스 및 데이터 동기화 제공
- AppSync를 시작하려면 하나의 GraphQL 스키마를 업로드해야 한다

 >[!question] GraphQL
 >Facebook 에서 개발한 데이터 쿼리언어
 >HTTP 요청 방식을 사용하지만 RestAPI 와 다르게 POST 요청만 사용한다.
 >= 1개의 end point만 사용한다
 >[POST] /graphql
 >참고 : REST API는 총 4개의 end point를 사용한다.  - [GET] /api/v1/user , - [POST] /api/v1/user , - [PUT] /api/v1/user/:id, - [DELETE] /api/v1/user/:id
##### <font color="#ffc000">MQTT(Message Queuing Telemetry Transport)</font>
경량 메시징 프로토콜로, 주로 네트워크 대역폭이 제한된 환경이나 저전력 장치에서 효율적으로 동작하도록 설계됨. MQTT는 IoT(사물인터넷) 환경에서 널리 사용된다.

**게시/구독 모델**: MQTT는 클라이언트가 특정 주제(topic)에 메시지를 게시(publish)하고, 해당 주제를 구독(subscribe)한 다른 클라이언트가 메시지를 받는 방식으로 동작합니다. 이 모델을 통해 클라이언트 간의 느슨한 결합을 유지할 수 있습니다.

![[Pasted image 20240623194155.png]]
출처 : https://www.twilio.com/en-us/blog/what-is-mqtt

## **1. AWS AppSync Diagram**

### AppSync의 작동 방식

1. 클라이언트(모바일 앱, 웹 앱, 실시간 대시보드 등)가 AppSync에 액세스한다.
2. AppSync 내부에는 GraphQL 스키마와 Resolver가 존재한다.
3. Resolver는 다양한 데이터 소스(DynamoDB, Aurora, Elasticsearch Service, Lambda 등)에서 데이터를 가져오는 방법을 알고 있다.
4. Resolver는 데이터 소스로부터 데이터를 가져와 GraphQL 스키마에 맞게 응답을 생성한다.
5. 클라이언트는 GraphQL 쿼리를 통해 필요한 데이터를 AppSync로부터 요청하고 받아온다.
6. AppSync는 실시간 데이터 액세스, 오프라인 동기화 등의 기능을 제공한다.
7. AppSync의 모니터링은 CloudWatch Metric과 로그를 통해 이루어진다.

 >[!question] GraphQL 스키마와 Resolver 의 관계
 >- **GraphQL 스키마**
 >	- GraphQL 스키마는 API의 데이터 구조를 정의하는 것이다.
 >	- 스키마에는 Query, Mutation, Subscription 등의 최상위 타입과 각각의 필드들이 정의된다.
 >	- 스키마는 클라이언트가 API에 요청할 수 있는 데이터의 모양을 결정한다.
>- **Resolver**
>	- Resolver는 GraphQL 스키마의 각 필드에 대한 구현 로직을 담당한다.
>	- 클라이언트의 요청이 들어오면 Resolver가 실행되어 데이터 소스(DB, API 등)로부터 데이터를 가져와 응답을 생성한다.
>	- Resolver는 스키마의 필드와 1:1로 대응된다.
>- **관계**
>	- GraphQL 스키마는 API의 데이터 구조를 정의하고, Resolver는 그 구조에 맞는 데이터를 제공한다.
>	- 스키마에 정의된 필드들은 각각 Resolver 함수에 의해 구현된다.
>	- 클라이언트는 GraphQL 스키마에 따라 쿼리를 작성하고, AppSync는 그 쿼리를 해석하여 적절한 Resolver를 실행한다.

이처럼 AppSync는 GraphQL을 기반으로 클라이언트와 다양한 데이터 소스를 연결하는 관리형 서비스이다. 실시간 데이터 처리, 오프라인 동기화 등의 기능을 제공하여 개발자들이 보다 쉽게 애플리케이션을 구축할 수 있도록 돕는다.

## 2. Cognito Integration

- Cognito 사용자가 속한 그룹을 기준으로 권한 부여를 수행한다
- GraphQL 스키마에서 Cognito 그룹에 대한 보안을 지정할 수 있다

### **솔루션 아키텍처 관점에서 AppSync와 클라이언트 간의 인증 및 권한 부여 프로세스**

사용자가 Cognito로 인증받으면 client는 jwt token을 받아서 AppSync에 요청을 하고 요청 내용을 리졸버에서 GraphQL 요청을 변환하고 응답을 GraphQL 로 변환하여 전달한다.

1. 클라이언트는 Amazon Cognito를 통해 인증을 수행하고 JWT(JSON Web Token) 토큰을 얻는다.
2. 클라이언트는 이 JWT 토큰을 AppSync에 전달한다.
3. AppSync는 전달받은 토큰을 확인하고 Cognito에서 사용자가 승인되었는지 확인한다.
4. 인증 및 권한 확인이 완료되면, AppSync의 Resolver가 실행된다.
5. Resolver는 사용자의 Cognito 그룹 정보를 확인하고, 그에 따른 권한 수준을 적용한다.
6. 권한 확인이 완료되면 Resolver는 필요한 데이터 소스(예: DynamoDB)에 접근하여 데이터를 가져와 응답을 생성한다.
7. Resolver는 생성한 응답을 GraphQL로 변환하여 클라이언트에게 전달한다.

이와 같은 인증 및 권한 부여 프로세스를 통해 AppSync는 안전하고 통제된 방식으로 데이터에 대한 액세스를 제공할 수 있다. 클라이언트와 데이터 소스 간의 중간자 역할을 하면서 보안을 강화하는 것이 AppSync의 주요 기능 중 하나이다.