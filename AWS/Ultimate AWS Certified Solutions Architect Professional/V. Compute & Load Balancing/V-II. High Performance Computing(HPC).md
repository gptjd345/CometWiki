p 186

https://aws.amazon.com/ko/getting-started/hands-on/deploy-elastic-hpc-cluster/faq/
https://aws.amazon.com/ko/blogs/tech/introduction-of-hpc-on-aws/

고성능 컴퓨팅(HPC)은 매우 복잡하고 계산 집약적인 작업을 수행하는 데 필요한 기술이다. 클라우드는 이러한 HPC 작업을 수행하기에 최적의 환경을 제공한다. 

# I. 클라우드에서 HPC의 이점

## 1. **즉각적인 리소스 생성**

- 클라우드는 필요할 때 순식간에 매우 많은 컴퓨팅 리소스를 생성할 수 있다.
- 물리적 하드웨어를 구매하고 설정하는 데 필요한 시간이 절약된다.

## 2. **확장성**

- 클라우드 환경에서는 필요한 만큼 리소스를 쉽게 확장할 수 있다.
- 추가 리소스를 활용하여 계산 시간을 단축하고 더 빠르게 결과를 도출할 수 있다.

## 3. **비용 효율성**

- 사용한 만큼만 비용을 지불하는 방식(Pay-as-you-go)으로, 리소스를 사용한 시간과 양에 따라 결제된다.
- 대규모 초기 투자 없이도 고성능 컴퓨팅을 활용할 수 있다.

## 4. **다양한 활용 분야**

- 클라우드 기반 HPC는 다양한 분야에서 활용될 수 있다:
    - **유전체학 (Genomics)**: 대규모 유전자 데이터 분석
    - **전산화학 (Computational Chemistry)**: 분자 모델링과 시뮬레이션
    - **재무위험 모델링 (Financial Risk Modeling)**: 복잡한 금융 모델 계산
    - **날씨 예측 (Weather Forecasting)**: 기상 데이터 분석 및 예측
    - **기계 학습 (Machine Learning)**: 대규모 데이터 셋을 활용한 모델 훈련
    - **딥 러닝 (Deep Learning)**: 신경망 모델의 훈련과 추론
    - **자율 주행 (Autonomous Driving)**: 자율 주행 차량의 시뮬레이션 및 데이터 처리

클라우드는 즉각적인 리소스 생성, 뛰어난 확장성, 비용 효율성 등 많은 이점을 제공하며, 유전체학, 전산화학, 재무위험 모델링, 날씨 예측, 기계 학습, 딥 러닝, 자율 주행 등의 다양한 분야에서 활용될 수 있다. 클라우드 기반 HPC를 통해 복잡한 계산 작업을 더 빠르고 효율적으로 수행할 수 있다.

# II. HPC(고성능 컴퓨팅)를 수행하는 데 유용한 AWS 서비스

## 1. Data Management & Transfer(데이터 관리 및 전송)

- **AWS Direct Connect**
    - 프라이빗 보안 네트워크를 통해 GB/s의 데이터를 클라우드로 이동
- **Snowball & Snowmobile**
    - 대규모 데이터(PB 단위)를 클라우드로 이동
- **AWS DataSync**:
    - 온프레미스와 S3, EFS, FSx 간에 대용량 데이터를 이동. 특히 Windows 환경에서 유용

## 2. Compute and Networking(컴퓨팅 및 네트워킹)

p 188 `구분 중요`


```
💡 EC2 인스턴스
- CPU 최적화, GPU 최적화 인스턴스 사용.
- 비용 절감을 위한 Spot 인스턴스 / Spot Fleet + 자동 확장.
- 네트워크 성능 향상을 위한 EC2 배치 그룹의 클러스터 사용. 
```

    
- **EC2 Enhanced Networking(EC2 향상된 네트워킹)(SR-IOV)**
    - 더 높은 대역폭, 더 높은 PPS(Packet per second), 더 낮은 지연 시간 제공.
    - **EC2 Enhanced Networking이 가능한 이유**
        1. **ENA(Elastic Network Adapter)**: 최대 100Gbps. EC2 개선을 위한 네트워킹으로 높은 대역폭을 제공하며, 초당 패킷이 높고 지연 시간이 낮다.
        2. **Intel 82599 VF**: 최대 10Gbps(레거시). 인텔사에서 제공하는 오래된 ENA
    - [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html)
    
