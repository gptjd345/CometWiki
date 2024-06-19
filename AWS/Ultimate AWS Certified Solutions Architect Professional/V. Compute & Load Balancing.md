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

# V-VII. Amazon ECR & EKS

# I. Amazon ECR - Elastic Container Registry
p 214

![[Amazon-ECR.png]]

## 1. 도커 이미지 저장 및 관리

- AWS에서 도커 이미지를 저장하고 관리할 수 있는 서비스
- 도커 이미지를 안전하게 저장하고 필요할 때 쉽게 접근

## 2. 개인 및 공용 저장소

- **개인 저장소**: 사용자는 자신의 계정 내에서 비공개 저장소를 생성하여 도커 이미지를 저장
- **공용 저장소**: Amazon ECR Public Gallery([](https://gallery.ecr.aws/)[https://gallery.ecr.aws](https://gallery.ecr.aws))를 통해 누구나 접근 가능한 공용 저장소를 제공, 이를 통해 이미지 공유

## 3. ECS와 완벽한 통합

- Amazon ECS와 완벽하게 통합되어, 도커 이미지를 쉽게 배포하고 관리
- ECR에 저장된 이미지를 직접 가져와 컨테이너 생성 가능

## 4. IAM을 통한 액세스 제어

- AWS IAM(Identity and Access Management)을 통해 ECR에 대한 액세스 제어 가능 → 세밀한 권한 관리
- 권한 오류 발생 시, IAM 정책 확인 필요

## 5. 이미지 취약성 검색

- 저장된 도커 이미지에 대해 취약성 스캔을 지원하여 보안 취약점을 식별하고 대응 가능

## 6. 버전 지정 및 이미지 태그

- **버전 지정**: 각 이미지에 대해 버전 지정 가능, 특정 버전의 이미지를 쉽게 관리하고 사용
- **이미지 태그**: 이미지를 태그로 분류하여 쉽게 식별하고 관리

## 7. 이미지 라이프사이클 정책

- 오래된 이미지를 자동으로 삭제하여 저장소를 효율적으로 관리
- 저장소 공간을 절약, 비용 절감


# VIII. Amazon EKS - Elastic Kubernetes Service
p 215

![[Pasted image 20240617213658.png]][출처]https://www.devopsschool.com/blog/wp-content/uploads/2021/03/Amazon-Elastic-Kubernetes-Service-EKS-Explained-Diagram-1.png

#### AWS EKS - Node(EC2 instance) Types
1. Managed Node Groups
* EC2 인스턴스를 자동으로 생성, 업데이트, 패치, 종료 작업 전반을 관리해줌 
* EC2 인스턴스로는 On-Demand 나 Spot Instances를 지원한다. 

2. Self-Managed Nodes
* 사용자가 직업 EC2 인스턴스를 직접 생성하고 관리함
* Amazon EKS Optimized AMI를 사용하여 좀 편하게 쓸 수 있음. 
 >[!tip] Amazon EKS Optimized Amazon Machine Image(AMI)
 >kubernetes 노드를 실행하는데 필요한 모든 소프트웨어가 사전 설치 및 구성되어있음. AMI는 다양한 인스턴스 타입과 요구사항에 맞게 최적화된 여러 버전으로 제공되며, 정기적으로 최신보안 패치와 업데이트를 수행하고있음. 이걸 가져다가 직접 node를 생성하면 그나마 편함 
 >

3. AWS Fargate
아무것도 관리하기 싫을 때 편하게 사용가능

#### Amazon EKS - Data Volumes

EKS에서 사용가능한 스토리지 유형
* Amazon EBS
* Amazon EFS (works with Fargate)
* Amazon FSx for Lustre
* Amazon FSx for NetApp ONTAP


#### AWS App Runner VS AWS Fargate

1. **비용 측면**
    - App Runner는 일반적으로 작은 워크로드와 긴 유휴 기간에 더 비용 효율적입니다.
    - Fargate는 더 복잡한 워크로드와 높은 활용도에 더 적합할 수 있습니다.
2. **기능 및 유연성 측면**
    - Fargate는 더 많은 기능과 유연성을 제공합니다. 예를 들어 사용자 지정 네트워킹, 스케일링 정책 등을 설정할 수 있습니다.
    - App Runner는 더 간단한 인터페이스를 제공하지만, Fargate만큼 많은 기능을 제공하지 않습니다. ex) Fargate는 여러 CloudWatch metric (CPU, network I/O, requests ...) 에 대한 스케일링 정책 설정가능 . 

App Runner Multi-Region Architecture
![[Pasted image 20240618194149.png]]

1. DynamoDB를 사용하는 App Runner Service를 만든다 
2. App Runner Service에서 만든 image를 ECR Registry 에 등록한다. 
3. DynamoDB는 Global Table Replication 을 통해 리전간 복제가 가능하다. 
4. ECR Registry는 Cross-Region Replication을 통해 리전간 복제가 가능하다 
5. App Runner를 복제해서 멀티 리전 구성할 수 있다. 
6. Route 53를 이용해 각 리전의 App Runner Service에 요청을 분배하도록 한다. 

#### Amazon ECS Anywhere &  EKS Anywhere

이거 왜쓰는지 이해안됨 
온프레미스에서 로드밸런싱 다되어서 구성되어있는 환경에서
굳이 이걸 쓰는 이유를 납득 못하겠음. 알면 정리좀 

#### AWS Lambda - Part 1

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
