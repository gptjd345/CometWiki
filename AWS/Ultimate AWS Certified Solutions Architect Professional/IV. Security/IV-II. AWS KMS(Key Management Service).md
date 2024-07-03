# I. AWS KMS(Key Management Service)
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

## 1. KMS(Key Management Service) 주요 유형
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

## 2. KMS 키의 종류
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

## 3. KMS Key 자료 출처에 따른 3가지 유형
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

