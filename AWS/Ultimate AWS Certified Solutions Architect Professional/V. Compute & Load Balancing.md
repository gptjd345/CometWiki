p 175
![[Solution-Architecture-on-AWS.png]]

# Solution Architecture on AWS
사용자가 응용 프로그램에 엑세스할 때 접속하는 순서

## 1. DNS Layer
* *Route 53*
## 2-1. CDN Layer
* *CloudFront*
- 정적 콘텐츠는 전 세계에 확산되고 CDN 계층, 콘텐츠 전송 네트워크를 통해 캐싱된다.
- CloudFront는 다음과 같은 방식으로 작동할 수 있다:
    1. **S3나 Glacier에서 정적 콘텐츠를 가져오기**: 기본 오리진 S3 사용
        
        일반적인 사용 사례이다. CloudFront는 S3 버킷이나 Glacier에 저장된 콘텐츠를 캐싱하여 전 세계 사용자에게 빠르게 제공한다.
        
        - User → CDN Layer → Static Assets Layer
    2. **사용자 지정 소스에서 콘텐츠 가져오기**: 커스텀 오리진 사용 → 동적 콘텐츠 전송에 사용
        
        사용자가 지정한 다른 서버나 데이터베이스에서 콘텐츠를 가져올 수 있습니다. 예를 들어, 사용자가 운영하는 웹 서버나 API 서버에서도 콘텐츠를 가져와 캐싱하고 배포할 수 있습니다.
## 2-1. Web Layer
- _CLB, ALB, NLB, API Gateway, Elastic IP_
- 웹 계층의 역할은 다음과 같다:

1. **사용자 요청 수신**: 사용자가 웹 브라우저나 모바일 앱을 통해 보낸 요청을 수신한다.
2. **동적 콘텐츠 처리**: 필요한 경우 동적인 콘텐츠를 처리한다.
3. **부하 분산 (Load Balancing)**: 여러 서버로 트래픽을 분산하여 특정 서버에 과부하가 걸리지 않도록 관리한다.
4. **요청 전달**: 수신한 요청을 컴퓨팅 계층으로 전달하여 실제 데이터 처리나 비즈니스 로직이 실행되도록 한다.
5. **응답 반환**: 컴퓨팅 계층에서 처리된 결과를 다시 사용자에게 반환한다.
* *EC2, ASG, Lambda, ECS, Fargate, Batch, EMR*
## 3. Compute Layer
- _EC2, ASG, Lambda, ECS, Fargate, Batch, EMR_

## 4-1. Caching / Sesstion Layer
- _ElastiCache, DAX, DynamoDB, RDS_

## 4-2. Database Layer
- _RDS, Aurora, DynamoDB ElasticSearch, S3, Redshift_

## 4-3. Decoupling Orchestration Layer
- _SQS, SNS, Kinesis Amazon MQ, Step Functions_

## 4-4. Storage Layer
- _EBS, EFS, Instance Store_

## 4-5. Static Assets Layer
- _(storage) S3, Glacier_



<font color="#92d050">////////////////////////////////////////////////////////////////////////////////////////</font>