- **Elastic Fabric Adapter(EFA):**
    - 고성능 컴퓨팅용 HPC 워크로드를 위해 향상된 ENA, Linux에서만 작동.
    - 노드 간 통신이나 밀접하게 결합된 작업이 유용. ex) 분산 연산
        - 이유 : MPI(Message Passing Interface) 표준 활용. 이 표준은 기본 Linux OS를 우회하여 지연 시간이 짧고 신뢰할 수 있는 안정적인 전송 제공.
    - [https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/efa.html](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/efa.html)

## 3. Storage(스토리지)
p 189

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Storage.html


![[storage.png]]

- **Instance-attached storage(인스턴스 연결 스토리지)**
    - **Amazon EBS**
        - io2 Block Express로 최대 256,000 IOPS 확장
        - 인스턴스에 연결하고 분리할 수 있는 내구성이 뛰어난 블록 수준 스토리지 볼륨을 제공
        - 여러 EBS 볼륨을 인스턴스에 연결 가능, 인스턴스의 수명과 독립적으로 유지됨
        - [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/storage_ebs.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/storage_ebs.html)
    - **Instance Store**
        - EC2 인스턴스와 연결되어 수백만 IOPS로 확장, 짧은 지연 시간
        - 인스턴스에 대한 임시 블록 수준 스토리지를 제공
        - 데이터는 연결된 인스턴스의 수명 동안에만 유지
        - [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html)
	- <font color="#92d050">* IOPS(Input/Output Operations Per Second): 저장 장치의 성능 측정 단위로, 초 당 입/출력 처리량</font>
    
- **Network storage(네트워크 스토리지)**
    - **Amazon S3**
        - 파일 시스템이 아닌 큰 Blob(블랍)
        - 안정적이고 저렴한 데이터 스토리지 인프라에 대한 액세스를 제공
        - [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonS3.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonS3.html)
        - <font color="#92d050">* Blob(Binary Large Object): 어떠한 큰 객체를 Binary(2진수) 형태로 저장하는 데 사용</font>
        
    - **Amazon EFS (Linux 인스턴스만 해당)**
        - 전체 크기를 기준으로 IOPS 확장 또는 프로비저닝된 IOPS 사용
        - Amazon EC2에서 사용할 수 있는 확장 가능한 파일 스토리지 제공
        - [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEFS.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEFS.html)
    - **Lustre용 Amazon FSx**
        - HPC 최적화된 분산 파일 시스템, 수백만 IOPS, S3에 의해 지원됨
        - 기능이 풍부한 고성능 파일 시스템을 시작, 실행 및 확장 가능
        - [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/storage_fsx.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/storage_fsx.html)

## 4. Automation and Orchestration(자동화 및 오케스트레이션)
p 190

- **AWS Batch**
    
    - 단일 실행이 가능한 다중 노드 병렬 작업을 지원
    - 여러 EC2 인스턴스에 걸쳐 있는 작업을 쉽게 예약하고 실행 가능
    - [https://docs.aws.amazon.com/ko_kr/batch/latest/userguide/what-is-batch.html](https://docs.aws.amazon.com/ko_kr/batch/latest/userguide/what-is-batch.html)

- **AWS ParallelCluster (병렬 클러스터)**
    - AWS 클라우드에서 고성능 컴퓨팅(HPC)를 구축하기 위한 오픈 소스 클러스터 관리 툴
    - 텍스트 파일로 구성할 수 있음
    - VPC, 서브넷, 클러스터 유형 및 인스턴스 유형 생성 자동화
    - [https://docs.aws.amazon.com/ko_kr/parallelcluster/latest/ug/what-is-aws-parallelcluster.html](https://docs.aws.amazon.com/ko_kr/parallelcluster/latest/ug/what-is-aws-parallelcluster.html)