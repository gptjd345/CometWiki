

[https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/what-is-load-balancing.html](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/what-is-load-balancing.html) ****

[https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html](https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html)

# I. AWS의 로드 밸런서 유형

p 239

AWS는 4가지 종류의 관리 로드 밸런서를 보유하고 있다.

1. **Classic Load Balancer**(v1 - 구세대) - 2009 - CLB
    - HTTP, HTTPS, TCP, SSL(secureTCP)
2. **Application Load Balancer**(v2 - 신세대) - 2016 - ALB
    - HTTP, HTTPS, WebSocket
3. **Network Load Balancer**(v2 - 신세대) - 2017 - NLB
    - TCP, TLS(secureTCP), UDP
4. **Gateway Load Balancer** - 2020 - GWLB
    - 계층 3(네트워크 계층)에서 작동 - IP Protocol

- 전반적으로 더 많은 기능을 제공하기 때문에 최신 세대 로드 밸런서를 사용하는 것이 좋다
- 일부 로드 밸런서는 내부(private) 또는 외부(public) ELB로 설정할 수 있다.

>[!question] Health Check란?
> Load Balancer의 Health Check는 서버나 서비스의 상태를 확인하여 정상적으로 작동하는지 여부를 판단하는 과정을 말한다. 일반적으로 다음과 같은 지표들을 검사한다. 
> 1. <font color="#9bbb59"> HTTP 상태 코드</font>: 서버가 올바른 HTTP 응답 코드를 반환하는지 확인. 보통 200번대 응답 코드는 정상으로 간주됨.
> 2. <font color="#9bbb59">응답 시간</font>: 서버가 요청에 대해 응답하는 데 걸리는 시간을 측정. 일정 시간 내에 응답하지 않으면 비정상으로 간주됨.
> 3. <font color="#9bbb59">TCP 연결</font>: TCP 연결이 성공적으로 이루어지는지 확인. TCP 핸드셰이크가 실패하면 비정상으로 간주됨.
> 4. <font color="#9bbb59">애플리케이션 수준의 검사</font>: 특정 URL이나 엔드포인트에 대한 요청을 통해 서비스가 정상적으로 작동하는지 확인. 
> 	- ex) 데이터베이스 연결 상태나 특정 기능이 정상 동작하는지 확인
> 5. <font color="#9bbb59">콘텐츠 검사</font>: 응답 본문에 특정 문자열이나 패턴이 포함되어 있는지 확인.
> 	- ex) 응답에 "OK"라는 문자열이 포함되어 있는지 검사.
> 이 외에도 로드 밸런서나 애플리케이션의 특성에 따라 추가적인 지표를 설정하여 Health Check를 수행할 수 있습니다.
 
## 1. **Classic Load Balancers (v1)**

p 240

[https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html)

![[Classic-Load-Balancers.png]]

- Health Checks는 SSL을 포함, HTTP(L7) 또는 TCP(L4) 기반일 수 있다.
- 하나의 SSL 인증서만 지원한다.
    - SSL 인증서는 많은 SAN(Subject Alternate Name)을 가질 수 있지만, SAN을 추가/편집/제거할 때마다 SSL 인증서를 변경해야 한다.
    - 가능하면 ALB를 SNI(Server Name Indication)와 함께 사용하는 것이 좋다.
    - 별개의 SSL 인증서를 원하는 경우 여러 CLB를 사용할 수 있다.
- TCP => TCP는 모든 트래픽을 EC2 인스턴스로 전달한다.
    - 2-way(양방향) SSL 인증을 사용하는 유일한 방법

 >[!question] SAN(Subject Alternative Name)?
 >SAN을 사용하면 하나의 인증서로 여러 호스트 이름을 보호할 수 있다. 
 >그러나 하나의 인증서 교체시 여러 도메인을 사용하는 서비스에 영향이 갈수 있어서
 >기본적으로 하나의 도메인은 하나의 SSL을 가지도록 하는 것을 기본으로 한다. 
 
## 2. **Application Load Balancer (v2)**

p 241

