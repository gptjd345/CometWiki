# I. Route 53 - Record Types
p 272

 
 1. **A 레코드 (A Record)**
    - 도메인 이름을 IPv4 주소(예: 192.0.2.1)로 매핑한다.
    - 용도: 특정 도메인 이름을 웹 서버나 기타 인터넷 리소스의 IPv4 주소와 연결할 때 사용한다.
1. **AAAA 레코드 (AAAA Record)**
    - 도메인 이름을 IPv6 주소(예: 2001:0db8:85a3:0000:0000:8a2e:0370:7334)로 매핑한다.
    - 용도: 특정 도메인 이름을 웹 서버나 기타 인터넷 리소스의 IPv6 주소와 연결할 때 사용한다.
2. **CNAME 레코드 (Canonical Name Record)**
	- 하나의 도메인 이름을 다른 도메인 이름으로 매핑한다.
	- 대상은 도메인 이름으로 A 또는 AAAA 레코드가 있어야 한다.
	- DNS 네임스페이스의 최상위 노드에 대한 CNAME 레코드를 만들 수 없다(Zone Apex)
	- ex) [example.com](http://example.com) 에 대해서는 생성할 수 없지만, [www.example.com](http://www.example.com) 에 대해서는 생성할 수 있다
	- **용도**: 도메인 이름을 다른 도메인 이름으로 리디렉션할 때 사용한다. 예를 들어, `www.example.com`을 `example.com`으로 리디렉션할 수 있습니다
3. **NS 레코드 (Name Server Record)**
    - 도메인 이름을 관리하는 네임 서버를 지정한다.
    - 도메인에 대한 트래픽 라우팅 방법을 제어한다.
    - **용도**: 특정 도메인의 네임 서버를 지정할 때 사용한다. 도메인의 DNS 설정을 다른 네임 서버로 위임할 때 필요하다.

## 1. Diagram for A record(A 레코드 다이어그램)
p 273

![[A-record.png]]
### 클라이언트가 EC2 인스턴스에 공용 IP를 통해 접근하려는 시나리오

1. 클라이언트는 Amazon Route 53을 통해 공용 IP 주소를 확보한다.
2. 이 공용 IP 주소를 사용하여 EC2 인스턴스에 접근한다.
3. 클라이언트는 이를 통해 간단한 A 레코드를 제공하고자 한다.

이를 통해 클라이언트는 간단한 A 레코드 기반의 솔루션을 구현할 수 있다. 더 안전하고 효율적인 솔루션을 위해서는 다음과 같은 추가적인 고려사항이 필요하다.

- 보안 그룹, 네트워크 ACL 등을 통한 네트워크 접근 제어
- 로드 밸런서, CDN 등을 통한 트래픽 관리 및 확장성 확보
- 인증 및 권한 관리 기능 구현
- 모니터링 및 로깅 기능 도입

## 2. CNAME vs. Alias
p 274

- AWS 리소스(로드 밸런서, CloudFront...)가 AWS 호스트 이름을 노출시킨다.
    - ex) lb1-1234.us-east-2.elb.amazonaws.com를 myapp.mydomain.com로 노출시키고 싶을 때 사용
