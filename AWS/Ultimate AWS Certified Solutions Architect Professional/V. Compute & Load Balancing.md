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

# V-VII. AWS Lambda - Part 1
p 225

Lambda의 한계
* 10GB RAM
* CPU는 RAM과 붙어있음
* Timeout 은 15분(15분 이상 걸리는 작업 비적합)
* /tmp Storage(임시저장소) 10GB
* 계정당 동시 실행 수 1000건 (약간 늘릴수는 있음)
* 람다의 배포 패키지 크기 50MB(압축시), 250MB(비압축시)
* 컨테이너 이미지 크기 10GB
* 호출 페이로드(Invocation Payload) 제한
	동기호출(Request와 Response 모두)에 대한 Payload 크기는 각각 6MB로 제한됨.
	비동기호출(Event)에 대한 Payload 크기는 256KB 
	
 >[!question] 비동기호출이란?
 >HTTP 를 기반한 요청이 아닌, AWS 서비스(S3, DynamoDB, Kinesis등)에서 발생한 이벤트 혹은 애플리케이션 코드에서 AWS SDK를 사용하여 Lambda 함수를 호출하는 경우를 말함.
 >
	

##### Lambda Concurrency and Throttling
1. 1계정당 1000개 Lamda 함수를 동시에실행가능하지만 보통 계정수준의 제한을 하나의 람다함수에서 쓰지는 않음. 
2. 개별 람다 함수의 Concurrency 한도를 따로 설정하는 편임. 
3. 개별 람다함수의 Concurrency(동시실행) 한도를 초과하는 경우 초과되는 람다함수 처리요청은 무시되고 ThrottlingException을 발생시킨다. 
 >[!question] Throttling?
 >Throttling이라는 용어는 원래 물리적인 목 조르기나 질식을 의미하다가, 점차 유량 제어 등의 기계적 의미로 확장되어 사용되어왔음.  현재 컴퓨터 시스템에서 리소스 사용을 제한하는 의미로 사용되고 있음. 
 >

##### Lambda & CodeDeploy
CodeDeploy를 사용하면 자동적으로 Lambda 함수들에 대한 트래픽 분배가 가능함

#### AWS Lambda - Part 2

##### Lambda In a VPC
![[Pasted image 20240618203140.png]]
Lambda가 private subnet의 RDS 에 접근하려면 같은 Private Subnet 에 존재해하며 보안그룹을 설정해야함. Lambda가 인터넷과 통신해야한다면 NAT 를 통해 IGW(Internet Gateway)를 거쳐야한다.
DynamoDB가 public 한 공간에 존재하지만 모든 트레픽을 private 하게 관리하고싶다면 람다가 접근하려는 대상에 EndPoint를 두면 됨.

##### Lambda - Fixed Public IP for external comms
VPC 설정없이 AWS의 Public Cloud에 람다함수를 배포하면 람다함수는 Random Public IP를 받게 된다. 

![[Pasted image 20240618225821.png]]

그러나 아래처럼 람다함수를 private subnet에 둬야한다면(위에서 처럼 private RDS 에 연결해야하거나 해서) NAT gateway를 사용하고 NAT gateway를 사용할때 고정 IP를 부착한다. 

##### Lambda - Synchronous Invocations
CLI, SDK, API Gateway 를 통해 람다 함수에 대해 동기호출이 이루어진다.
SDK의 경우 비동기 와 동기 방식 모두 호출이 가능하다(invoke()함수는 동기식, invokeAsync()함수는 비동기식으로 동작)

![[Pasted image 20240618230809.png]]

##### Lambda - Asynchronous Invocations
S3, SNS, Amazon EventBridge 등에 의해 비동기 호출이 이루어진다.
* AWS Lambda는 함수 호출 실패 시 자동으로 최대 3번까지 재시도한다.
* 3번의 재시도 후에도 실패하면 Lambda는 이벤트를 포기하고 다음 이벤트를 처리한다.

Dead Letter Queue (DLQ)
DLQ는 Lambda 함수 실행에 실패한 이벤트를 저장하는 Amazon SQS or SNS 대기열이다. 
* 3번 재시도 했는데 실패한 이벤트를 나중에 분석하고 수동으로 처리가능
* Lambda 함수에 대한 오류처리 및 모니터링가능 

##### Lambda - Architecture Discussion

**S3 - SNS - Lambda 아키텍처**
![[Pasted image 20240618233640.png]]
- S3 버킷에서 이벤트가 발생하면 SNS 로 알림이 전송됩니다.
- SNS는 Lambda 함수를 트리거하여 이벤트를 처리합니다.
- 이 아키텍처는 간단하고 직접적인 방식으로, 이벤트 처리 지연이 최소화됩니다.
- SNS 에 DLQ를 설정 할 수 있지만, 메시지 버퍼링 기능이 제한적이다(SNS는 메시지를 실시간으로 전송하는 발행-구독모델이기때문)
- 메시지 처리 실패 시 재처리에 한계가있음(실시간 발행-구독모델이기 떄문)

**S3 - SNS - SQS - Lambda 아키텍처**
![[Pasted image 20240618233654.png]]
- S3 버킷에서 이벤트가 발생하면 SNS 에 의해 알림이 전송된다. 
- SNS에 의해 전송된 알림은 SQS 대기열에 버퍼링 된 후 전달된다. 
- SQS 대기열은 Lambda 함수를 트리거하여 이벤트를 처리합니다.
- SQS에서 DLQ를 설정해 실패한 건에 대해 재처리하기 용이하다. 
- 하지만 이벤트 처리 지연이 증가할 수 있습니다.

