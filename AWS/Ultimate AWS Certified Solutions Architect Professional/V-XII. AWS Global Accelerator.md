
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
