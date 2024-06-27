
# I. AWS Global Accelerator
p 301 - 302

- AWS 내부 네트워크를 활용하여 애플리케이션으로 라우팅
- 응용 프로그램을 위해 2개의 Anycast IP가 생성됨
- Anycast IP는 트래픽을 Edge Location으로 직접 전송함
- Edge locations는 트래픽을 응용 프로그램으로 전송함

>[!question] AWS Global Accelerator가 내부적으로 Anycast IP를 사용하는 이유
>AWS Global Accelerator는 여러 AWS 리전에서 실행되는 애플리케이션의 가용성과 성능을 향상시키도록 설계된 서비스이다. Global Accelerator를 생성하면 애플리케이션 트래픽의 진입점(entry point)으로 사용할 수 있는 두 개의 Anycast IP 주소가 제공된다. 이러한 Anycast IP 주소는 BGP(Border Gateway Protocol)를 통해 인터넷을 통해 공지된다. 즉, 여러 AWS Edge Location에서 공지된다.
>
>트래픽의 흐름은 다음과 같다.
>![[Pasted image 20240627113307.png]]
>1. 클라이언트는 애플리케이션에 액세스하기 위한 요청을 시작한다. 요청은 애플리케이션 트래픽의 진입점(entry point)으로 구성한 Anycast IP 주소로 전송된다.
>2. Anycast IP 주소는 BGP를 통해 인터넷에 알려진다. 클라이언트가 Anycast IP 주소로 요청을 보내면 BGP는 Anycast IP 주소를 알리는 가장 가까운 AWS Edge Location으로 트래픽을 라우팅한다.
>3. 트래픽은 클라이언트에 가장 가까운 AWS Edge Location에 도달한다. 이는 클라이언트와 AWS 네트워크 간의 첫 번째 접촉 지점이다.
>4. 그런 다음 트래픽은 애플리케이션과 연결된 Global Accelerator endpoint로 전송된다. Global Accelerator는 endpoint 그룹 구성에 따라 트래픽을 최적의 AWS 리전으로 라우팅한다.
>5. 트래픽은 구성한 AWS 리전의 애플리케이션에 도달한다. 그런 다음 응답은 동일한 경로를 통해 클라이언트로 다시 전송된다.
>	
>애니캐스트 IP 주소 사용의 주요 이점은 트래픽을 가장 가까운 AWS Edge Location으로 라우팅할 수 있어 지연 시간을 줄이고 성능을 향상시키는 데 도움이 된다는 것이다. 또한 Global Accelerator는 상태 확인을 사용하여 endpoint 의 상태를 모니터링하고 트래픽을 정상적인 endpoint로 자동 라우팅한다. 이는 애플리케이션의 가용성을 향상시키는 데 도움이 된다.
>[https://stackoverflow.com/questions/76178384/why-does-aws-global-accelerator-use-anycast-internally](https://stackoverflow.com/questions/76178384/why-does-aws-global-accelerator-use-anycast-internally)
>[https://aws.amazon.com/ko/global-accelerator/features/](https://aws.amazon.com/ko/global-accelerator/features/)

- Elastic IP, EC2 instances, ALB, NLB, public 또는 private와 작동함
- NLB 및 EIPs endpoints를 제외한 클라이언트 IP 주소 보존 지원

>[!question] AWS Global Accelerator를 시작하려면 어떻게 해야 합니까?
 >API를 사용하거나 AWS Management Console을 통해 또는 AWS CloudFormation 템플릿을 사용하여 AWS Global Accelerator 설정을 시작할 수 있다. AWS Global Accelerator는 글로벌 서비스이므로 특정 AWS 리전에 한정되지 않는다.
 >- **애플리케이션에 맞게 AWS Global Accelerator를 설정하는 3가지 간단한 단계**
 >1. <u>액셀러레이터 생성</u>: 액셀러레이터를 생성하면 AWS Global Accelerator가 이와 연결된 고정 IP 주소를 프로비저닝한다. 그런 다음 고객이 지정하는 프로토콜 및 포트를 기반으로 최종 클라이언트에서 액셀러레이터로 인바운드 연결을 처리할 수 있도록 하나 이상의 리스너를 구성한다.
 >2. <u>엔드포인트 그룹 구성</u>: 트래픽을 배포할 AWS 리전을 지정함으로써 액셀러레이터의 리스너에 연결할 하나 이상의 리전별 엔드포인트 그룹을 선택한다. 리스너는 이 엔드포인트 그룹에 등록된 엔드포인트로 요청을 라우팅한다. AWS Global Accelerator는 각 엔드포인트에 정의된 상태 확인 설정을 사용하여 그룹 내 엔드포인트의 상태를 모니터링한다. 엔드포인트 그룹이 수락하는 트래픽 양을 제어하는 트래픽 다이얼 비율을 각 엔드포인트 그룹에 대해 구성할 수 있다. 기본적으로 트래픽 다이얼 비율은 모든 리전별 엔드포인트 그룹에 대해 100%로 설정되어 있다.
 >3. <u>엔드포인트 그룹에 엔드포인트 등록</u>: 각 엔드포인트 그룹에 Application Load Balancer, Network Load Balancer, EC2 인스턴스 또는 탄력적 IP 주소와 같은 리전별 리소스를 하나 이상 등록한다. 그런 다음 각 엔드포인트로 라우팅할 트래픽 양을 선택한다.
 >[https://aws.amazon.com/ko/global-accelerator/faqs/](https://aws.amazon.com/ko/global-accelerator/faqs/)
 
- **Consistent Performance (일관된 성능)**
    - 가장 낮은 지연 시간과 빠른 지역 페일오버를 위한 지능형 트래픽 라우팅
    - 클라이언트 캐시 문제 없음(IP가 변경되지 않기 때문 → 2개의 Anycast IP 사용 )
    - 내부 AWS 네트워크
- **Health Checks**
    - Global Accelerator는 애플리케이션의 Health Check를 수행
    - 애플리케이션을 글로벌화하는 데 도움이 됨(unhealthy의 경우 1분 이내에 장애 발생)
    - 재해 복구에 탁월함(Health Check 덕분에)
- **Security (보안)**
    - 2개의 외부 IP(Anycast IP)만 화이트리스트에 추가하면 됨
    - AWS Shield 덕분에 DDoS 공격에서 보호됨

## AWS Global Accelerator vs CloudFront
p 303

- 둘 다 AWS 글로벌 네트워크를 사용하고 전 세계에 분포된 해당 edge location을 활용함
- 두 서비스 모두 DDoS 공격을 막기 위해 AWS Shield와 통합되어 있음
    
- **CloudFront**
    - 캐시 가능한 콘텐츠(예: 이미지 및 비디오)의 성능 향상
    - 동적인 콘텐츠(API 가속화 및 동적 사이트 제공 등)의 성능 개선
    - 콘텐츠는 edge에서 제공됨
	    
- **Global Accelerator**
    - TCP 또는 UDP를 통한 광범위한 애플리케이션의 성능 향상
    - edge에서 패킷을 단일 또는 여러 AWS 리전에서 실행되는 애플리케이션으로 프록시하여 TCP 또는 UDP를 통해 광범위한 애플리케이션의 성능을 개선
    - 게임(UDP), IoT(MQTT) 또는 Voice over IP와 같은 HTTP가 아닌 사용 사례에 적합함
    - static IP 주소가 필요한 HTTP 외 사용 사례에 적합함
    - 정적 IP 주소 또는 결정적 빠른 지역 페일오버가 필요한 HTTP 사용 사례에도 적합함

[https://aws.amazon.com/ko/global-accelerator/faqs/](https://aws.amazon.com/ko/global-accelerator/faqs/)

# II. Solution Architecture Comparisons

p 304

1. EC2 on its own with Elastic IP
2. EC2 with Route53
3. ALB + ASG
4. ALB + ECS on EC2
5. ALB + ECS on Fargate
6. ALB + Lambda
7. API Gateway + Lambda
8. API Gateway + AWS Service
9. API Gateway + HTTP backend (ex: ALB)

## 1. EC2 with Elastic IP
p 305

![[EC2withElasticIP.png]]
1. 클라이언트는 Public IP(Elastic IP)로 EC2 instance에 접근한다.
2. Public EC2 instance는 failover를 위해 대기 인스턴스를 가지고 있다.
3. 재해 복구(Disaster Recovery, DR)를 원하는 경우 첫 번째 EC2 instance의 Elastic IP Address를 두 번째 EC2 instance로 움직이게 한다.
4. 클라이언트는 Elastic IP를 사용해 자동으로 Standby Instance로 리디렉션된다.

### 특징
- 빠른 failover
- Elastic IP 주소는 변경되지 않기 때문에, 고객 입장에서는 서비스 중단 없이 장애 조치가 이루어지는 것을 알 수 없음
- 클라이언트가 static Public IP 주소를 필요로 하는 경우, AWS Global Accelerator를 활용하면 IP 주소 변경 없이 안정적이고 빠른 서비스를 제공
- Elastic IP는 고정 IP 주소를 제공하지만, 확장성에는 제약됨
    - 새로운 인스턴스를 추가하거나 장애 조치를 수행할 때마다 새로운 Elastic IP를 할당해야 함
- 저렴함

## 2. EC2 with Route53

### Stateless web app - scaling horizontally(scaling out)
p 306 - 307

![[Statelesswebapp-scalinghorizontally.png]]

### 종료된 EC2 instance가 존재하지 않는 경우

1. 웹 애플리케이션이 상태 비저장(stateless)이라고 가정한다. 즉, 각 요청은 독립적이며 인스턴스 간에 상태를 공유하지 않는다.
2. 현재 3개의 EC2 인스턴스가 있으며, 각 인스턴스는 Public EC2 instance이며 Elastic IP가 없는 상태이다. 대신 클라이언트는 Route53의 DNS 쿼리를 통해 A 레코드를 받아 EC2 인스턴스와 통신한다. (TTL 1시간)

- 탄력적 IP를 릴리스하는 경우 탄력적 IP를 가리키는 DNS 레코드도 삭제해야 한다. 그렇지 않으면 DNS 레코드가 손상되어 권한 없는 사용자가 레코드를 조작하게 될 수 있다.

### EC2 instance 중 하나가 종료된 경우

클라이언트가 웹 애플리케이션에 정상적으로 접근하기 위해 필요한 것

1. 이 문제를 해결하기 위해 DNS 기반 부하 분산이 필요하다. (DNS 기반 로드 밸런싱)
2. DNS 기반 부하 분산은 Route53의 A 레코드를 통해 이루어진다. 각 클라이언트는 다른 IP 주소를 받게 되어 다른 인스턴스로 연결된다. (여러 인스턴스를 사용할 수 있음)
3. Route53의 TTL(Time To Live) 설정으로 인해, 일부 사용자는 오래된 정보를 받을 수 있다. 이를 해결하기 위해 건강 확인 등의 방법을 사용할 수 있다.
4. 클라이언트에서는 호스트 이름 분석 실패에 대한 처리 로직을 가지고 있어야 한다. 예를 들어 다른 URL을 시도하는 등의 대응이 필요하다.
5. 새로운 인스턴스가 추가되더라도 DNS TTL로 인해 즉시 전체 트래픽을 받지 못할 수 있다. 새 클라이언트가 DNS를 재해결하고 새 인스턴스로 트래픽을 받을 때까지 기다려야 한다.

## 3. ALB + ASG
p 308

![[ALB-ASG.png]]

1. Route53의 DNS 쿼리가 가명 레코드(alias record)를 통해 ALB의 DNS 이름을 확인하고 트래픽을 ALB로 라우팅할 수 있다.
2. ALB는 Health Check와 다중 AZ에서 활성화를 활용해, 3개의 가용성 영역으로 트래픽을 라우팅한다. 확장성이 뛰어난 고전적인 아키텍처이다.
3. 그림의 자동 스케일링 그룹에는 5개의 EC2 인스턴스가 있다. 새로운 인스턴스가 추가되면 바로 서비스에 투입되어 부하 분산이 이루어진다.
4. Health Check을 통해 서비스가 없는 인스턴스로는 트래픽이 전달되지 않는다.
5. 스케일링(확장) 시간이 다소 느릴 수 있지만(EC2 인스턴스 시작 + bootstrap), 사전에 준비된 AMI와 사용자 데이터 스크립트로 최적화할 수 있다.
6. ALB는 일반적인 트래픽 변화에 잘 대응할 수 있지만, 극심한 피크 현상에 대해서는 사전 준비가 필요하다. Pre-warming과 같은 기술을 통해 이러한 문제를 해결할 수 있다. Pre-warming은 ALB 인스턴스를 미리 시작하고 준비시켜 두는 것을 의미한다. 이렇게 하면 갑작스러운 트래픽 증가에도 ALB가 즉시 대응할 수 있다.
7. 인스턴스가 처리할 수 있는 용량을 초과하는 오버로드 상태가 되면 일부 요청이 손실될 수 있다.
8. CloudWatch 와 Cross-Zone 밸런싱을 활용해 효율적인 확장을 달성할 수 있다.
    1. ALB는 CloudWatch 지표를 사용하여 자동 확장을 수행한다. CloudWatch에서 모니터링되는 지표(CPU 사용률, 메모리 사용률 등)를 기반으로 ALB 용량을 자동으로 조정한다.
    2. Cross-Zone 밸런싱을 활성화하면 ALB는 모든 가용 영역에 걸쳐 트래픽을 균등하게 분산시킬 수 있다. 이를 통해 가용성과 탄력성을 높일 수 있다.
9. ALB의 목표 활용률은 일반적으로 40%에서 70% 사이가 적절하다. 이 범위를 유지하면 과도한 용량 낭비를 방지하면서도 적절한 여유 용량을 확보할 수 있다. 활용률이 너무 낮으면 비용 효율성이 낮아지고, 너무 높으면 오버로드 위험이 증가한다.

## 4. ALB + ECS on EC2 (backed by ASG)
p 309

![[ALB-ECSonEC2(ASG).png]]

1. ALB와 Auto Scaling Group(ASG)만 사용할 때와 위에서 말한 것과 동일한 자동 스케일링 속성을 가진다. (ALB + ASG와 동일한 속성)
2. 하지만 여기에 ECS(Elastic Container Service)가 추가되면서 상황이 달라진다.
    1. ECS를 통해 관리되기 때문에, 응용 프로그램이 Docker 컨테이너에서 실행되어야 한다. (응용 프로그램이 도커에서 실행)
    2. ECS는 동적 포트 매핑 기능을 사용하여 EC2 인스턴스에서 다중 작업을 실행할 수 있게 해준다. (ASG + ECS를 통해 동적 포트 매핑 가능)
3. 이 아키텍처에서는 ECS 서비스 자체의 자동 스케일링을 조정하는 것이 어려워진다. (ECS 서비스 자동 확장 + ASG 자동 확장 조정이 어려움)
    1. ASG를 통한 EC2 인스턴스 스케일링과 ECS 서비스 스케일링을 별도로 관리해야 한다.
4. 따라서 두 가지 자동 스케일링 규칙 세트가 필요할 수 있다.
    1. EC2 인스턴스 스케일링을 위한 ASG 규칙
    2. ECS 서비스 스케일링을 위한 별도의 규칙

이와 같이 ECS 환경에서는 ALB와 ASG 외에도 ECS 서비스 스케일링을 고려해야 하므로, 자동 스케일링 설정이 더 복잡해질 수 있다. 이를 효과적으로 관리하기 위해서는 두 가지 스케일링 규칙을 적절히 조합하는 것이 중요하다.

## 5. ALB + ECS on Fargate
p 310

![[ALB+ECSonFargate.png]]

1. Fargate를 사용하면 EC2 인스턴스 관리가 필요 없다. Fargate가 AWS 네트워크 내에서 자동으로 작업을 시작하고 관리한다.
2. 응용 프로그램은 여전히 Docker 컨테이너에서 실행된다.
3. Fargate의 서비스 자동 스케일링 기능을 사용하면 서비스 스케일링이 매우 쉽다. EC2 인스턴스를 미리 시작할 필요가 없다.
4. Docker 컨테이너를 빠르게 배포할 수 있어 수요 급증 시에도 대응이 용이하다. 그러나, 급작스러운 정점의 경우 ALB에 의해 여전히 제한이 있다.
5. ALB(Application Load Balancer)는 이 아키텍처에서 서버리스 계층이 아닌, 관리 계층으로 볼 수 있다. ALB는 잘 조정되며 안전하고 확장 가능한 솔루션이다.
6. 전반적으로 Fargate를 사용하면 EC2 인스턴스 관리 부담이 없어지고, 서비스 자동 스케일링이 매우 쉬워진다. 이는 매우 안전하고 확장 가능한 아키텍처라고 할 수 있다.

## 6. ALB + Lambda
p 311

![[ALB-Lambda.png]]

1. serverless에서는 AWS Lambda와 같은 서버리스 기술이 중요한 역할을 한다.
2. ECS(Elastic Container Service)의 애플리케이션을 AWS Lambda와 함께 사용할 수 있다. 이 경우 ALB(Application Load Balancer)의 타깃 그룹으로 Lambda 함수를 사용할 수 있다.
3. Lambda 함수를 ALB의 타깃으로 사용하면 Lambda 런타임에 제한이 있을 수 있다.
    1. 클라이언트 런타임과 고객 런타임을 구분해야 한다.
4. Docker가 없는 경우 ECS를 사용하면 접근성이 높아진다. Lambda 런타임 덕분에 균일한 확장성을 가질 수 있다.
    1. 1,000개의 Lambda 함수가 있다면 API Gateway를 사용하지 않고도 HTTP/HTTPS로 직접 Lambda 함수를 공개할 수 있다.
5. ALB와 Lambda 조합은 비용 절감 효과가 크지만, API Gateway의 기능은 제공하지 않는다.
6. ALB와 WAF(Web Application Firewall)을 결합하면 보안 강화에 도움이 된다.
7. 작업 유형에 따라 ECS(Elastic Container Service)와 Lambda(AWS Lambda)를 선택적으로 사용하는 하이브리드 마이크로서비스가 좋은 솔루션이 될 수 있다.
    1. 예: 일부 요청에는 ECS를 사용하고 다른 요청에는 Lambda를 사용

## 7. API Gateway + Lambda
p 312

![[APIGateway-Lambda.png]]

1. API Gateway와 Lambda를 함께 사용하면 더 많은 요청을 처리할 수 있지만, 그에 따른 비용 지불이 필요하다. (요청당 지불)
2. 서버리스 아키텍처를 통해 매끄러운 확장성을 제공할 수 있다.
3. API Gateway에는 소프트 제한이 있는데, 초당 1만 요청과 1000개의 동시 실행이 가능하다. 이는 잠재적 제한이며 더 늘릴 수 있다. (Soft limits: 10000/s API Gateway, 1000 concurrent Lambda)
4. API Gateway를 통해 인증, 속도 제한, 캐싱, API 키 관리 등 다양한 기능을 얻을 수 있다.
5. Lambda의 (Cold Start)콜드 스타트 시간이 대기 시간을 늘릴 수 있다. 특히 여러 서비스를 연결할 때 이 문제가 발생할 수 있다.
6. 하지만 X-Ray와의 통합을 통해 인프라 내에서 모든 요청을 추적할 수 있는 장점이 있다.

## 8. API Gateway + AWS Service (as a proxy)
p 313

![[APIGateway-AWSService(proxy).png]]

1. API Gateway는 AWS 서비스와 함께 프록시로 사용될 수 있다.
2. 클라이언트 - API Gateway - Lambda - SQS 구조보다는 클라이언트 - API Gateway - SQS (또는 SNS, Step Functions) 구조가 더 나은 아키텍처이다.
    1. 이 경우 Lambda 호출을 피할 수 있어 대기 시간이 짧아지고 비용도 절감된다.
        1. Lambda의 동시 실행 제한도 피할 수 있다.
        2. API Gateway만 관리하면 되므로 유지해야 할 사용자 지정 코드가 없다.
        3. API Gateway를 통해 AWS API 서비스를 안전하게 공개할 수 있다.
3. SQS 외에도 SNS나 Step Functions 등 다른 AWS 서비스를 활용할 수 있다.
4. 단, API Gateway의 페이로드 크기 제한(10MB)을 유의해야 한다.
    1. S3 프록시로 사용하려 한다면 이 제한이 문제가 될 수 있다.

## 9. API Gateway + HTTP backend (ex: ALB)
p 314

![[APIGateway-HTTPbackend.png]]

1. API Gateway를 사용하여 API 게이트웨이 기능을 얻을 때 이 아키텍처를 사용한다.
2. 사용자 지정 HTTP 백엔드(예: ALB) 위에서 API Gateway를 운영하면, 인증, 속도 제한, API 키 관리, 캐싱 등의 기능을 활용할 수 있다.
3. 온프레미스 서버, 응용 프로그램 로드 밸런서, 타사 HTTP 서비스 등과 연결 가능하다.

이처럼 API Gateway와 HTTP 백엔드를 조합하면 API Gateway가 제공하는 다양한 기능을 활용하면서도, 기존의 HTTP 인프라를 활용할 수 있다.

# III. AWS Outposts
p 315 - 316

https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/S3onOutposts.html

![[AWSOutposts.png]]

- **하이브리드 클라우드**
    - 기업이 온프레미스 인프라와 클라우드 인프라를 동시에 운영하는 방식
	    
- **두 가지 IT 시스템 관리 방식**
    - AWS 클라우드 관리: AWS 콘솔, CLI 및 AWS API 사용
    - 온프레미스 인프라를 위한 관리
	    
- **AWS Outposts**
    - AWS가 제공하는 온프레미스 인프라 솔루션
    - 클라우드와 동일한 온프레미스 인프라 솔루션을 구축하기 위해 AWS 인프라, 서비스, API 및 도구를 제공하는 "server racks"입니다
    - AWS가 “Outposts Rack”을 온프레미스에 설치 및 관리
    - 고객은 “Outposts Rack”의 물리적 보안을 책임
	    
- **혜택**
    - 온프레미스 시스템에 대한 낮은 지연 시간 액세스 가능
    - 온프레미스에서 로컬 데이터 처리 가능
    - 데이터 보유 - 데이터를 온프레미스에 유지할 수 있어 규제 요구 사항 충족 가능
    - 온프레미스에서 클라우드로의 마이그레이션 용이성
    - 완전 관리형 서비스 - AWS가 Outposts Rack의 설치, 운영, 관리를 책임지므로 고객은 이를 직접 관리할 필요 없음
	    
- **Outpost에서 작동하는 일부 서비스**

![[AWSOutposts2.png]]

## 1. S3 on AWS Outposts
317

- S3 API를 사용하여 AWS Outpost에서 로컬로 데이터 저장 및 검색
- 데이터와 온프래미스 애플리케이션의 근접성 → AWS 지역으로의 데이터 전송 감소
    - Outposts에 데이터를 저장함으로써, 온프레미스 애플리케이션과 가까운 곳에 데이터를 두어 AWS 클라우드로의 데이터 전송을 줄일 수 있다.
- S3 Outposts 스토리지 클래스
    - S3 Outposts에는 특별한 스토리지 클래스가 있으며, 이를 통해 온-프레미스에 데이터를 저장할 수 있다.
- utposts의 기본 설정은 SSE-S3(Server-Side Encryption with S3-Managed Keys)를 사용하여 데이터를 암호화한다.

### **AWS Outposts에서 데이터를 AWS 클라우드로 접근하는 방법**

[https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/S3OutpostsWorkingBuckets.html](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/S3OutpostsWorkingBuckets.html)

![[S3onAWSOutposts.png]]

- **S3 액세스 포인트 사용**
    1. S3 on Outposts에 데이터를 저장하려면 먼저 버킷을 생성한다.
    2. 버킷을 생성할 때 버킷 이름과 버킷을 저장할 Outpost를 지정한다.
    3. S3 on Outposts 버킷에 액세스하여 객체 작업을 수행하려면 다음으로 액세스 포인트를 생성하고 구성한다.
        1. 액세스 포인트는 모든 AWS 서비스 또는 S3에 데이터를 저장하는 고객 애플리케이션에 대한 데이터 액세스를 간소화한다.
        2. 액세스 포인트는 `GetObject` 및 `PutObject` 같은 객체 작업을 수행하는 데 사용할 수 있는 버킷에 연결된 네트워크 엔드포인트이다.
        3. 각 액세스 포인트에는 고유한 권한 및 네트워크 제어가 있다.
        4. 이를 통해 VPC에서 Outposts의 S3 스토리지에 쉽게 접근하고 보안을 관리할 수 있다.
    4. 액세스 포인트로 요청을 라우팅하려면 엔드포인트도 생성해야 한다.
        1. S3 on Outposts 엔드포인트를 사용하면 VPC를 Outposts 버킷에 프라이빗으로 연결할 수 있다.
        2. S3 on Outposts 엔드포인트는 S3 on Outposts 버킷에 대한 진입점의 가상 통합 자원 식별자(URI)이다. 수평으로 확장된 고가용성 중복 VPC 구성 요소이다.
        3. Outpost에 있는 각 Virtual Private Cloud(VPC)에는 연결된 엔드포인트가 하나씩 있을 수 있으며, Outpost당 엔드포인트를 최대 100개 보유할 수 있다.
        4. 이런 엔드포인트를 생성할 때 S3와 S3 on Outposts에서 동일한 작업이 작동하여 API 모델 및 동작을 동일하게 유지할 수 있다.
    5. 이를 통해 클라우드에서 직접 데이터에 액세스할 수 있다.
    
    AWS Management Console, AWS CLI, AWS SDK 또는 REST API를 사용하여 S3 on Outposts 버킷, 액세스 포인트 및 엔드포인트를 생성하고 관리할 수 있다. S3 on Outposts 버킷의 객체를 업로드하고 관리하려면 AWS CLI, AWS SDK 또는 REST API를 사용하면 된다.
    
- **데이터 동기화 서비스 사용**
    AWS 데이터 동기화 서비스를 이용하여 Outposts의 데이터를 AWS 클라우드의 Amazon S3 버킷으로 동기화할 수 있다.
    

이렇게 두 가지 방법을 통해 Outposts에 저장된 데이터를 AWS 클라우드로 접근할 수 있다. 이는 AWS Outposts 사용 시 고객이 활용할 수 있는 대표적인 예시이다.

# IV. AWS WaveLength
p 318

![[AWSWaveLength.png]]

# V. AWS Local Zones
p 319

![[AWSLocalZones.png]]

