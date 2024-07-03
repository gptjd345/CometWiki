# Amazon EMR(Elastic MapReduce)
AWS 에서 제공하는 관리형 빅데이터 처리 서비스로 대규모 데이터 세트를 처리하고 분석하는데 
사용된다. 

Apache Hadoop 클러스터를 만들어서 많은 양의 데이터를 분석하고 처리하는데 도움을 준다. 

주요 특징
1. 관리형 서비스 : Amazon EMR은 클러스터의 설정, 관리 , 유지보수를 AWS 가 대신 처리하여
사용자가 데이터 처리작업에 집중할수 있도록한다.
2. 확장성 : 필요에 따라 클러스터의 크기를 쉽게 조정가능(병렬처리가능) 
	- Auto-scaling with CloudWatch
3. 유연성: 다양한 빅데이터 도구와 프레임워크를 지원(Apache Hadoop, Apache Spark, Apache HBase, Apache Flink, Apache Hudi, Presto)

# EMR - Integrations
EMR은 VPC 내에 실행되며 단일가용성 영역(Single AZ)에 있음. 
VPC 안에는 EC2 와 EBS Volume이 존재하며 EBS Volume은 [HDFS(Hadoop Distributed File System)](Hadoop)기능을 수행하며 각 EC2 인스턴스의 임시 스토리지(Temporary Storage) 역할을 수행한다.
만약 데이터를 장기간 보유하고 싶다면 EMRFS 라는 걸 사용해야한다. <font color="#00b050">(EMR 클러스터가 종료되면 해당 클러스터의 EC2 인스턴스와 이와 연결된 EBS 볼륨도 삭제되기 때문)</font>

>[!question] EMRFS(EMR File System) ?
>Amazon S3와 통합된 파일 시스템(S3를 EMRFS로 구성할 수 있게 끔 이미 최적화되어있다는 뜻)으로 
>사용자가 원한다면 S3 버킷을 EMRFS로 구성하여 사용할 수 있다. 
>이는 HDFS와 달리 , S3(여기서는 EMRFS)는 클러스터가 종료되더라도 데이터가 유지되므로 장기간 데이터를 저장하는데 사용된다. S3는 기본적으로 다중AZ 라 내구성이 더 강하며 서버측 암호화도 정의할 수 있다.  
>


![[Pasted image 20240704003714.png]]

Amazon EMR 클러스터를 생성할 때 Hive를 선택하면, EMR은 클러스터를 구성하는 EC2 인스턴스들 중에 적절한 노드에 Hive를 자동으로 설치하고 구성한다. 일반적으로 클러스터의 마스터 노드(Master Node)에 설치된다. 위 그림에서 Amazon DynamoDB는 Hive가 처리하기 위한 <font color="#de7802">외부 DB</font>라고 생각하자.  

참고 : EMR클러스터가 생성되고 Hive를 설치하는 구체적인 절차는 다음과 같다. 
1. **EMR 클러스터 생성**:
    - AWS Management Console로 이동하여 EMR 서비스를 선택
    - "Create cluster" 버튼을 클릭
2. **소프트웨어 선택**:
    - "Software Configuration" 섹션에서 Hive를 선택 필요에 따라 다른 소프트웨어(예: Hadoop, Spark 등)도 함께 선택가능
3. **클러스터 구성**:
    - 인스턴스 유형, 클러스터 크기, 네트워크 설정(VPC 및 서브넷), 보안 그룹 등을 설정
4. **클러스터 생성**:
    - 모든 설정이 완료되면 "Create cluster" 버튼을 클릭

이후 EMR은 선택한 소프트웨어를 자동으로 설치하고 구성한다. Hive는 클러스터에 필요한 모든 종속성(dependency)과 함께 설치되며, 사용자는 추가적인 설치나 구성을 할 필요가 없다. 클러스터가 시작되면, 마스터 노드에서 Hive를 사용할 수 있으며, 이를 통해 데이터를 쿼리하고 분석할 수 있다. 
 -> 자동화된 설치 및 구성으로 사용자는 복잡한 설치 과정을 걱정하지 않고 빅데이터 작업에 집중할 수 있다. 

# Amazon EMR - Node types & purchasing

1. Master Node
	* 클러스터의 상태를 모니터링하고 작업을 스케줄링 및 분배한다. - long running
	* 하둡의 NameNode와 ResourceManager의 역할을 수행한다.
2. Core Node
	*  데이터 저장 및 작업 실행을 담당한다. - long running
	* 하둡의 DataNode와 NodeManager의 역할을 수행한다. HDFS에 데이터를 저장하고, 작업을 실행한다.
3. Task Node(Optional)
	* 작업 실행에만 참여하며, 데이터 저장은 하지 않는다.
	* 추가적인 컴퓨팅 자원을 제공하여 작업을 병렬로 처리한다. HDFS에는 데이터를 저장하지 않고, 맵리듀스 작업 등을 수행한다.