# I. AWS CloudTrail
p 81 - 82

[https://docs.aws.amazon.com/ko_kr/awscloudtrail/latest/userguide/cloudtrail-user-guide.html](https://docs.aws.amazon.com/ko_kr/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)

- AWS 계정의 거버넌스, 컴플라이언스 및 감사를 제공하는 중요한 서비스이다.
- 활성화 : CloudTrail은 AWS 계정 생성 시 기본적으로 활성화되어 있다.
- 이벤트/API 호출 기록 : AWS 계정 내에서 발생한 이벤트와 API 호출을 기록하고, 다음 서비스를 통해 기록을 가져올 수 있다.
    - Console
    - SDK
    - CLI
    - AWS Services
- 로그 저장 : CloudTrail 로그는 CloudWatch Logs나 S3 버킷에 저장할 수 있다.
- 추적 범위 : 추적은 모든 리전(기본값) 또는 단일 리전에 적용할 수 있다.
- 리소스 삭제 조사: AWS에서 리소스가 삭제된 경우 먼저 CloudTrail을 조사하여 삭제 이벤트를 확인할 수 있다.

> **CloudTrail Diagram**
	![[CloudTrailDiagram.png]]
> - 중앙 집중화된 로깅: CloudTrail은 AWS 계정 내에서 발생하는 모든 API 호출 이벤트를 기록하고 중앙에서 관리한다. 이를 통해 보안 및 규정 준수 감사에 활용할 수 있다.
> - 다양한 이벤트 기록: SDK, CLI, Console, IAM Users & IAM Roles, 다른 서비스 등 AWS 내에서 발생하는 다양한 이벤트를 기록한다.
> - 장기 보관: 기본적으로 90일 동안의 이벤트 기록을 제공하지만, CloudWatch 로그 또는 S3 버킷으로 전송하여 장기간 보관할 수 있다.
> - 감사 및 조사: CloudTrail 콘솔에서 발생한 이벤트를 확인하고 분석할 수 있어, 보안 및 규정 준수 감사에 활용할 수 있다.

## 1. CloudTrail Events
p 83 - 84

[https://docs.aws.amazon.com/ko_kr/awscloudtrail/latest/userguide/cloudtrail-concepts.html](https://docs.aws.amazon.com/ko_kr/awscloudtrail/latest/userguide/cloudtrail-concepts.html)

CloudTrail 세 가지 유형의 이벤트를 기록한다.
모든 이벤트 유형은 CloudTrail JSON 로그 형식을 사용한다.
기본적으로 추적 및 이벤트 데이터 스토어는 관리 이벤트를 로그하지만 데이터 또는 Insights 이벤트는 로그하지 않는다.

1. **Management Events(관리 이벤트)**
    - AWS 계정의 리소스에 대해 수행되는 관리 작업에 대한 정보를 제공한다.(제어 영역 작업)
    - 예제
        - 보안 구성 (예: AWS Identity and Access Management `AttachRolePolicy` API Call 작업)
	        - IAM AttachRolePolicy : 하나 이상의 AWS 관리형 정책 또는 고객 관리형 정책을 특정 IAM 역할(Role)에 연결하는 API 작업
        - 디바이스 등록(예: Amazon EC2 `CreateDefaultVpc` API Call 작업)
        - 데이터 라우팅 규칙 구성(예: Amazon EC2 `CreateSubnet` API Call 작업)
	        - Amazon EC2 CreateSubnet : VPC(가상 사설 클라우드) 내에 서브넷을 생성하는 API 작업
        - 로깅 설정 (예: AWS CloudTrail `CreateTrail` API Call 작업).
        - 기본적으로 CloudTrail 트레일 및 CloudTrail 레이크 이벤트 데이터는 로그 관리 이벤트를 저장한다.
        - 읽기 이벤트(리소스를 수정하지 않음)와 쓰기 이벤트(리소스를 수정할 수 있음)를 분리할 수 있다.
	         
2. **Data Events(데이터 이벤트)**
    - 리소스 상에서, 또는 리소스 내에서 수행되는 리소스 작업에 대한 정보를 제공한다. (데이터 영역 작업)
    - 예제
        - S3 버킷의 객체에 대한 [Amazon S3 객체 수준 API 활동](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cloudtrail-logging-s3-info.html#cloudtrail-data-events) (예: `GetObjectDeleteObject`,, 및 `PutObject` API Call 작업)
            - Amazon S3 개체 수준 활동(예: GetObject, DeleteObject, PutObject): 읽기 및 쓰기 이벤트를 구분할 수 있다.
        - AWS Lambda 함수 실행 활동 (API) `Invoke`
        - CloudTrail [`PutAuditEvents`](https://docs.aws.amazon.com/awscloudtraildata/latest/APIReference/API_PutAuditEvents.html)외부의 이벤트를 기록하는 데 사용되는 [CloudTrail Lake 채널에서의](https://docs.aws.amazon.com/ko_kr/awscloudtrail/latest/userguide/query-event-data-store-integration.html) 활동 AWS
        - 주제에 따른 Amazon SNS [`Publish`](https://docs.aws.amazon.com/sns/latest/api/API_Publish.html) 및 [`PublishBatch`](https://docs.aws.amazon.com/sns/latest/api/API_PublishBatch.html) API 운영이다.
    - 기본적으로 데이터 이벤트는 기록되지 않는다. (대량 작업으로 인한 비용 때문)
    - 필요한 경우 데이터 이벤트 기록을 활성화할 수 있다.
	    
3. **Insights Event(인사이트 이벤트)**
    - 비정상적인 활동 탐지
        - 부정확한 리소스 프로비저닝
        - 서비스 제한 도달
        - IAM 작업 버스트
        - 주기적인 유지보수 활동 공백
    - 정상 활동 기준선 생성
        - 정상적인 관리 이벤트를 분석하여 기준선을 만든다.
    - 비정상 패턴 감지
        - 지속적으로 쓰기 이벤트를 분석하여 정상 기준선과 비교하여 비정상적인 패턴을 감지한다.
    - 알림 및 자동화
        - CloudTrail 콘솔에 이상 징후를 표시
        - Amazon S3로 이벤트 발송
        - EventBridge 이벤트를 생성하여, (자동화 요구에 따라) 자동화된 대응을 트리거로 사용
    
    > **작동원리**
    > ![[CloudTrailInsights.png]]
    > 1. 관리 이벤트 분석: CloudTrail은 AWS 리소스에 대한 관리 이벤트를 기록하고, CloudTrail Insight가 이를 지속적으로 분석한다.
    > 2. 이상 현상 감지: CloudTrail Insight는 이벤트 패턴을 분석하여 비정상적인 활동을 감지하고, 이를 인사이트 이벤트로 생성한다.
    > 3. 인사이트 이벤트 생성: 감지된 이상 현상은 CloudTrail 콘솔에 표시되며, 아마존 인더스트리 및 EventBridge 이벤트로도 전송된다.
    > 4. 자동화 연계: CloudWatch를 통해 인사이트 이벤트를 감지하고, 이에 대한 자동화 작업(예: 이메일 전송)을 수행할 수 있다.

## 3. CloudTrail Events Retention(이벤트 보존)
p 85

- CloudTrail 이벤트는 기본적으로 90일 동안 CloudTrail에 저장된다.
- 이 기간 이후에 이벤트를 유지하려면 이벤트를 S3에 기록하고 Athena를 사용한다.
    - Amazon S3 버킷에 저장 → Amazon Athena를 사용하여 분석
        - Athena를 사용하면 SQL 쿼리를 통해 S3에 저장된 CloudTrail 이벤트 데이터를 쉽게 분석할 수 있다.

> **작동원리**
> ![[CloudTrailEventsRetention.png]]
> 1. **CloudTrail 로그 저장**
>     - 관리 이벤트, 데이터 이벤트, 인사이트 이벤트 등 모든 이벤트 데이터가 CloudTrail에 기록된다.
>     - 기본적으로 90일 동안 CloudTrail 로그가 유지된다.
> 2. **S3 버킷에 장기 저장**
>     - 장기간 이벤트 데이터를 보관하기 위해 S3 버킷에 로그를 전송할 수 있다.
>     - S3 버킷에 저장된 로그 데이터는 무제한 기간 동안 보관할 수 있다.
> 3. Athena를 통한 분석:
>     - S3 버킷에 저장된 CloudTrail 로그 데이터는 Athena 서비스를 통해 쿼리할 수 있다.
>     - Athena는 서버리스 서비스이므로 별도의 인프라 구축 없이 SQL 쿼리로 로그 데이터를 분석할 수 있다.






# II. **Integration**

>[!question] EventBridge Integration?
EventBridge Integration = AWS CloudTrail + Amazon EventBridge 를 의미한다.

## 1. Amazon EventBridge - Intercept API Calls
p 86

EventBridge와 CloudTrail을 통합하면 API 호출을 모니터링하고 특정 이벤트에 대한 알림을 받을 수 있다.

![[InterceptAPICalls.png]]

SNS 알림을 받고 싶다면 다음과 같은 과정으로 구현할 수 있다.

1. 사용자는 API Call로 DynamoDB의 테이블을 삭제 요청을 한다.
2. CloudTrail에서 DynamoDB 테이블 삭제 API 호출이 기록된다.
3. Amazon EventBridge는 CloudTrail 로그에서 이 API 호출 이벤트를 감지한다.
4. Amazon EventBridge의 규칙을 통해 특정 조건(예: DynamoDB 테이블 삭제 API 호출)을 감지하면 이를 트리거로 만든다.
5. 트리거된 규칙은 Amazon SNS로 메시지를 전송한다.
6. SNS에 가입한 구독자(예: 이메일 주소)에게 알림이 전송된다.

이를 통해 DynamoDB 테이블 삭제와 같은 중요한 API 호출이 발생할 때 신속하게 알림을 받을 수 있다. 이는 보안 모니터링, 감사 및 운영 관리에 유용하다.

## 2. Amazon EventBridge + CloudTrail
p 87

EventBridge와 CloudTrail의 통합을 활용하여 다양한 AWS 리소스 및 API 호출 이벤트를 모니터링하고 알림을 받을 수 있다.

![[AmazonEventBridge-CloudTrail.png]]

### 사용자가 AWS 계정에서 역할을 수행하는 것을 모니터링하고 알림을 받는 예시

1. **CloudTrail 구성**
    - CloudTrail을 통해 AWS 계정의 모든 API 호출 내역을 로깅하도록 설정한다.
    - AssumeRole API 호출이 CloudTrail에 기록된다.
2. **EventBridge 규칙 생성**
    - EventBridge에서 새 규칙을 생성한다.
    - 이벤트 소스를 "CloudTrail Event"로 선택한다.
    - 이벤트 유형을 "AssumeRole" API 호출로 필터링한다.
3. **대상 구성**
    - EventBridge 규칙의 대상으로 SNS 토픽을 선택한다.
    - SNS 토픽을 생성하고 이메일 또는 SMS 알림을 받도록 구독한다.
4. **규칙 활성화**
    1. EventBridge 규칙을 활성화하여 실행 상태로 만든다.

이렇게 구성하면 사용자가 AWS 계정에서 역할을 수행할 때마다 CloudTrail에 기록되고, EventBridge 규칙이 이를 감지하여 SNS 토픽을 통해 알림을 보내게 된다. 이를 통해 권한 상승 활동을 실시간으로 모니터링할 수 있다.

### 보안 그룹 인바운드 규칙 변경과 같은 API 호출로 CloudTrail과 EventBridge를 통해 모니터링하는 예시

1. **CloudTrail 구성**
    - CloudTrail을 통해 AWS 계정의 모든 API 호출 내역을 로깅하도록 설정한다.
    - AuthorizeSecurityGroupIngress API 호출이 CloudTrail에 기록된다.
2. **EventBridge 규칙 생성**
    - EventBridge에서 새 규칙을 생성한다.
    - 이벤트 소스를 "CloudTrail Event"로 선택한다.
    - 이벤트 유형을 "AuthorizeSecurityGroupIngress" API 호출로 필터링한다.
3. **대상 구성**
    - EventBridge 규칙의 대상으로 SNS 토픽을 선택한다.
    - SNS 토픽을 생성하고 이메일 또는 SMS 알림을 받도록 구독한다.
4. **규칙 활성화**
    - EventBridge 규칙을 활성화하여 실행 상태로 만든다.

이렇게 구성하면 사용자가 보안 그룹의 인바운드 규칙을 변경할 때마다 CloudTrail에 기록되고, EventBridge 규칙이 이를 감지하여 SNS 토픽을 통해 알림을 보내게 된다. 이를 통해 보안 그룹 변경 활동을 실시간으로 모니터링할 수 있다.

# III. CloudTrail – SA Pro


