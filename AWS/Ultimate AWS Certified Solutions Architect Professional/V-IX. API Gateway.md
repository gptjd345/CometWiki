# I. **Overview**
p 255

[https://aws.amazon.com/ko/api-gateway/](https://aws.amazon.com/ko/api-gateway/)

[https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/welcome.html](https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/welcome.html)

- 어떤 규모에서든 개발자가 API를 손쉽게 생성, 게시, 유지 관리, 모니터링 및 보안 유지할 수 있도록 하는 완전관리형 서비스
- 백엔드 서비스의 데이터, 비즈니스 로직 또는 기능에 액세스할 수 있는 "정문" 역할
- Lambda, HTTP 및 AWS Services를 API로 노출할 수 있도록 지원한다
- 최대 수십만 개의 동시 API 호출을 수신 및 처리하는 데 관계된 모든 작업을 처리
    - 모니터링 및 API 버전 관리, 권한 부여 및 액세스 제어, 제한, 트래픽 관리(API keys 키, throttles 스로틀), 대규모, 서버리스, 요구/응답 변환, OpenAPI 사양, CORS 지원
- 최소 요금이나 시작 비용이 없다
    - 수신한 API 호출과 전송한 데이터 양에 대한 요금 결제
    - API 게이트웨이 계층화 요금 모델을 사용하는 경우 API 사용량 증가에 따라 비용 절감
- 제한 사항
    - 29초 타임아웃
    - 10MB 최대 페이로드 크기

![[API-Gateway .png]]

# II. **Deployment Stages**
p 256

- API 변경사항을 "Stages 스테이지"에 배치(원하는 개수만큼)
- 스테이지(dev, test, prod)에 대해 원하는 이름 사용
- 구축 기록이 보관됨에 따라 스테이지를 롤백할 수 있다

![[Deployment-Stages.png]]

# III. **Integrations**
p 257

[https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-api-integration-types.html](https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-api-integration-types.html)

[https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/how-to-integration-settings.html](https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/how-to-integration-settings.html)

- **HTTP**
    - API에서 백엔드의 HTTP endpoints 공개 가능
    - 예: 내부 HTTP API 온프레미스, Application Load Balancer...
    - Why? 속도 제한 추가, 캐싱, 사용자 인증, API 키 등...
- **Lambda Function**
    - Lambda 프록시 통합 또는 Lambda 비프록시(사용자 지정) 통합을 사용하여 API 메서드를 Lambda 함수와 통합 가능
    - AWS Lambda에서 지원하는 REST API를 쉽게 노출할 수 있는 방법
- **AWS Service**
    - API 게이트웨이를 통해 AWS API를 노출하시겠습니까?
    - 예: AWS Step Function 워크플로우 시작, SQS에 메시지 게시
    - Why? 인증 추가, 공개 배포, 요금 제어...

## **Solution Architecture** 토론: S3 앞 API 게이트웨이
p 258

아래와 같은 설계는 파일 업로드 시 10MB 페이로드 크기 제한의 영향을 받는다
![[Solution-Architecture1.png]]
### **클라이언트 애플리케이션과 API 게이트웨이 간의 통신 구조 개선**
![[Solution-Architecture2.png]]

1. 클라이언트 애플리케이션은 API 게이트웨이와 통신한다.
2. API 게이트웨이는 Lambda 함수와 연동되어 있다.
3. Lambda 함수는 S3 버킷에서 미리 서명된 URL을 생성할 수 있다.
4. 이 미리 서명된 URL은 가벼운 URL 형태로 클라이언트 애플리케이션에 전달된다.
5. 클라이언트 애플리케이션은 이 미리 서명된 URL을 사용하여 S3에 직접 파일을 업로드할 수 있다.
6. 이 구조를 통해 메가바이트, 기가바이트, 테라바이트 등 다양한 크기의 파일을 업로드할 수 있다.
7. 이는 매우 효율적이고 확장성 있는 솔루션 아키텍처이다.

이렇게 API 게이트웨이와 Lambda 함수, 미리 서명된 URL 등을 활용하여 파일 업로드 프로세스를 최적화하는 것이 매우 유용한 접근법이다.

# **IV. API Endpoint Types**
p 259

[https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-api-endpoint-types.html](https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-api-endpoint-types.html)

- **Edge-Optimized(default) 엣지 최적화**: 글로벌 클라이언트용
    - Cloud Front Edge 위치를 통해 요청이 라우팅됨 (가장 가까운 CloudFront 접속 지점(POP) 선택 → 지연 시간 개선)
    - API 게이트웨이가 여전히 한 지역에만 존재
- **Regional 리전**
    - 동일한 지역 내 클라이언트의 경우
    - CloudFront와 수동으로 결합 가능(캐싱 전략 및 배포에 대한 보다 많은 제어 가능)
- **Private 프라이빗**
    - 인터페이스 VPC 엔드포인트(ENI)를 사용하여 VPC에서만 액세스할 수 있다
        - 엔드포인트는 사용자가 VPC에서 만든 엔드포인트 네트워크 인터페이스(ENI)
    - 리소스 정책을 사용하여 액세스 정의

## **Caching API responses**
p 260

[https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-caching.html](https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-caching.html)

- 캐싱을 통해 백엔드로 호출하는 횟수를 줄일 수 있다
- 기본 TTL(time to live)은 300초(min: 0s, max: 3600s)이다
- 스테이지별로 캐시를 정의한다
- 메서드당 캐시 설정을 재정의할 수 있다
- 클라이언트는 헤더(Cache-Control: max-age=0)를 사용하여 캐시를 무효화할 수 있다(적절한 IAM 승인 필요)
- 전체 캐시를 즉시 플러시(비활성화)할 수 있다
- 캐시 암호화는 옵션
- 0.5GB에서 237GB 사이의 캐시 용량

![[Caching-API-responses.png]]

# **V. Errors**
p 261

- 4xx는 클라이언트 오류를 의미한다
    - 400: 잘못된 요청
    - 403: 액세스 거부, WAF 필터링
    - 429: 할당량 초과, Throttle
- 5xx는 서버 오류를 의미한다
    - 502: Bad Gateway 예외
        - Lambda 프록시 통합 백엔드에서 반환되는 호환되지 않는 출력
            - Lambda 함수에서 반환되는 출력이 API Gateway에서 예상하는 형식과 맞지 않는 경우 발생
            - Lambda 함수에서 반환되는 출력 형식을 API Gateway에서 예상하는 형식과 일치시키는 것이 중요
        - 부하가 많이 걸려 순서가 맞지 않는 호출
            - API Gateway와 Lambda 함수 간의 호출이 순차적으로 처리되지 않고 순서가 뒤바뀌는 경우 발생
            - Lambda 함수 내에서 적절한 상태 관리와 동기화 메커니즘을 구현하거나, API Gateway의 부하를 줄이기 위한 추가적인 최적화 작업이 필요
    - 503: 서비스 사용 불가 예외
    - 504: 통합 실패
        - Endpoint Request Time-out Exception API Gateway 요청이 최대 29초 후에 시간 초과됨

# **VI. Security**
p 262

- SSL 인증서를 로드한 다음 Route53을 사용하여 CNAME 정의
- **리소스 정책(~S3 버킷 정책)**
    - API에 액세스할 수 있는 사용자 제어
    - AWS 계정, IP 또는 CIDR 블록, VPC 또는 VPC Endpoints의 사용자
- **API 수준에서 API 게이트웨이를 위한 IAM 실행 역할**
    - Lambda 함수, AWS 서비스를 호출하려면 IAM 역할 정의 필요
- **CORS(원본 간 리소스 공유)**
    - 브라우저 기반 보안 제공
    - API를 호출할 수 있는 도메인 제어

# **VII. Authentication**
p 263

- **IAM 기반 액세스(AWS_IAM)**
    - 인프라 내에서 액세스를 제공하는 데 적합하다
    - SigV4를 통해 헤더에 IAM 자격 증명 전달
- **Lambda Authorizer(이전 사용자 정의 Authorizer)**
    - Lambda를 사용하여 사용자 지정 OAuth / SAML / 타사 인증 확인
- **Cognito User Pools**
    - 클라이언트가 Cognito로 인증한다
    - 클라이언트가 인증 코드 토큰을 API 게이트웨이로 전달한다
    - API Gateway는 토큰을 확인하는 방법을 알고 있다

![[Authentication.png]]

# **VIII. Logging, Monitoring,Tracing**
p 264

- **CloudWatch Logs**
    - 스테이지 레벨에서 CloudWatch 로깅 사용(로그 레벨 – ERROR, INFO 포함)
    - 전체 요청/응답 데이터 기록 가능
    - API Gateway Access Log 전송 가능(사용자 지정 가능)
    - Kinesis Data Firehos로 로그를 직접 보낼 수 있다(CW 로그 대신)
- **CloudWatch Metrics**
    - 세부적인 측정 기준을 가능하게 하는 스테이지별 측정 기준
    - 통합 지연 시간, 지연 시간, CacheHitCount, CacheMissCount
- **X-Ray**
    - API Gateway에서 요청에 대한 추가 정보를 가져오려면 추적 사용
    - X-Ray API Gateway + AWS Lambda가 아키텍처의 작동 원리에 대한 전체 그림 제공

# **IX. Usage Plans & API Keys**
p 265

[https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html)

- API를 고객에게 오퍼링($)으로 제공하고자 하는 경우
- **Usage Plans 사용 계획**
    - 하나 이상의 배포된 API 스테이지 및 방법에 액세스할 수 있는 사용자
    - 얼마나, 얼마나 빨리 그들이 그것들에 접근할 수 있는지
    - API 키를 사용하여 API 클라이언트를 식별하고 액세스를 측정한다
    - 개별 클라이언트에 적용되는 조절 제한 및 할당량 제한 구성
- **API Keys**
    - 고객에게 배포할 영숫자 문자열 값
        - Ex) WBjHxNtoAb4WPKBC7cGm64CBibIb24b4jt8jJHo9
    - 액세스 제어를 위해 사용 계획과 함께 사용할 수 있다
    - 조절 제한이 API 키에 적용된다
    - 할당량 제한은 전체 최대 요청 수이다
