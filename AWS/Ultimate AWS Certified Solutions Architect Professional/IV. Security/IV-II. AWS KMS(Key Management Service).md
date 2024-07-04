# I. AWS KMS(Key Management Service)

## 1. AWS KMS(Key Management Service)
p 93

[https://docs.aws.amazon.com/kms/latest/developerguide/overview.html](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)

• 데이터에 대한 액세스를 쉽게 제어할 수 있는 AWS는 우리를 위해 키를 관리합니다

- AWS의 다양한 서비스에서 암호화를 사용할 때 대부분 AWS Key Management Service (KMS)를 활용하는 것이 일반적이다.
- 데이터를 보호하는 데 사용되는 암호화 키를 쉽게 만들고 제어할 수 있는 관리형 서비스이다.
- KMS는 권한 부여를 위해 IAM과 완전히 통합됩니다.
- 거의 모든 서비스와 통합되어 있다.
    - Amazon EBS: 볼륨 암호화
    - Amazon S3: 객체의 서버측 암호화
    - Amazon Redshift: 데이터의 암호화
    - Amazon RDS: 데이터의 암호화
    - Amazon SSM: 파라미터 스토어
- CLI/SDK 사용 가능: 필요에 따라 KMS API를 직접 호출하여 프로그래밍 방식으로 암호화 키를 관리할 수도 있다.

## 2. KMS(Key Management Service) 주요 유형
p 94

[https://velog.io/@chan9708/AWS-Certified-Solutions-Architect-Associate-18-AWS-Security-암호화](https://velog.io/@chan9708/AWS-Certified-Solutions-Architect-Associate-18-AWS-Security-%EC%95%94%ED%98%B8%ED%99%94)

1. 대칭 키(AES-256)
    - 단일 암호화 키로 암호화와 복호화에 사용된다.
    - KMS에서 제공하는 기본 키 유형이다.(KMS의 첫 제품)
    - KMS와 통합된 AWS 서비스에서 주로 사용된다.
    - 봉투 암호화(envelope encryption)에 필요하다.
        - [https://systorage.tistory.com/entry/암호화-기법-봉투-암호화-Envelope-encryption](https://systorage.tistory.com/entry/%EC%95%94%ED%98%B8%ED%99%94-%EA%B8%B0%EB%B2%95-%EB%B4%89%ED%88%AC-%EC%95%94%ED%98%B8%ED%99%94-Envelope-encryption)
    - 암호화되지 않은 KMS 키에 직접 액세스할 수 없으며, KMS API를 통해 사용해야 한다.
2. 비대칭 키(RSA, ECC):
    - 공개 키와 개인 키로 구성된 키 쌍이다.
    - 암호화/복호화 또는 서명/확인 작업에 사용된다.
    - 공개 키는 다운로드할 수 있지만, 암호화된 개인 키에는 직접 액세스할 수 없다.
    - AWS 외부에서 암호화가 필요한 경우 유용하다.
    - 사용 사례: KMS API를 호출할 수 없는 사용자에 의한 AWS 외부 암호화

## 3. KMS 키의 종류
p 95 - 96

[https://docs.aws.amazon.com/ko_kr/kms/latest/cryptographic-details/basic-concepts.html](https://docs.aws.amazon.com/ko_kr/kms/latest/cryptographic-details/basic-concepts.html)

1. **고객 관리 키**
    
    - 고객이 생성, 관리, 사용, 활성화/비활성화할 수 있다.
    - 로테이션 정책을 설정할 수 있다.
    - CloudTrail에서 주요 정책(자원 정책) 및 감사 추적이 가능하다.
    - 봉투 암호화에 활용할 수 있다.
2. **AWS 관리 키**
    
    - AWS 서비스에서 사용된다. (aws/s3, aws/ebs, aws/redsshift)
    - AWS에서 관리되며 1년마다 자동으로 순환된다.
    - CloudTrail에서 주요 정책 및 감사 내역을 볼 수 있다.
3. **AWS 소유 키**
    
    - AWS에서 생성 및 관리하며, 일부 AWS 서비스에서 리소스를 보호하는 데 사용된다.
    - 여러 AWS 계정에서 사용될 수 있지만, 계정에 없다.
    - 보고, 사용, 추적 또는 감사할 수 없다.

| KMS Key           | Customer Managed Key         | AWS Managed Key                                | AWS Owned Key |
| ----------------- | ---------------------------- | ---------------------------------------------- | ------------- |
| KMS 키 메타데이터 보기 가능 | O                            | O                                              | X             |
| KMS 키 관리 가능       | O                            | X                                              | X             |
| 내 AWS 계정에만 사용됨    | O                            | O                                              | X             |
| 자동 로테이션           | 선택 (매년, 약 365일)              | 필수 (매년, 약 365일)                                | 다양            |
| 요금                | 월 요금(시간당 비례 배분)<br>사용량 기준 요금 | 월 요금 없음<br>사용량 기준 요금<br>(일부 AWS 서비스는 고객 대신 납부) | 요금 없음         |

## 4. KMS Key 자료 출처에 따른 3가지 유형
p 97

- 키 자료 출처는 키 생성 시 선택하며, 생성 후에는 변경할 수 없다.
- 각 유형에 따라 키 관리의 책임과 통제 수준이 달라진다.

1. **KMS (AWS_KMS) - 기본값**
    - AWS KMS가 자체 키 저장소에서 키 자료를 생성하고 관리한다.
2. **External (EXTERNAL)**
    - 키 자료를 외부에서 가져온다.
    - 사용자가 AWS 외부에서 키 자료를 보호하고 관리할 책임이 있다.
3. **Custom Key Store (AWS_CLOUDHSM)**
    - AWS KMS가 사용자 지정 키 저장소(CloudHSM Cluster)에 키 자료를 작성한다.

# II. KMS Key Source
## 1. Custom Key Store(**사용자 지정 키 스토어,** CloudHSM)
p 98

[https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/keystore-cloudhsm.html](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/keystore-cloudhsm.html)

- KMS와 CloudHSM 클러스터를 통합하여 사용자 지정 키 저장소로 사용한다.
- 핵심 자료(키 자료)는 고객이 소유하고 관리하는 CloudHSM 클러스터에 저장된다.
- 암호화 작업은 HSM(Hardware Security Module) 내에서 수행된다.
- 사용자 지정 키 저장소를 사용하는 주요 사용 사례
    - HSM을 직접 제어해야 하는 경우
    - KMS 키를 전용 HSM에 저장해야 하는 경우

사용자 지정 키 저장소를 통해 고객은 키 자료에 대한 더 강력한 통제와 관리를 할 수 있다. 하지만 이를 위해서는 CloudHSM 클러스터의 운영과 관리에 대한 책임이 고객에게 있다.

> **작동 원리**
> 
> ![[CloudHSM.png]]
> 
> 1. [활성 AWS CloudHSM 클러스터](https://docs.aws.amazon.com/cloudhsm/latest/userguide/getting-started.html)를 생성하거나 기존 클러스터를 선택한다. 클러스터는 서로 다른 가용 영역에 최소 2개의 활성 HSM을 가지고 있어야 한다. 그런 다음 해당 클러스터에 AWS KMS에 대한 [전용 암호화 사용자(CU) 계정](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/hsm-key-store-concepts.html#concept-kmsuser)을 만든다.
> 2. 위에서 AWS KMS선택한 AWS CloudHSM 클러스터와 연결된 [사용자 지정 키 저장소를 생성](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/create-keystore.html)한다. AWS KMS 사용자 지정 키 스토어를 만들고, 보고, 편집하고, 삭제할 수 있는 [완전한 관리 인터페이스를](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/manage-keystore.html) 제공한다.
> 3. 사용자 지정 키 스토어를 사용할 준비가 되면 [관련 AWS CloudHSM 클러스터에 연결](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/disconnect-keystore.html)한다. AWS KMS 연결을 지원하는 데 필요한 네트워크 인프라를 생성한다. 그런 다음, 클러스터에서 키 구성 요소를 생성 및 관리할 수 있도록 전용 CU(Crypto User) 계정 자격 증명을 사용해 클러스터에 로그인한다.
> 4. 이제 [사용자 지정 키 스토어에서 대칭 암호화 KMS 키를 생성](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/create-cmk-keystore.html)할 수 있다. KMS 키를 생성할 때 사용자 지정 키 스토어를 지정만 하면 된다.

## 2. External
p 99

### **외부 키 자료를 KMS 키로 가져오는 BYOK(Bring Your Own Key) 기능**

- 자신의 키 자료를 KMS 키로 가져올 수 있다. BYOK(Bring Your Own Key)
- AWS 외부에서 키 자료의 보안, 가용성, 내구성에 대한 책임이 사용자에게 있다.
- 대칭 및 비대칭 KMS 키 모두 BYOK 방식으로 가져올 수 있다.
- 사용자 지정 키 저장소(CloudHSM)에서는 BYOK를 사용할 수 없다.
- KMS 키에 대한 수동 키 회전은 지원되지만, 자동 키 회전은 지원되지 않는다.

BYOK를 사용하면 고객이 키 자료에 대한 더 강력한 통제력을 가질 수 있다. 하지만 키 자료 관리에 대한 책임이 고객에게 있다는 점을 유의해야 한다. 또한 사용자 지정 키 저장소와는 호환되지 않는다는 점도 고려해야 한다.

> **작동 원리**
> 
> ![[External.png]]
> 
> **고객이 외부에서 생성한 키 자료를 KMS로 가져오는 BYOK(Bring Your Own Key) 프로세스**
> 
> 1. 고유한 키 구성 요소를 대신 가져오려면, 키 구성 요소 없이 KMS 키를 만든다.(기본적으로, KMS 키를 만들면 AWS KMS가 자동으로 키 구성 요소를 생성)
>     [# 1단계: 키 구성 요소 없이 AWS KMS key 생성](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/importing-keys-create-cmk.html)
>     
> 2. [키 자료가 AWS KMS key 없는 파일을 만든](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/importing-keys-create-cmk.html) 후에는 AWS KMS 콘솔 또는 [GetParametersForImport](https://docs.aws.amazon.com/kms/latest/APIReference/API_GetParametersForImport.html)API를 사용하여 래핑 공개 키와 해당 KMS 키에 대한 가져오기 토큰을 다운로드한다.
>     [# 2단계: 래핑 퍼블릭 키 및 가져오기 토큰 다운로드](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/importing-keys-get-public-key-and-token.html)
>     
> 3. 다운로드한 퍼블릭 키와 지정한 래핑 알고리즘을 사용하여 키 구성 요소를 암호화한다.
>     [# 3단계: 키 구성 요소 암호화](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/importing-keys-encrypt-key-material.html)
>     
> 4. AWS KMS key와 함께 사용할 키 구성 요소를 가져올 수 있다. 키 구성 요소를 가져오려면 3단계에서 암호화한 키 구성 요소와 2단계에서 다운로드한 가져오기 토큰을 업로드한다.
>     [# 4단계: 키 구성 요소 가져오기](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/importing-keys-import-key-material.html)

## 3. Multi-Region Keys(**다중 지역 키)**
p 100 - 101

![[Multi-RegionKeys.png]]

1. KMS에서는 특정 리전(ex. us-east-1)에 키를 생성할 수 있다.
2. 이렇게 생성된 키를 다른 리전(ex. us-west-2, eu-west-1, ap-southeast-2)에 복제할 수 있다.
3. 복제된 키는 동일한 키 자료와 키 ID를 가지게 된다.
4. 따라서 다른 리전에서도 동일한 키를 사용할 수 있게 된다.

이를 통해 고객은 다중 리전에 걸쳐 동일한 키를 사용할 수 있다. 이는 재해 복구 및 고가용성을 위해 유용하다. 또한 리전 간 데이터 복제 시에도 동일한 키를 사용할 수 있어 편리하다.

### KMS의 다지역 키 기능에 대한 핵심 내용

- 서로 다른 AWS 리전에서 동일한 KMS 키 세트를 서로 교환하여 사용할 수 있다.(~ 여러 영역에서 동일한 KMS 키)
- 한 리전에서 암호화하고 다른 리전에서 암호화 해제할 수 있다.(지역 간 API 호출을 다시 암호화하거나 수행할 필요 없음)
- 다중 지역 KMS 키는 동일한 키 ID, 키 자료, 자동 회전 등의 특성을 가집니다.
- KMS 다중 지역 기능은 글로벌하지 않고, 기본 키와 복제본의 관계로 구성됩니다.
- 각 다지역 키는 독립적으로 관리되며, 한 번에 하나의 기본 키만 복제본을 승격할 수 있습니다.
- 주요 사용 사례: 재해 복구, 글로벌 데이터 관리(예: DynamoDB 글로벌 테이블), 액티브-액티브 애플리케이션, 분산 서명 애플리케이션 등

# III. SSM(AWS Systems Manager) Parameter Store

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

# IV. AWS Secrets Manager

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