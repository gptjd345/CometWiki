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

# III. CloudTrail – Solution Architecture(SA) Pro

CloudTrail과 다양한 AWS 서비스를 활용한 솔루션 아키텍처 구현

## 1. Solution Architecture: Delivery to S3
p 88

![[DeliverytoS3.png]]

1. **CloudTrail에서 S3로 파일 전송**
    - CloudTrail은 5분 단위로 로그 파일을 S3 버킷에 전송할 수 있다.
    - 이때 SSE-S3(default) 또는 SSE-KMS 암호화를 활성화할 수 있다.
    - S3 버킷에 수명주기 정책을 설정하여 오래된 로그 파일을 Glacier 계층으로 이동시킬 수 있다.
2. **S3 이벤트 기반 알림 구현**
    - S3에 로그 파일이 저장되면 S3 이벤트를 통해 SQS 큐, SNS 토픽, Lambda 함수 등에 알림을 보낼 수 있다.
3. **CloudTrail 기반 알림 구현**
    - CloudTrail이 직접 SNS 토픽으로 알림을 보내도록 구성할 수 있다.
    - SNS 토픽에서는 SQS 큐나 Lambda 함수를 호출하는 등 다양한 후속 작업을 수행할 수 있다.

### **S3의 주요 보안 및 관리 기능**

- **S3 버전 관리 사용**
    - S3 버전 관리를 사용하면 버킷에 저장된 모든 버전의 객체를 모두 보존, 검색 및 복원할 수 있다.
    - 의도치 않은 사용자 작업 및 애플리케이션 장애로부터 더 쉽게 복구할 수 있다.
    - 버전 관리가 사용 설정된 버킷의 경우 실수로 삭제되거나 덮어쓴 객체를 복구할 수 있다.
    - [https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/Versioning.html](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/Versioning.html)
- **MFA(Multi Factor Authentication) 삭제 보호**
    - Amazon S3 버킷에서 S3 버전 관리를 사용하는 경우 선택적으로 MFA(멀티 팩터 인증) Delete를 사용 설정하도록 버킷을 구성하여 다른 보안 계층을 추가할 수 있다.
    - MFA Delete 및 MFA 보호 API 액세스는 서로 다른 시나리오에 대한 보호를 제공하기 위해 마련된 기능이다.
    - [https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/MultiFactorAuthenticationDelete.html](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/MultiFactorAuthenticationDelete.html)
- **S3 라이프사이클 정책(S3 IA, Glacier…)**
    - 수명 주기 동안 객체가 비용 효율적으로 저장되도록 관리하려면 Amazon S3 수명 주기 구성을 생성한다.
    - 데이터 수명 주기에 따라 S3, S3 Glacier, S3 Glacier Deep Archive로 자동으로 이동시킬 수 있다.
    - [https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/object-lifecycle-mgmt.html](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)
- **S3 객체 잠금 사용**
    - 고정된 시간 동안 또는 무기한으로 Amazon S3 객체의 삭제 또는 덮어쓰기를 방지하는 데 도움이 된다.
    - 객체 잠금은 보관 기간 및 법적 보존 등 객체 보관을 관리하는 2가지 방법을 제공한다.
    - S3 버전 관리가 활성화된 버킷에서만 작동한다. 객체 버전을 잠그면 Amazon S3는 해당 객체 버전의 메타데이터에 잠금 정보를 저장한다.
    - 객체에 보존 기간 또는 법적 보존을 설정하면 요청에 지정된 버전만 보호된다.
    - 보존 기간과 법적 보존은 객체의 새로운 버전을 생성하거나 객체에 추가할 마커를 삭제하는 것을 차단하지 않는다.
    - [https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/object-lock.html](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/object-lock.html)