[https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

- Application Load Balancer는 OSI(Open Systems Interconnection) 모델의 7계층(HTTP)인 애플리케이션 계층에서 작동한다.
- 시스템 간에 여러 HTTP 애플리케이션에 대한 로드 밸런싱(target groups 대상 그룹)
- 동일한 시스템의 여러 애플리케이션(예: 컨테이너)에 대한 로드 밸런싱
    - ECS에 적합하며, 동적 포트 매핑 기능이 있다.
- HTTP/2 및 WebSocket 지원
- 리디렉션 지원
    - ex) ALB level 에서 HTTP -> HTTPS로 직접 리디렉션 할 수 있음
    - ex) 잘못요청된 URL을 다른 URL로 리디렉션
- path, header,query string에 대한 라우팅 규칙 설정 가능
    - ex) 개발환경과 운영환경을 분리하여 관리할 때 도메인(Host header)에 따라 개발환경 또는 운영환경의 타겟 그룹으로 트레픽을 라우팅 할 수 있음.
    - ex) 모바일/PC 환경 분리
    - ex) 동적으로 DNS 이름을 제공
    - 기본 라우팅 알고리즘: 라운드 로빈

### HTTP 기반 트래픽

![[HTTP기반트래픽.png]]
### **Target Groups**

- EC2 instances(Auto Scaling Group에서 관리 가능) - HTTP
- ECS tasks(ECS 자체에서 관리) - HTTP
- Lambda functions - HTTP 요청이 JSON 이벤트로 변환된다
- IP Addresses - 꼭 ALB의 타깃으로 private IP를 등록해야 한다
- ALB는 여러 대상 그룹으로 라우팅할 수 있다
- Health checks이 target group 수준이다

## 3. Network Load Balancer (v2)

p 244

[https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html)

- Network load balancers는 OSI(Open Systems Interconnection) 모델의 4계층(전송 계층)에서 다음을 허용한다
    - TCP 및 UDP 트래픽을 인스턴스로 전달
    - 초당 수백만 건의 요청 처리
    - 대기 시간 감소 ~100ms(vs ALB의 경우 400ms)
- NLB에는 AZ당 하나의 static IP가 있으며, Elastic IP 할당을 지원한다 (특정 IP 화이트리스트에 도움이 됨)
    - DNS name은 AZ마다 구성되므로 하나의 AZ에 문제가 생기면 다른 가용영역으로 전환할 수 있다
- NLB는 극한의 성능, TCP 또는 UDP 트래픽에 사용된다
- AWS free tier에 포함되지 않는다

### **Target Groups**

p 245

- EC2 instances - NLB에 직접 등록
- IP Addresses - 꼭 private IP여야 한다
- Application Load Balancer
![[Network-Load-Balancer.png]]

### **Zonal DNS Name(**영역 DNS 이름)