- **CNAME**
    - 호스트 이름을 다른 호스트 이름으로 지정한다. ([app.mydomain.com](http://app.mydomain.com) => [blabla.anything.com](http://blabla.anything.com))
    - 루트 도메인이 아닌 경우에만 해당(일명 [something.mydomain.com](http://something.mydomain.com))
- **Alias 별칭**
    - 호스트 이름을 AWS Resource로 지정한다. ([app.mydomain.com](http://app.mydomain.com) => [blabla.amazonaws.com](http://blabla.amazonaws.com) )
    - 루트 도메인 및 루트 도메인이 아닌 경우(일명 [mydomain.com](http://mydomain.com) )에 작동한다.
    - 무료
    - 네이티브 헬스 체크
- 도메인 이름을 다른 도메인 이름으로 매핑하는 데 사용되지만 아래와 같은 차이가 있다.
    - **표준 vs 비표준**: CNAME은 표준 DNS 레코드 타입이며, Alias는 AWS Route 53에서만 지원하는 비표준 타입이다.
    - **루트 도메인 지원**: CNAME 레코드는 루트 도메인에서 사용할 수 없지만, Alias 레코드는 루트 도메인에서 사용할 수 있다.
    - **사용 용도**: CNAME은 주로 도메인 이름 간의 매핑에 사용되며, Alias는 AWS 리소스와의 매핑에 최적화되어 있다.
    - **다른 레코드와의 혼합**: CNAME은 다른 레코드와 혼합될 수 없지만, Alias는 A 레코드나 AAAA 레코드와 같이 사용 가능하다.

## 3. Alias Records Targets
p 275

- Elastic Load Balancers
- CloudFront Distributions
- API Gateway
- Elastic Beanstalk environments
- S3 Websites
- VPC Interface Endpoints
- Global Accelerator accelerator
- Route 53 record 동일한 호스트 존
- EC2 DNS 이름에 대한 ALIAS 레코드를 설정할 수 없다
    - EC2 인스턴스의 DNS이름은 인스턴스 할당시 생기는 동적인 이름으로 인스턴스 상태 변화에 따라 바뀔수있기 때문이다.

## 4. Records TTL (Time To Live)
p 276

- Route 53에서 TTL은 DNS 레코드가 캐시될 수 있는 시간(초)을 말한다.
- Alias record를 제외하고 각 DNS record에 대한 TTL이 필수이다.
- TTL 값이 높을 수록 DNS 조회 결과가 더 오래 캐시되며, 낮을수록 더 자주 갱신된다.
- 높은 TTL 값은 클라이언트의 Route53으로의 DNS 조회 빈도를 줄여 이에 대한 네트워크 트래픽을 감소시키고, 결과적으로 조회시간을 줄여 성능을 향상시킨다.
- 낮은 TTL 값은 레코드 변경 사항을 더 빨리 반영할 수 있다. 클라이언트가 변경을 알아채는 시간이 빨라진다.
	![[Records-TTL.png]]
# II. Routing Policies
https://velog.io/@combi_jihoon/Route53-Policies

## 1. Simple(단순 라우팅)
p 277

[https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-simple.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-simple.html)

- **Single Value**
    
    - 일반적으로 트래픽을 단일 리소스로 라우팅
    - Health Check와는 관련이 없다
	 ![[Single-Value.png]]
    
    - 클라이언트가 Route53에게 질의하면 답변(IP)는 하나만 준다.
- **Multiple Value**
    
    - 동일 레코드에서 여러 값을 지정할 수 있다
    - 여러 개의 값이 반환되면 클라이언트가 임의의 값이 선택한다  ![[Multiple-Value.png]]
    
    - 클라이언트가 Route53에게 질의하면 답을 여러 개주고 클라이언트는 이중 하나를 선택한다.

## 2. Weighted (가중치 기반 라우팅)
p 278

[https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-weighted.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-weighted.html)

- 각 특정 리소스에 대한 요청 비율 제어하여 분산한다
- Health Check와 연결할 수 있다
    - 상태가 안 좋은 리소스에 트래픽을 보내지 않는다
- 사용 사례: 지역 간 로드 밸런싱, 새 애플리케이션 버전 테스트...

## 3. Latency-based (지연 시간 기반 라우팅)
p 279

[https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-latency.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-latency.html)

- 가까운 대기 시간이 가장 적은 리소스로 리디렉션
- 사용자의 대기 시간이 우선시되는 경우 매우 유용하다
- 대기 시간은 사용자와 AWS 지역 간의 트래픽을 기반으로 한다
    - 독일 사용자는 미국으로 이동할 수 있다(지연이 가장 낮은 경우)
- Health Check와 연결할 수 있다(페일오버 기능이 있음)

즉, 클라이언트와 지정된 AWS 영역사이의 트래픽에 대한 지연시간이 클라리언트의 요청에 대한 답변이 된다(어느 인스턴스의 주소를 줄지)

## 4. Failover (Active-Passive) (장애 조치 라우팅)
p 280

[https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-failover.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-failover.html)

![[Failover.png]]

Health Check를 통해 장애가 있는 경우 보조 리소스로 트래픽을 전환한다.
Primary 레코드를 생성하고 failover에 사용할 Secondary 레코드를 생성해야 한다. 그런데 이 때 pirmary와 secondary 모두에 health checks를 달아줄 수도 있지만 secondary에는 꼭 health checks를 추가하지 않아도 된다.
primary, secondary 레코드를 모두 생성하고 나면 primary에 장애가 발생했을 때 secondary로 failover를 하게 되며 만약 primary가 복구 되면 다시 primary로 failover back을 할 수 있다.

## 5. Geolocation (지리적 위치 라우팅)
p 281

[https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-geo.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-geo.html)

- Latency-based (지연 시간 기반 라우팅)과는 다르다
- 사용자 지리적 위치를 기반으로 한다
- 대륙별, 국가별 또는 미국의 주별로 지리적 위치 지정 가능(중복된 경우 가장 정확한 위치 선택)
- "Default" 레코드를 만들어야 한다(위치가 일치하지 않을 경우)
- 사용 사례: 웹 사이트 현지화, 콘텐츠 배포 제한, 로드 밸런싱 등...
- Health Check와 연결할 수 있습니다
- 설정 복잡도가 상대적으로 낮다
	![[Pasted image 20240625114129.png]]

## 6. Geoproximity(지리 근접 라우팅)
p 282

[https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-geoproximity.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-geoproximity.html)

- 사용자 및 리소스의 지리적 위치에 따라 트래픽을 리소스로 라우팅
- 사용자의 물리적위치와 가장 가까운 리소스로 라우팅하여 지연시간을 최소화하는데 초점.
- 바이어스(bias)라고 하는 값을 지정하여 특정 리소스에 더 많은 트래픽을 라우팅하거나 더 적은 트래픽을 라우팅하도록 선택적으로 선택할 수도 있다
    - 바이어스(bias)는 트래픽이 리소스로 라우팅되는 지리적 영역의 크기를 확장하거나 축소한다
- 트래픽을 리소스로 라우팅하는 지리적 지역의 크기를 선택적으로 변경하려면 바이어스에 해당하는 값을 지정한다
    - 확장 시(1~99) - 리소스에 대한 트래픽 증가
    - 축소 시(-1 ~ -99) - 리소스에 대한 트래픽 감소
- 사용할 수 있는 리소스는 AWS 리소스와 Non-AWS 리소스 두 가지가 있다.
    - AWS resources(AWS 영역 지정)
    - Non-AWS resources(위도 및 경도 지정 필요)
- 설정 복잡도가 높다
- 이 기능을 사용하려면 Route 53 Traffic Flow를 사용해야 한다

### Traffic flow(트래픽 흐름)
p 285

[https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/traffic-flow.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/traffic-flow.html)

- 크고 복잡한 구성에서 레코드를 생성하고 유지 관리하는 프로세스 단순화(ex) Geoproximity 기록)
- **장점**
    - **비주얼 편집기**
        - 복잡한 레코드 트리를 만들고 레코드 간의 관계를 확인할 수 있다
        - 무료로 원하는 만큼 많은 트래픽 정책을 만들 수 있다
    - **버전 관리**
        - 이전 버전은 삭제할 때까지 계속 존재한다
        - 트래픽 정책당 기본 버전 제한은 1000개이다
    - **자동 기록 생성 및 업데이트**
        - 트래픽 정책 레코드를 생성하여 모든 레코드를 자동으로 생성할 수 있다
        - [example.com](http://example.com) 또는 www.example.com과 같이 트리 루트에 호스팅 영역과 레코드 이름을 지정하면 Route 53이 트리에 다른 모든 레코드를 자동으로 생성한다
    - **지리 근접 라우팅 정책**
        - 트래픽 흐름 시각적 캔버스의 지리 근접 맵을 사용하면 트래픽이 각 글로벌 엔드포인트로 라우팅되는 방식을 보다 직관적으로 이해 가능
    - **다양한 호스팅 영역의 여러 레코드에 재사용**
        - 여러 퍼블릭 호스팅 영역에 레코드를 자동으로 생성 가능

## 7. Multi-Value (다중값 응답 라우팅)
p 286

[https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-multivalue.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-multivalue.html)

- 트래픽을 여러 리소스로 라우팅할 때 사용
- 사용자의 질의에 대한 여러 IP 주소를 반환하고 사용자가 선택하게 끔하여 가용성을 높임.
- Route 53은 복수의 값/자원을 반환한다
- Health Checks와 연결할 수 있다(Health Resource 정상 리소스에 대한 값만 반환)
    - 각 IP 주소에 대한 별도의 Health Checks를 수행한다.
- 각 Multi-Value 쿼리에 대해 최대 8개의 정상 레코드가 반환된다
- Multi-Value는 ELB를 대체할 수는 없다

## 8. **IP-based Routing (**IP 기반 라우팅)
p 287

[https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-ipbased.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-ipbased.html)

- 라우팅은 클라이언트의 IP 주소를 기반으로 한다
- 클라이언트 및 해당 엔드포인트/위치(사용자-IP-엔드포인트 매핑)에 대한 CIDR 목록을 제공한다
- 사용 사례: 성능 최적화, 네트워크 비용 절감...
- 예: 특정 ISP에서 특정 엔드포인트로 최종 사용자 라우팅

# III. Hosted Zones
p 288

[https://ssunw.tistory.com/entry/Route-53-Route-53-개요](https://ssunw.tistory.com/entry/Route-53-Route-53-%EA%B0%9C%EC%9A%94)

- 도메인 및 하위 도메인으로 트래픽을 라우팅하는 방법을 정의하는 레코드용 컨테이너
- **Public Hosted Zone**
    - 인터넷에서 트래픽을 라우팅하는 방법(Public Domain Name)을 지정하는 레코드 포함
    - [application1.mypublicdomain.com](http://application1.mypublicdomain.com)
- **Private Hosted Zone**
    - 하나 이상의 VPC(Private Domain Name) 내에서 트래픽을 라우팅하는 방법을 지정하는 레코드 포함
    - application1.company.internal

## **1. Public vs. Private Hosted Zones**
p 289

![[PublicHostedZones.png]]

**Public Hosted Zone**
- 인터넷에서 공용 도메인을 해결할 수 있다
- 공개적으로 접근 가능하며, EC2 인스턴스의 공용 IP, 애플리케이션 로드밸런서, CloudFront 배포, S3 웹사이트의 공용 IP 등을 대상으로 한다
- [https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/AboutHZWorkingWith.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/AboutHZWorkingWith.html)

![[PrivateHostedZones.png]]

**Private Hosted Zone**
- VPC 내에서만 해결할 수 있으며, 폐쇄 URL도 정의할 수 있다
- VPC 내부에서 사용되며, EC2 인스턴스의 private IP, 데이터베이스 인스턴스의 private IP 등에 링크하는 데 도움이 된다
- private DNS가 있는 Private Hosted Zone을 가지고 있다면 VPC에서 활성화해야 한다
- [https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html)

## **2. Good to Know**
p 290

- 내부 프라이빗 DNS(Private Hosted Zone)의 경우 VPC 설정에서 enableDnsHostnames와enableDnsSupport를 사용 하도록 설정해야 한다
- DNS 보안 확장(DNSSEC, Domain Name System Security Extensions)
    - DNS 트래픽을 보호하기 위한 프로토콜로 DNS 데이터 무결성 및 출처 확인
    - MITM(Man in the Middle) 공격으로부터 보호
    - Route 53은 도메인 등록을 위한 DNSSEC와 DNSSEC 서명을 모두 지원
    - 공용 호스팅된 구역에서만 작동
- • Route 53 with 3rd Registrar
    - AWS에서 도메인을 구입하고 Route 53을 DNS 공급자로 사용할 수 있다
    - 타사 Registrar에서 NS(Name Server) 레코드 업데이트

# **IV. Health Checks**

## 1. **Health Checks**
p 291

- HTTP Health Checks는 퍼블릭 리소스에 대해서만 사용 가능하다.
- Health checks를 이용해 자동화된 DNS 장애 복구(failover)가 가능하다.
- Health checks는 아래 세 가지 기능을 할 수 있다.
    1. endpoint를 모니터링 할 수 있다.
        - application, server, 다른 AWS 리소스 중 어느 하나를 모니터링 할 수 있다.
    2. 또 다른 Health checks를 모니터링 할 수 있다.
        - 이를 계산된 Health checks라 한다. (Calculated Health Checks)
        - 여러 개의 Health checkers로부터 받은 결과를 하나의 Health checks로 합쳐주는 역할을 한다.
    3. Cloudwatch 알람을 모니터링 할 수 있다.
        - DynamoDB의 throttles
        - RDS 알람
        - 사용자가 지정한 metrics 등등
- Health checks는 CloudWatch metrics와 통합 되어 있다.
	 ![[Health-Checks 1.png]]

## **2. Calculated Health Checks**
p 292

- 여러 개의 Health Checks의 결과를 하나의 Health check로 합치는 역할을 한다.
- 결과를 합칠 때는 OR/AND/NOT 중 하나를 사용할 수 있다.
    - 이를 이용해 몇 개 이상의 Health checks가 통과해야 parent가 통과로 여길 지를 결정할 수 있다.
- Parent Health check는 최대 256개까지의 child Health checks를 가질 수 있다.
- 사용 예시모든 health checks가 fail이 될 수도 있는데 웹 사이트의 유지 보수를 위해서는 몇 개는 pass AND 몇 개는 fail 등의 기준을 정해서 모든 checks가 fail이 되는 것은 막는 것이 좋을 수 있다.
	![[CalculatedHealthChecks.png]]

## 3. **Monitor an Endpoint (Endpoint 모니터링)**
p 293

- 약 15개 정도의 전세계의 heath checkers가 endpoint의 health를 확인한다.
    - Healthy/Unhealthy의 경계선은 3이 default이다.
    - 이는 장애로 간주되기 전 몇 번의 장애가 일어나야 하는 지를 의미한다.
    - 30초 주기로 확인하며 10초로 짧게 설정하는 것도 가능하지만 돈이 더 든다.
- 지원하는 프로토콜: HTTP, HTTPS, TCP
- 만약 Health checkers의 18% 이상이 특정 엔드 포인트가 healthy하다고 판단하면 Route 53은 해당 엔드 포인트가 healthy하다고 여긴다. 그렇지 않으면 Unhealthy이다.
- 이 기능을 이용해서 어떤 지역을 Route 53이 쓸 수 있을 지를 결정할 수 있다.
- Status code가 2xx, 3xx일 때만 Health checks을 통과할 수 있다.
- Health checks는 해당 엔드 포인트가 반환하는 응답이 text인 경우 받은 첫 5120bytes의 텍스트가 맞는 지를 확인하는 방법을 이용해 pass / fail을 결정할 수 있다.
- 이 기능을 이용하기 위해서는 사용하는 리소스가 Health checkers로부터 요청을 받아야 하므로 Health checkers에 대한 방화벽을 열어 두어야 한다.

	![[MonitoranEndpoint.png]]

## 4. **Monitor an CloudWatch / Private Hosted Zones**
p 294

- 만약 endpoint가 Private Hosted Zones에 있는 경우 CloudWatch 알람을 대신 이용해 Health check를 할 수 있다.
    - 즉, private VPC에 있는 경우 Health checkers는 public에 있기 때문에 해당 endpoint에 접근할 수 없는 경우가 생길 수 있다.
    - 또는 on-premise 리소스의 경우에도 Health checker가 접근할 수 없다.
- 이럴 때는 CloudWatch Metric을 이용해 알람을 생성해서 Health Checkers가 해당 알람을 확인하도록 하면 된다.
    - 만약 경보 상태가 되면 Health checks가 통과하지 못하도록 하면 된다.
    
	![[MonitoranCloudWatch.png]]

## 5. **Health Checks Solution Architecture RDS multi-region failover**

p 295
![[HealthChecksSolutionArchitecture.png]]

### 다중 지역 장애 조치를 위한 솔루션 아키텍처
- RDS 데이터베이스를 다른 지역에 비동기식으로 복제하여 장애 조치를 구현할 수 있다.
    - RDS Main us-east-1 — 비동기식 복제 → RDS Read Replica us-west-2
    - 두 가지 방법
        1. EC2 인스턴스를 사용하여 데이터베이스 상태를 모니터링하고, HTTP를 호출해 health-db route를 노출하는 방법
        2. CloudWatch Alarm을 정의하고, Health Check 결과를 이 알람과 연결하는 방법
            - CloudWatch Alarm이 SNS 토픽이나 CloudWatch Event과 연결되면 이것을 트리거로 Lambda 함수를 호출할 수 있다.
            - 이 Lambda 함수가 Route 53을 사용하여 애플리케이션의 DNS 레코드를 업데이트하여, 읽기 복제본을 승격시킬 수 있다.
- 이를 통해 자동 장애 조치를 구현할 수 있다.

이와 같이 Route 53은 DNS 관리, 장애 조치, 부하 분산 등 다양한 기능을 제공하여 애플리케이션의 가용성과 확장성을 높일 수 있다.

# V. **Hybrid DNS**
p 296

![[Hybrid-DNS.png]]