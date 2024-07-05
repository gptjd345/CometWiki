
# I. Amazon ECR - Elastic Container Registry

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


# II. Amazon ECR – Cross Region Replication(교차 리전 및 교차 계정 복제)

[https://docs.aws.amazon.com/ko_kr/AmazonECR/latest/userguide/Repositories.html](https://docs.aws.amazon.com/ko_kr/AmazonECR/latest/userguide/Repositories.html)

ECR private 레지스트리는 지역 간 및 계정 간 복제를 모두 지원한다. ****이를 통해 CodeBuild에서 빌드된 이미지를 다른 지역으로 자동으로 복제할 수 있다.

예를 들어, CodeBuild를 사용해 이미지를 빌드하고 이를 ECR에 푸시하면, 교차 지역 복제 설정 덕분에 이미지는 자동으로 다른 지역의 ECR로 복제된다. 이를 통해 다른 지역의 ECS (Elastic Container Service)에서 즉시 작업을 시작할 수 있어, 글로벌 서비스 제공이 원활해진다.

- **장점**
    - 자동 복제: 이미지를 다른 지역으로 자동으로 복제할 수 있어, 별도의 수작업 없이 여러 지역에 이미지 배포 가능
    - 시간 절약: 다른 지역에서 이미지를 다시 빌드할 필요가 없어 시간과 리소스를 절약
    - 글로벌 배포: 여러 지역에 걸쳐 글로벌 응용 프로그램을 쉽게 제공

# III. Amazon ECR – Image Scanning

[https://docs.aws.amazon.com/ko_kr/AmazonECR/latest/userguide/image-scanning.html](https://docs.aws.amazon.com/ko_kr/AmazonECR/latest/userguide/image-scanning.html)

ECR에서는 컨테이너 이미지를 스캔하여 취약점을 식별할 수 있으며, 이는 자동화된 프로세스를 통해 이루어질 수 있다.

- **기본 스캐닝**
    - CVE(Common Vulnerability and Exposures) 데이터베이스를 사용하는 두 가지 버전의 기본 스캐닝을 제공
    - 하나는 오픈 소스 Clair 프로젝트를 사용하는 현재 GA 버전, 다른 하나는 AWS 기본 기술
    - 푸시 시 스캔하도록 리포지토리를 구성하거나 수동 스캔 수행 가능
    - Amazon ECR은 스캔 결과 목록 제공
    - 기본 검색 → 검색 결과는 AWS 콘솔 내에서 검색 가능
        - OS 스캔
        - 두 가지 스캐닝 주파수: 수동 및 푸시 시 스캔
- **향상된 스캐닝**
    - Amazon Inspector와 통합되어 리포지토리에 대한 자동화되고 지속적인 스캐닝을 제공
    - 컨테이너 이미지에서 운영 체제와 프로그래밍 언어 패키지 취약점을 모두 검사
    - 새로운 취약점이 나타나면 스캔 결과가 업데이트되고 Amazon Inspector는 EventBridge에 이벤트를 내보내 이를 알려줌
    - 향상된 검색 → 검색 결과는 AWS 콘솔 내에서 검색 가능
        - OS 및 프로그래밍 언어 패키지 취약점
        - 두 가지 스캐닝 주파수: 푸시 시 스캔 및 연속 스캔

# IV. Amazon EKS - Elastic Kubernetes Service

• Amazon EKS = Amazon Elastic Kubernetes 서비스  
• AWS에서 관리형 Kubernetes 클러스터를 시작하는 방법
• Kubernetes는 컨테이너화된(일반적으로 Docker) 애플리케이션의 자동 배포, 확장 및 관리를 위한 오픈 소스 시스템
• ECS의 대안으로, 목표는 비슷하지만 API는 다름
• EKS는 서버리스 컨테이너를 배포하기 위해 작업자 노드 또는 Fargate를 배포하려는 경우 EC2를 지원
• 사용 사례: 회사에서 이미 Kubernetes를 사내 또는 다른 클라우드에서 사용하고 있으며 Kubernetes를 사용하여 AWS로 마이그레이션하려는 경우  
• Kubernetes는 클라우드에 구애받지 않음(Azure, GCP 등 모든 클라우드에서 사용 가능)  
• 여러 지역의 경우 지역당 1개의 EKS 클러스터 구축  
• CloudWatch Container Insights를 사용하여 로그 및 메트릭 수집

이 아키텍처는 3개의 가용성 영역(Availability Zone)으로 구성되어 있으며, 각 영역에는 VPC(Virtual Private Cloud)가 포함되어 있다.

각 VPC에는 Public subnet과 Private subnet이 있다. Public subnet에는 ELB(Elastic Load Balancing)와 NGW(NAT Gateway)가 있으며, Private subnet에는 EKS(Elastic Kubernetes Service) 노드와 EKS Pods가 있다.

이 아키텍처는 고가용성과 내결함성을 위해 여러 가용성 영역에 걸쳐 구성되어 있다. 또한 퍼블릭 서브넷과 프라이빗 서브넷을 분리하여 보안을 강화하고 있다. EKS node와 Pods는 컨테이너 기반 애플리케이션을 실행하기 위한 것이다.

- **주요 보안 요소**
    - **Public subnet과 Private subnet의 분리 →** 내부 리소스 접근 제한을 통한 보안 강화
        - Public subnet: 인터넷에 노출되어야 하는 리소스(ELB, NGW)
        - Private subnet: 내부에서만 접근할 수 있는 리소스(EKS 노드, EKS Pods)
    - **NGW(NAT Gateway)의 사용 →** 내부 리소스에 대한 직접적인 인터넷 접근을 차단
        - Private subnet의 리소스들이 인터넷에 직접 접근할 수 없도록 NGW를 통해 트래픽 라우팅
    - **EKS node와 EKS Pods의 분리** 컴퓨팅 리소스와 애플리케이션 컨테이너를 분리함으로써 보안 강화
        - EKS node: EC2 인스턴스로 구현
        - EKS Pods: EKS node 위에서 실행
    - **자동 스케일링 그룹** → 과도한 리소스 사용을 방지, 보안을 향상
        - EKS node는 자동 스케일링 그룹으로 관리, 트래픽 변화에 따라 노드 수를 자동 조절
    - **부하 분산기** → 단일 지점 장애를 방지하고 가용성 높임
        - 공용 부하 분산기(ELB)와 사설 부하 분산기를 통해 트래픽을 분산

[출처]https://www.devopsschool.com/blog/wp-content/uploads/2021/03/Amazon-Elastic-Kubernetes-Service-EKS-Explained-Diagram-1.png

## 1. AWS EKS - Node(EC2 instance) Types
p 219

1. **Managed Node Groups**
* EC2 인스턴스를 자동으로 생성, 업데이트, 패치, 종료 작업 전반을 관리해줌 
* EC2 인스턴스로는 On-Demand 나 Spot Instances를 지원한다. 

2. **Self-Managed Nodes**
* 사용자가 직업 EC2 인스턴스를 직접 생성하고 관리함
* Amazon EKS Optimized AMI를 사용하여 좀 편하게 쓸 수 있음. 
 >[!tip] Amazon EKS Optimized Amazon Machine Image(AMI)
 >kubernetes 노드를 실행하는데 필요한 모든 소프트웨어가 사전 설치 및 구성되어있음. AMI는 다양한 인스턴스 타입과 요구사항에 맞게 최적화된 여러 버전으로 제공되며, 정기적으로 최신보안 패치와 업데이트를 수행하고있음. 이걸 가져다가 직접 node를 생성하면 그나마 편함 
 >

3. **AWS Fargate**
* 아무것도 관리하기 싫을 때 편하게 사용가능

## 2. Amazon EKS - Data Volumes

* **장점**
	* EKS 클러스터에서 StorageClass 매니페스트를 지정 -> 애플리케이션 요구사항에 맞는 스토리지 클래스 선택 가능
	* CSI(Container Storage Interface) 호환 드라이버 활용 -> 다양한 스토리지 공급업체에 종속되지 않고 유연성 있게 선택 가능
	* 다양한 스토리지 유형 선택 가능 -> 워크로드 요구사항에 맞는 최적의 스토리지 선택 가능
		* **EKS에서 사용가능한 스토리지 유형**
			* Amazon EBS
			* Amazon EFS (works with Fargate)
			* Amazon FSx for Lustre
			* Amazon FSx for NetApp ONTAP
* **단점**
	* **복잡성**: 다양한 스토리지 옵션 활용을 위해, 각 스토리지 유형의 특성과 사용법을 숙지 필요
	* **관리 오버헤드**: 다양한 스토리지 옵션을 사용 시, 스토리지 관리에 대한 오버헤드가 증가
	* **비용**: 다양한 스토리지 옵션은 각각 다른 가격 정책을 가지고 있어, 최적의 가격 대비 성능을 찾는 데 어려움


## 3. AWS App Runner VS AWS Fargate

1. **비용 측면**
    - App Runner는 일반적으로 작은 워크로드와 긴 유휴 기간에 더 비용 효율적입니다.
    - Fargate는 더 복잡한 워크로드와 높은 활용도에 더 적합할 수 있습니다.
2. **기능 및 유연성 측면**
    - Fargate는 더 많은 기능과 유연성을 제공합니다. 예를 들어 사용자 지정 네트워킹, 스케일링 정책 등을 설정할 수 있습니다.
    - App Runner는 더 간단한 인터페이스를 제공하지만, Fargate만큼 많은 기능을 제공하지 않습니다. ex) Fargate는 여러 CloudWatch metric (CPU, network I/O, requests ...) 에 대한 스케일링 정책 설정가능 . 

App Runner Multi-Region Architecture
1. DynamoDB를 사용하는 App Runner Service를 만든다 
2. App Runner Service에서 만든 image를 ECR Registry 에 등록한다. 
3. DynamoDB는 Global Table Replication 을 통해 리전간 복제가 가능하다. 
4. ECR Registry는 Cross-Region Replication을 통해 리전간 복제가 가능하다 
5. App Runner를 복제해서 멀티 리전 구성할 수 있다. 
6. Route 53를 이용해 각 리전의 App Runner Service에 요청을 분배하도록 한다. 

## 4. Amazon ECS Anywhere

[https://aws.amazon.com/ko/blogs/aws/getting-started-with-amazon-ecs-anywhere-now-generally-available/](https://aws.amazon.com/ko/blogs/aws/getting-started-with-amazon-ecs-anywhere-now-generally-available/)

- 고객 관리 인프라(사내, VM 등)에서 컨테이너를 쉽게 실행 가능
- 고객이 모든 환경에서 기본 Amazon ECS 태스크를 배포할 수 있도록 허용
- **시작하기**
	1. 온프레미스 서버나 VM(외부 인스턴스)을 ECS 클러스터에 등록
	2. 외부 인스턴스에 AWS Systems Manager Agent(SSM), Amazon ECS Container Agent 및 Docker 설치 필요
	3. 외부 인스턴스에 AWS API와 통신할 수 있도록 허용하는 IAM 역할이 필요
	4. 온프레미스 환경에서 AWS Cloud를 제어하기 위해 ECS 서비스 중 완전히 관리되는 Amazon ECS 제어 플랜을 사용
	5. 등록된 외부 인스턴스에서 작업을 실행하려면 시작 유형에서 EXTERNAL 선택
	※ AWS 영역에 안정적으로 연결되어 있어야 가능

- **사용 사례**
    - 규정 준수, 규제 및 대기 시간 요구사항 충족
    - AWS 리전 밖에서 앱을 실행하고 다른 서비스에 더 가깝게 사용 가능
    - 온-프레미스 ML, 비디오 처리, 데이터 처리, …

## 5. Amazon EKS Anywhere

[https://aws.amazon.com/ko/eks/eks-anywhere/faqs/](https://aws.amazon.com/ko/eks/eks-anywhere/faqs/)

- AWS가 구축한 컨테이너 관리 소프트웨어
- 온프레미스와 엣지의 Kubernetes 클러스터를 더 쉽게 실행, 관리 가능
- 지원 비용 절감 및 중복된 타사 툴 유지 보수, 손상 방지
- **시작하기**
	1. AWS 외부에서 생성된 Kubernetes 클러스터 생성 및 운영하기 위해 온프레미스 환경에 EKS 클러스터 설치
	    - Amazon EKS Distro 활용(AWS의 Kubernetes 번들 출시)
	        - Amazon EKS Distro: Amazon EKS를 통해 배포되는 것과 동일한 오픈 소스 구성 요소 및 종속성을 포함하는 Kubernetes 배포
	2. EKS 클러스터를 온프레미스에서 사용하기 위해 EKS Anywhere Installer 설치 필요
	    - 선택 옵션: EKS Connector를 사용해 EKS Anywhere나 EKS 클러스터를 AWS에 연결 가능
	        - EKS Connector: Kubernetes 클러스터에서 실행되며, 클러스터를 Amazon EKS 콘솔에 등록할 수 있도록 하는 소프트웨어 에이전트
	        - 완전 연결 및 부분 연결 해제: Amazon EKS Anywhere 클러스터에서 AWS 연결, EKS Console 활용
	            - EKS Console: AWS Console 내의 Amazon EKS 통합 대시보드. Kubernetes 클러스터 및 애플리케이션의 연결, 시각화 및 문제 해결 수행 가능
	        - 완전 연결 해제: EKS Distro를 설치하고 오픈 소스 툴을 활용하여 클러스터 관리
