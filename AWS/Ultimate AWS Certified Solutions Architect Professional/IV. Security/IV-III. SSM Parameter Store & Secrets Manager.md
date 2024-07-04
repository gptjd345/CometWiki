# I. SSM(AWS Systems Manager) Parameter Store

## 1. SSM Parameter Store의 주요 특징
p 102

[https://docs.aws.amazon.com/ko_kr/systems-manager/latest/userguide/systems-manager-parameter-store.html](https://docs.aws.amazon.com/ko_kr/systems-manager/latest/userguide/systems-manager-parameter-store.html)

- 구성 및 비밀 데이터를 위한 안전한 스토리지 제공
- KMS를 이용한 자동 암호화 지원(Seamless Encryption) (옵션)
- 서버리스, 확장성, 내구성, 간편한 SDK 제공
- 구성 및 비밀 데이터의 버전 관리 지원
- IAM을 통한 세부적인 액세스 제어 가능
- Amazon EventBridge와의 연동을 통한 알림 기능
- AWS CloudFormation과의 통합 지원

이를 통해 고객은 애플리케이션에 필요한 구성 및 비밀 데이터를 안전하게 저장하고 관리할 수 있다. 특히 KMS 암호화, 버전 관리, IAM 기반 액세스 제어 등의 기능을 활용하여 데이터 보안을 강화할 수 있다.

또한 서버리스 특성과 CloudFormation 연동을 통해 손쉽게 Parameter Store를 인프라에 통합할 수 있어 운영 효율성도 향상된다

### SSM Parameter Store과 AWS KMS의 연계 방식
[https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/services-parameter-store.html](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/services-parameter-store.html)

![[SSMParameterStore-AWS_KMS.png]]

1. 응용 프로그램은 SSM Parameter Store를 사용하여 일반 텍스트 형태의 구성 데이터를 저장할 수 있다.
2. 응용 프로그램의 IAM 권한이 중요합니다. EC2 인스턴스 역할 등을 통해 SSM Parameter Store에 대한 적절한 액세스 권한을 가져야 한다.
3. 구성 데이터가 암호화되어 있는 경우, SSM Parameter Store는 KMS(Key Management Service)를 사용하여 데이터를 암호화/복호화한다.
4. 응용 프로그램은 KMS 서비스에 대한 액세스 권한이 필요합니다. 이를 통해 암호화된 구성 데이터를 읽고 사용할 수 있다.
5. 즉, 응용 프로그램, SSM Parameter Store, KMS 서비스가 상호 연계되어 구성 데이터의 안전한 저장과 관리가 가능하다.

이와 같은 방식으로 응용 프로그램은 SSM Parameter Store와 KMS를 활용하여 구성 데이터의 보안과 관리를 강화할 수 있다.

## 2. SSM Parameter Store Hierarchy
p 103

### SSM Parameter Store의 계층 구조와 활용 방법

1. SSM Parameter Store에서는 계층 구조를 사용하여 매개변수를 구성할 수 있다. 예를 들어 "my-app/dev/db-url", "my-app/dev/db-password" 등의 형태로 구성할 수 있다.
2. 이를 통해 응용 프로그램은 필요한 수준의 매개변수에만 접근할 수 있도록 권한을 관리할 수 있다. 예를 들어 "my-app/dev/*" 경로에 대한 권한만 부여할 수 있다.
3. 또한 "my-app/prod/*" 경로의 매개변수에는 prod 환경의 Lambda 함수만 접근할 수 있도록 하는 등 보다 세분화된 권한 관리가 가능하다.
4. 이러한 계층 구조와 IAM 정책 관리를 통해 응용 프로그램의 보안성을 높일 수 있다.
5. 추가로 AWS에서 제공하는 퍼블릭 파라미터를 활용하는 것도 좋은 방법이다. 예를 들어 특정 지역의 최신 AMI ID를 찾는 등의 용도로 사용할 수 있다.
6. 결과적으로 SSM Parameter Store의 계층 구조와 IAM 정책 관리를 통해 응용 프로그램의 구성 데이터를 안전하고 효과적으로 관리할 수 있다.

## 3. Standard and advanced parameter tiers
p 104

[https://docs.aws.amazon.com/ko_kr/systems-manager/latest/userguide/parameter-store-advanced-parameters.html](https://docs.aws.amazon.com/ko_kr/systems-manager/latest/userguide/parameter-store-advanced-parameters.html)

|**구분**|**표준**|**고급**|
|---|---|---|
|허용되는 총 파라미터 수  <br>(AWS 계정 및 AWS 리전당)|10,000|100,000|
|파라미터 값의 최대 크기|4KB|8KB|
|파라미터 정책 사용 가능|아니요|예|
|비용|추가 요금 없음|요금 적용|
|스토리지 여금|무료|고급 파라미터당 월 $0.05|

## 4. Parameter 정책(고급 매개 변수용)

- 비밀번호와 같은 중요한 데이터를 강제로 업데이트하거나 삭제하기 위해 파라미터(만료일)에 TTL을 할당할 수 있다.
- 한 번에 여러 정책을 할당할 수 있다.

### 파라미터 스토어의 만료 정책을 설정하고 EventBridge를 통해 알림을 받는 방법

![[Parameter.png]]

1. 파라미터 스토어에 저장된 파라미터가 만료되기 15일 전에 EventBridge를 통해 알림을 받도록 설정한다.
2. 이를 통해 파라미터를 업데이트할 충분한 시간을 확보할 수 있다.
3. 파라미터가 TTL(Time-to-Live) 기간 내에 업데이트되지 않으면 자동으로 삭제된다.
4. EventBridge에서는 파라미터가 변경되지 않은 상태로 20일이 지나면 별도의 알림을 보내도록 설정할 수 있다.

이렇게 EventBridge와 파라미터 스토어를 연계하면 파라미터 관리를 자동화하고 효율적으로 관리할 수 있다. 필요에 따라 다양한 알림 및 자동화 정책을 구현할 수 있다.

# II. AWS Secrets Manager

## 1. AWS Secrets Manager의 주요 기능
p 106

[https://docs.aws.amazon.com/ko_kr/secretsmanager/latest/userguide/intro.html](https://docs.aws.amazon.com/ko_kr/secretsmanager/latest/userguide/intro.html)

1. **비밀 저장 및 관리**: 비밀번호, API 키, 데이터베이스 연결 문자열 등과 같은 중요한 정보를 안전하게 저장하고 관리할 수 있다.
2. **보안 암호 교제**: X일마다 비밀을 자동으로 회전시키는 기능을 제공한다. 이를 통해 보안성을 높일 수 있다. 회전 시 비밀 생성은 Lambda 함수를 사용하여 자동화할 수 있다.
    - 관리형 교체: 대부분의 관리 비밀의 경우 서비스가 사용자를 대신하여 순환을 구성하고 관리하는 관리 순환을 사용한다. (Lambda 함수 사용X)
    - Lambda 함수에 의한 로테이션: 다른 유형의 비밀의 경우 Secrets Manager 로테이션은 Lambda 함수를 사용하여 시크릿과 데이터베이스 또는 서비스를 업데이트한다.
    - [https://docs.aws.amazon.com/ko_kr/secretsmanager/latest/userguide/rotating-secrets.html](https://docs.aws.amazon.com/ko_kr/secretsmanager/latest/userguide/rotating-secrets.html)
3. **데이터베이스 지원**: Amazon RDS, Redshift, DocumentDB 등 다양한 데이터베이스 엔진을 지원하며, 사용자 지정 Lambda 기능을 통해 기타 데이터베이스와도 통합할 수 있다.
4. **액세스 제어**: 리소스 기반 정책을 사용하여 비밀에 대한 액세스 제어를 할 수 있다.
5. **다른 AWS 서비스와의 통합**: Secrets Manager는 AWS CloudFormation, AWS CodeBuild, Amazon ECS, Amazon EMR, AWS Fargate, Amazon EKS 등의 다른 AWS 서비스와 통합되어, 이들 서비스에서 Secrets Manager에 저장된 비밀을 쉽게 사용할 수 있다.

이와 같이 Secrets Manager는 애플리케이션의 중요한 보안 정보를 안전하게 저장하고 관리할 수 있는 강력한 AWS 서비스이다.

### AWS Secrets Manager와 ECS 통합

![[AWSSecretsManager-ECS.png]]

1. 시크릿 매니저를 통해 데이터베이스 암호와 같은 중요한 비밀 정보가 관리되고 있다.
2. RDS 데이터베이스와 시크릿 매니저가 연결되어 있어, 데이터베이스 암호가 시크릿 매니저에 저장되고 있다.
3. 자원 기반 정책을 사용하여 비밀 정보에 대한 접근을 통제할 수 있다.
4. 시크릿 매니저는 다른 서비스들과 통합되어, 자동으로 비밀 정보를 가져올 수 있다.
5. ECS 태스크가 시크릿 매니저에서 데이터베이스 암호를 가져와 환경 변수로 사용하여, RDS 데이터베이스에 안전하게 접근할 수 있다.

이와 같이 시크릿 매니저를 사용하여 중요한 비밀 정보를 안전하게 관리하고, 다른 서비스와 통합하여 활용할 수 있다.

## 2. Secrets Manager – with CloudFormation

p 107

[https://docs.aws.amazon.com/ko_kr/secretsmanager/latest/userguide/cloudformation.html](https://docs.aws.amazon.com/ko_kr/secretsmanager/latest/userguide/cloudformation.html)

### AWS CloudFormation에서 AWS Secrets Manager 보안 암호 생성

![[AWSCloudFormation.png]]

1. 첫 번째 부분에서는 보안 암호를 생성한다. [https://docs.aws.amazon.com/ko_kr/secretsmanager/latest/userguide/cfn-example_secret.html](https://docs.aws.amazon.com/ko_kr/secretsmanager/latest/userguide/cfn-example_secret.html)
2. 두 번째 부분에서는 RDS DB 인스턴스의 비밀을 참조한다.
3. 세 번째 부분에서는 'RDS DB 인스턴스에 비밀을 연결하는 비밀 첨부 파일'을 만든다. 비밀 회전 시, RDS 데이터베이스의 비밀번호를 변경해야 한다는 것을 시크릿 매니저에게 알려준다.

## 3. Secrets Manager – Sharing Across Accounts
p 108

![[SecretsManager–SharingAcrossAccounts.png]]
1. 시크릿 매니저에 저장된 비밀은 KMS Key로 암호화되어 있다. 따라서 개발자 계정의 사용자가 해당 비밀에 접근하려면 KMS Key에 대한 권한이 필요하다.
2. 먼저 KMS Key에 KMS 정책을 연결해 개발자 계정의 사용자가 KMS Decrypt 작업을 수행할 수 있도록 허용한다. 이를 통해 개발자 계정의 사용자가 KMS Key를 사용하여 비밀을 복호화할 수 있다. (Resource Access Manager로는 해결 불가능)
3. 그 다음 시크릿 매니저의 시크릿에 리소스 기반 정책을 적용한다. 이를 통해 개발자 계정 사용자에게 해당 비밀의 GetSecretValue 권한을 부여할 수 있다.
4. 이 두 가지 권한(KMS 키 사용 권한, 시크릿 매니저 비밀 접근 권한)이 부여되면, 개발자 계정 사용자는 시크릿 매니저의 비밀에 접근하고 이를 사용할 수 있다.

이와 같은 방식으로 시크릿 매니저의 비밀을 개발자 계정에 안전하게 공유할 수 있다. 리소스 기반 정책과 KMS 키 사용 권한을 통해 보안을 유지하면서도 필요한 사용자에게 비밀 정보를 제공할 수 있다.

## 4. SSM Parameter Store vs Secrets Manager
p 108

### Secrets Manager

- Secrets 저장 및 자동 회전에 대한 비용이 발생
- AWS Lambda를 통해 시크릿을 자동으로 로테이션
- KMS 암호화는 필수 사항
- RDS, Redshift, DocumentDB에 Lambda 기능이 제공된
- AWS Cloud Formation과 통합 가능

### SSM Parameter Store

- 상대적으로 저렴한 비용으로 사용
- 간단한 API
- 시크릿 로테이션 없음(EventBridge에 의해 트리거된 Lambda를 사용하여 로테이션 활성화 가능)
- KMS 암호화는 선택 사항
- AWS Cloud Formation과 통합 가능
- SSM Parameter Store API를 사용하여 Secrets Manager의 시크릿을 풀 수 있음

## 5. SSM Parameter Store vs. Secrets Manager Rotation
p 109

![[Rotation.png]]

Secrets Manager의 경우 30일 마다 Lambda 함수를 호출해, Amazon RDS의 암호를 변경할 수 있다.
[https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/rds-secrets-manager.html](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/rds-secrets-manager.html)

SSM Parameter Store의 경우 Amazon EventBridge를 통해 30일 마다 Lambda 함수를 호출해, SSM Parameter Store와 Amazon RDS의 암호를 변경할 수 있다.