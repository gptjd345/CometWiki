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


 # Route 53 - Record Types
 
 1. **A 레코드 (A Record)**
    - **설명**: 도메인 이름을 IPv4 주소(예: 192.0.2.1)로 매핑합니다.
    - **용도**: 특정 도메인 이름을 웹 서버나 기타 인터넷 리소스의 IPv4 주소와 연결할 때 사용합니다.
2. **AAAA 레코드 (AAAA Record)**
    - **설명**: 도메인 이름을 IPv6 주소(예: 2001:0db8:85a3:0000:0000:8a2e:0370:7334)로 매핑합니다.
    - **용도**: 특정 도메인 이름을 웹 서버나 기타 인터넷 리소스의 IPv6 주소와 연결할 때 사용합니다.
3. **CNAME 레코드 (Canonical Name Record)**
	- **설명**: 하나의 도메인 이름을 다른 도메인 이름으로 매핑합니다.
	- **용도**: 도메인 이름을 다른 도메인 이름으로 리디렉션할 때 사용합니다. 예를 들어, `www.example.com`을 `example.com`으로 리디렉션할 수 있습니다
4. **NS 레코드 (Name Server Record)**
    - **설명**: 도메인 이름을 관리하는 네임 서버를 지정합니다.
    - **용도**: 특정 도메인의 네임 서버를 지정할 때 사용합니다. 도메인의 DNS 설정을 다른 네임 서버로 위임할 때 필요합니다.

##### Route 53 CNAME vs. Alias
도메인 이름을 다른 도메인 이름으로 매핑하는 데 사용되지만 아래와 같은 차이가있다.
- **표준 vs 비표준**: CNAME은 표준 DNS 레코드 타입이며, Alias는 AWS Route 53에서만 지원하는 비표준 타입니다.
- **루트 도메인 지원**: CNAME 레코드는 루트 도메인에서 사용할 수 없지만, Alias 레코드는 루트 도메인에서 사용할 수 있다.
- **사용 용도**: CNAME은 주로 도메인 이름 간의 매핑에 사용되며, Alias는 AWS 리소스와의 매핑에 최적화되어 있다.
- **다른 레코드와의 혼합**: CNAME은 다른 레코드와 혼합될 수 없지만, Alias는 A 레코드나 AAAA 레코드와 같이 사용 가능하다.

##### Route 53 - Alias Records Targets
Elastic Load Balancers
CloudFront Distributions
API Gateway
Elastic Beanstalk environments
S3

그러나!!
Route 53의 alias 레코드를 EC2 인스턴스의 DNS이름으로 설정할 수 없다.
EC2 인스턴스의 DNS이름은 인스턴스 할당시 생기는 동적인 이름으로 인스턴스 상태 변화에 
따라 바뀔수있기 때문이다. 

##### Route 53 - Records TTL (Time To Live)
Route 53에서 TTL은 DNS 레코드가 캐시될 수 있는 시간(초)을 말한다.
TTL 값이 높을 수록 DNS 조회 결과가 더 오래 캐시되며, 낮을수록 더 자주 갱신된다. 

높은 TTL 값은 클라이언트의 Route53으로의 DNS 조회 빈도를 줄여
이에 대한 네트워크 트래픽을 감소시키고, 결과적으로 조회시간을 줄여 성능을 향상시킨다.

낮은 TTL 값은 레코드 변경 사항을 더 빨리 반영할 수 있다. 클라이언트가 변경을 알아채는 
시간이 빨라짐. 

#### Route 53 - Routing Policies 

1. Simple
	* Single Value : 클라이언트가 Route53에게 질의하면 답변(IP)는 하나만 준다.
	* Multiple Value : 클라이언트가 Route53에게 질의하면 답을 여러개주고 클라이언트는 이중 하나를 선택한다. 

2. Weighted
	* Route53이 요청에 대해 비중을 둬서 분산할수 있게 한다.

3. Latency-based 
	* 클라이언트와 지정된 AWS 영역사이의 트래픽에 대한 지연시간이 클라리언트의 요청에 대한 답변이됨(어느 인스턴스의 주소를 줄지)

4. Failover (Active-Passive)
	* Health Check를 통해 장애가 있는 경우 보조리소스로 트래픽을 전환한다.

5. Geolocation
	* 사용자 위치기반으로 사전에 정의된 리소스로 트래픽을 라우팅
	* 특정 지역 사용자를 특정 리소르로 라우팅 하는데 사용 
	* 설정복잡도가 상대적으로낮음 
6. Geoproximity
	* 사용자의 위치와 리소스간의 거리 및 가중치를 기반으로 최적의 리소스로 트래픽을 라우팅
	* 사용자의 물리적위치와 가장 가까운 리소스로 라우팅하여 지연시간을 최소화하는데 초점.
	* 설정복잡도가 높음
7. Traffic flow
	* 사용자가 트래픽 라우팅 규칙을 시각적인 트래픽 정책 그래프로 생성하고 관리가능. 이 그래프는 다양한 라우팅 정책(예: 지리적 라우팅, 지연 시간 기반 라우팅, 가중치 기반 라우팅 등)을 포함할 수 있음.
8. Multi-Value
	* 사용자의 질의에 대한 여러 IP 주소를 반환하고 사용자가 선택하게끔하여 가용성을 높임. 
	* 각 IP 주소에 대한 별도의 건강검사를 수행한다.
9. IP-based Routing
	* CIDR 정보를 저장해서 사용자의 IP 에 대해 라우팅하도록함. 
#### Route 53 - 




