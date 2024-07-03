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

![[Pasted image 20240703234713.png]]