- **429 Too Many Requests**
    - 한 지역의 모든 API에 걸쳐 계정 수준 조절
    - 클라이언트는 재시도 메커니즘을 구현해야 한다

# **X. WebSocket API – Overview**
p 266

[https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-websocket-api.html](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-websocket-api.html)

- **WebSocket이란?**
    - 사용자의 브라우저와 서버 간의 양방향 대화형 통신
    - 서버는 클라이언트에 정보를 푸시할 수 있다
    - 이를 통해 상태에 맞는 애플리케이션 사용 사례 제공
- 웹소켓 API는 채팅 애플리케이션, 협업 플랫폼, 멀티플레이어 게임 및 금융 거래 플랫폼과 같은 실시간 애플리케이션에서 자주 사용된다
- AWS Services(Lambda, DynamoDB) 또는 HTTP 엔드포인트와 함께 작동한다

![[WebSocket-API.png]]

[https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/websocket-api-step-functions-tutorial.html](https://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/websocket-api-step-functions-tutorial.html)

## 서버 대 클라이언트 메시징 @connections를 클라이언트에 대한 회신에 사용
p 267

![[connections.png]]

1. WebSocket URL을 사용하여 클라이언트와 연결을 맺는다
2. 연결 ID를 DynamoDB 테이블에 저장하여 각 연결을 관리한다.
3. 연결 URL 콜백을 활용하여 /@connections/connectionid 형태로 특정 클라이언트에게 메시지를 전송할 수 있다.
4. 이를 통해 Lambda 함수에서 API Gateway의 특별한 콜백 URL에 데이터를 게시하면, 해당 데이터가 원하는 클라이언트에게 다시 전달될 수 있다.

WebSocket을 사용하면 양방향 통신이 가능하므로, 클라이언트에서 API Gateway로 요청을 보내는 것뿐만 아니라 API Gateway에서도 클라이언트에게 데이터를 보낼 수 있다. 이러한 방식으로 호환되지 않는 출력이나 순서가 맞지 않는 호출 문제를 해결할 수 있다.

# **XI. Private APIs**

p 268

[https://docs.aws.amazon.com/ko_kr/vpc/latest/privatelink/privatelink-access-aws-services.html](https://docs.aws.amazon.com/ko_kr/vpc/latest/privatelink/privatelink-access-aws-services.html)

- VPC interface endpoint를 사용하여 VPC에서만 액세스할 수 있다
- 각 VPC interface endpoint를 사용하여 여러 개인 API에 액세스할 수 있다
- API 게이트웨이 리소스 정책
    - AWS 계정 전반을 포함하여 선택한 VPC 및 VPC 엔드포인트에서 API에 대한 액세스 허용 또는 거부
    - 이를 통해 2단계의 보안 접근 제어가 가능하다. 특히 aws:SourceVpc조건을 이용하여 크로스 AWS 계정으로부터의 API 액세스를 제어할 수 있다
    
    ![[Private-APIs 1.png]]