- **SSE-S3 or SSE-KMS encryption(서버측 암호화)**
    - **SSE-S3**
        - 아마존 S3에서 관리하는 키를 사용하여 암호화하는 방법이다.
        - S3 managed data key는 S3에 의해 완전히 소유되고 관리 된다.
        - **암호화 과정**
            1. 암호화되지 않은 객체를 S3 로 업로드하여 SSE-S3 암호화를 할 것이다.
            2. S3 로 객체를 전송할 때 HTTP/HTTPS 프로토콜로 헤더에 "x-amz-server-side-encryption":"AES256" 을 설정해 전송한다.
            3. 그러면 Amazon S3 는 이 헤더를 통해 S3 Managed Data Key 로 전송받은 Object 를 암호화 하고 저장한다.
    - **SSE-KMS (Key Manager Service)**
        - 아마존 S3에서 관리하는 키를 사용하여 암호화하는 방법이다.
        - 위의 SSE-S3보다는 조금 더 사용자 단위의 컨트롤을 할 수 있다.
        - KMS 에서 키를 관리하는 이유는 어떤 사람이 키에 접근 가능하게 하고 어떤 사람은 키에 접근하지 못하게 하는 것이 가능하고, 추적도 가능하기 때문이다.
        - **암호화 과정**
            1. Amazon S3 는 전송받은 객체의 헤더를 확인하고 어떤 방식으로 암호화 할 지 설정한다.
            2. SSE-KMS 방식이기 때문에 S3 에서 암호화 할 때 사용하는 키는 KMS 에서 미리 세팅해놓은 KMS Customer Master Key 를 사용하여 전송받은 객체를 암호화한다.
            3. 그리고 암호화 한 객체를 S3 버킷에 저장한다.
    - [](https://inpa.tistory.com/entry/AWS-%F0%9F%93%9A-S3-%EA%B0%9D%EC%B2%B4-%EC%95%94%ED%98%B8%ED%99%94-%EA%B8%B0%EB%8A%A5-%EC%A2%85%EB%A5%98-%EB%B0%8F-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)[https://inpa.tistory.com/entry/AWS-📚-S3-객체-암호화-기능-종류-및-사용하기](https://inpa.tistory.com/entry/AWS-%F0%9F%93%9A-S3-%EA%B0%9D%EC%B2%B4-%EC%95%94%ED%98%B8%ED%99%94-%EA%B8%B0%EB%8A%A5-%EC%A2%85%EB%A5%98-%EB%B0%8F-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
- **CloudTrail Log File Integrity(무결성) 검증을 수행하는 기능(해싱 및 서명용 SHA-256)**
    - 로그 파일이 CloudTrail 전송된 후 수정, 삭제 또는 변경되지 않았는지 확인하려면 CloudTrail 로그 파일 무결성 검증을 사용할 수 있다.
    - 이 기능은 산업 표준 알고리즘(해시의 경우 SHA-256, 디지털 서명의 경우 RSA 포함 SHA-256)으로 구축되었다.
    - [https://docs.aws.amazon.com/ko_kr/awscloudtrail/latest/userguide/cloudtrail-log-file-validation-intro.html](https://docs.aws.amazon.com/ko_kr/awscloudtrail/latest/userguide/cloudtrail-log-file-validation-intro.html)

## 2. Solution Architecture: Multi Account, Multi Region Logging

**CloudTrail의 다중 계정 및 다중 지역 기능**

p 89

CloudTrail은 다중 계정에서 로그를 수집할 수 있다. 예를 들어 계정 A와 계정 B에서 발생한 이벤트 로그를 수집할 수 있다.

![[MultiAccount.png]]

1. 계정 A와 B의 CloudTrail은 로그를 보안 계정의 S3 버킷에 저장한다. (이 보안 계정은 다른 두 계정과는 별도의 계정이다.)
2. S3 버킷 정책을 정의하여 CloudTrail이 해당 S3 버킷에 로그 파일을 전송할 수 있도록 허용해야 한다.
3. 이렇게 하면 CloudTrail이 다중 계정의 이벤트 로그를 안전하게 중앙 집중화하여 관리할 수 있다.

이렇게 CloudTrail의 다중 계정 및 다중 지역 기능을 활용하여 로그 관리의 효율성과 보안성을 높일 수 있다.

### 로그 관리의 효율성과 보안성을 높일 수 있는 방법

1. **S3 버킷 정책 설정**
    - 상호 계정 간 로그 전송을 위해 필수적인 요소이다.
    - 계정 A가 CloudTrail 파일에 액세스하려는 경우:
        - 옵션 1: 계정 간 역할 생성 및 역할 수행
        - 옵션 2: 버킷 정책 편집
2. **보안 계정 활용**
    - 로그를 안전하게 보관할 수 있는 별도의 보안 계정을 사용한다.
    - 사용자 관리 및 접근 통제가 더 엄격해져 로그 보안이 강화된다.
    - 장기적으로 로그 데이터의 안전성을 확보할 수 있다.

## 3. Solution Architecture: Alert for API calls
p 90

[https://docs.aws.amazon.com/ko_kr/AmazonCloudWatch/latest/monitoring/logging_cw_api_calls.html](https://docs.aws.amazon.com/ko_kr/AmazonCloudWatch/latest/monitoring/logging_cw_api_calls.html)

### CloudTrail과 CloudWatch를 활용하여 특정 API 호출에 대한 경고 생성 방법

![[AlertforAPIcalls.png]]

1. CloudTrail을 통해 API 호출 이벤트를 CloudWatch 로그로 스트리밍한다.
2. CloudWatch 로그에서 관심 있는 API 호출 패턴을 메트릭 필터로 정의한다.
3. 메트릭 필터가 일치하면 CloudWatch 알람을 트리거하도록 설정한다.
4. CloudWatch 알람이 트리거되면 SNS 주제로 알림을 보낸다.
5. SNS 주제에서는 다양한 작업을 수행할 수 있다. 예를 들어 Lambda 함수를 호출하거나 SQS 큐에 메시지를 전송할 수 있다.
6. 이를 통해 특정 API 호출이 발생할 때마다 경고를 생성하고, 이에 대한 자동화된 대응 프로세스를 구현할 수 있다. 예를 인스턴스 종료 API 호출이 감지되면 즉시 알람을 발생시키고 대응 조치를 취할 수 있다.

이처럼 CloudTrail과 CloudWatch의 조합을 통해 효과적인 모니터링 및 경고 체계를 구축할 수 있습니다. 사용 사례에 따라 다양한 방식으로 활용할 수 있는 유용한 기능들입니다.

- 로그 필터 메트릭을 사용하여 높은 수준의 API 발생을 감지할 수 있다. 특정 API 호출이 아니더라도 전반적인 API 호출 패턴을 분석할 수 있다.
    - 예: EC2 TerminateInstance API 발생 횟수
    - 예: 사용자당 API 호출 수
    - 예: 높은 수준의 거부된 API 호출 탐지
- 이를 통해 비정상적인 활동이나 보안 침입 시도 등을 감지할 수 있다. 단순한 API 호출 감지를 넘어 전반적인 보안 모니터링이 가능하다.
- 메트릭 필터는 유연하게 구성할 수 있어, 사용 사례에 맞는 다양한 지표를 정의할 수 있다.
- CloudWatch 알람은 이러한 지표를 기반으로 경보를 발생시키므로, 이상 징후를 신속하게 감지하고 대응할 수 있다.

## 5. Solution Architecture: Organizational Trail
p 91

[https://docs.aws.amazon.com/ko_kr/awscloudtrail/latest/userguide/creating-trail-organization.html](https://docs.aws.amazon.com/ko_kr/awscloudtrail/latest/userguide/creating-trail-organization.html)

CloudTrail의 조직 트레일 기능은 AWS 조직 내에서 발생하는 모든 이벤트를 중앙에서 모니터링할 수 있게 해주는 매우 강력한 기능이라고 할 수 있다.

![[OrganizationalTrail.png]]

### CloudTrail의 조직 트레일 주요 기능

1. AWS 조직에서 관리 계정은 조직 트레일을 설정할 수 있다. 이 트레일은 관리 계정에 생성되며, 모든 자식 계정의 이벤트를 모니터링한다.
2. 관리 계정에서 생성된 조직 트레일은 자식 계정의 모든 이벤트를 캡처한다. 이를 통해 조직 전체의 활동을 통합적으로 모니터링할 수 있다.
3. 개발자 OU, 프로덕션 OU 등 조직의 다양한 계정들이 모두 이 트레일에 의해 모니터링된다. (편리하고 효과적인 방식)
4. 관리 계정의 S3 버킷에 이벤트 로그가 저장되며, S3 버킷 접두사에는 모니터링되는 계정 번호가 포함된다.
5. 이를 통해 조직 전체의 이벤트를 통합적으로 관리하고 분석할 수 있다.

## 6. 이벤트에 가장 빠르게 반응하는 방법
p 92

전체적으로 CloudTrail은 이벤트를 제공하는 데 최대 15분이 소요될 수 있다.

CloudTrail 이벤트에 가장 빠르게 대응하는 방법은 다음과 같습니다:

- **EventBridge 사용**
    - CloudTrail의 모든 API 호출에 대해 EventBridge를 트리거할 수 있다.
    - EventBridge는 CloudTrail 이벤트를 실시간으로 캡처하므로 가장 빠르고 반응이 좋은 방법이다.
- **CloudWatch Logs의 CloudTrail Delivery 사용**
    - CloudTrail 이벤트를 CloudWatch Logs로 스트리밍할 수 있다.
    - CloudWatch Logs에서 메트릭 필터를 수행하여 이상 징후를 신속하게 탐지할 수 있다.
- **S3에 CloudTrail Delivery 사용**
    - CloudTrail 이벤트를 5분마다 S3 버킷으로 전달할 수 있다.
    - S3에 저장된 이벤트는 로그 무결성 분석, 교차 계정 제공, 장기 저장이 가능하다.


