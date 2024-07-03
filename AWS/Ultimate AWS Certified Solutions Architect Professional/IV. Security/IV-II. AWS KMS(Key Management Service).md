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