- Regional NLB DNS 이름 확인은 활성화된 모든 AZ의 모든 NLB 노드에 대한 IP 주소를 반환한다
    - [my-nlb-1234567890abcdef.elb.us-east-1.amazon.aws.com](http://my-nlb-1234567890abcdef.elb.us-east-1.amazon.aws.com/)
- Zonal DNS Name
    - NLB에는 각 노드에 대해 DNS 이름이 있다
    - 각 노드의 IP 주소를 확인하는 데 사용한다
    - [us-east-1a.my-nlb-1234567890abcdef.elb.us-east-1.amazon.aws.com](http://us-east-1a.my-nlb-1234567890abcdef.elb.us-east-1.amazon.aws.com/)
    - 대기 시간 및 데이터 전송 비용을 최소화하는 데 사용된다
    - 앱별로 논리를 구현해야 한다
    ![[Zonal-DNS-Name.png]]

## 4. **Gateway Load Balancer**

p 247

[https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html)

- AWS에서 타사 네트워크 가상 어플라이언스 구축, 확장 및 관리
    - 예: 방화벽, 침입 탐지 및 방지 시스템, 딥 패킷 검사 시스템, 페이로드 조작 등...
- 3계층(네트워크 계층)에서 작동 - IP 패킷
- 다음 기능을 결합한다
    - Transparent Network Gateway - 모든 트래픽에 대한 single entry/exit
    - Load Balancer - 트래픽을 가상 어플라이언스로 분산
- 포트 6081에서 GENVE 프로토콜을 사용한다

## 4. **Gateway Load Balancer**

p 247

[https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html)

- AWS에서 타사 네트워크 가상 어플라이언스 구축, 확장 및 관리
    - 예: 방화벽, 침입 탐지 및 방지 시스템, 딥 패킷 검사 시스템, 페이로드 조작 등...
- 3계층(네트워크 계층)에서 작동 - IP 패킷
- 다음 기능을 결합한다
    - Transparent Network Gateway - 모든 트래픽에 대한 single entry/exit
    - Load Balancer - 트래픽을 가상 어플라이언스로 분산
- 포트 6081에서 GENVE 프로토콜을 사용한다

	![[Pasted image 20240620220057.png]]
### **Target Groups**

p 248

- EC2 instances
- IP Addresses - 꼭 private IP여야 한다

![[Gateway-Load-Balancer.png]]

# II. **Cross-Zone Load Balancing**

p 249

**Cross Zone Load Balancing** 사용하는 경우(교차 영역 로드 밸런싱 사용하는 경우) 각 로드 밸런서 인스턴스는 모든 AZ에 등록된 모든 인스턴스에 균등하게 분산된다 → 균등 분배
![[Cross-Zone-Load-Balancing1.png]]
**Cross Zone Load Balancing** 사용하지 않는 경우(교차 영역 로드 밸런싱이 사용하지 않는 경우) 요청이 Elastic Load Balancer의 노드 인스턴스에 배포된다 → 불균든 분배
![[Cross-Zone-Load-Balancing2.png]]

- **Classic Load Balancer**
    - 기본적으로 ‘비활성화’로 AZ 데이터를 사용하지 않음
    - 활성화된 경우 AZ 간 데이터에 대한 요금이 부과되지 않음
- **Application Load Balancer**
    - 항상 켜져 있음(비활성화할 수 없음)
    - AZ간 데이터에 대한 요금 부과 없음
- **Network Load Balancer**
    - 기본적으로 ‘비활성화’
    - 활성화된 경우 AZ 간 데이터에 대한 요금($)을 지불함
- **Gateway Load Balancer**
    - 기본적으로 ‘비활성화’
    - 활성화된 경우 AZ 간 데이터에 대한 요금($)을 지불함

# III. **Sticky Sessions(고정 세션)**

**(Session Affinity 세션 어피니티, 선호도)**

p 251

[https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/application/sticky-sessions.html](https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/application/sticky-sessions.html)

- 동일한 클라이언트가 로드 밸런서 뒤에 있는 동일한 인스턴스로 항상 리디렉션되도록 세션을 구현할 수 있다
- 이는 Classic Load Balancers & Application Load Balancers에 적합하다
    - Application Load Balancer는 기간 기반 쿠키와 애플리케이션 기반 쿠키를 둘 다 지원
- 끈적임에 사용되는 "쿠키"는 사용자가 제어할 수 있는 유통기한이 있다
- 장점: 사용자가 세션 데이터를 잃지 않도록해 로그인 정보를 저장하는데 사용할 수 있다
- 단점: 끈적임을 활성화하면 백엔드 EC2 인스턴스에 대한 부하에 불균형이 발생할 수 있다
	![[Sticky-Sessions.png]]

# IV. Request Routing Algorithms(요청 라우팅 알고리즘)

## 1. LOR(Least Outstanding Requests, 최소 연결 요청)

p 252

[https://aws.amazon.com/ko/about-aws/whats-new/2019/11/application-load-balancer-now-supports-least-outstanding-requests-algorithm-for-load-balancing-requests/](https://aws.amazon.com/ko/about-aws/whats-new/2019/11/application-load-balancer-now-supports-least-outstanding-requests-algorithm-for-load-balancing-requests/)

- 요청을 받을 다음 인스턴스는 보류 중/미완료 요청 수가 가장 적은 인스턴스이다(처리중인 요청이 적은 대상에게 라우팅)
- Application Load Balancer 및 Classic Load Balancer(HTTP/HTTPS)와 함께 작동한다
	![[LOR.png]]

## 2. **Round Robin**

p 253

- 대상 그룹에서 대상을 (차례대로) 동일하게 선택한다
- Application Load Balancer 및 Classic Load Balancer(TCP)와 함께 작동한다
	![[Round-Robin.png]]

## 3. Flow Hash

p 254

- 프로토콜, 소스/대상 IP 주소, 소스/대상 포트 및 TCP 시퀀스 번호를 기준으로 대상을 선택한다
- 각 TCP/UDP 연결은 연결 수명 동안 단일 대상으로 라우팅된다(Sticky Sessions, 고정 세션과 비슷)
- Network Load Balancer와 함께 작동한다
	![[Flow-Hash.png]]
