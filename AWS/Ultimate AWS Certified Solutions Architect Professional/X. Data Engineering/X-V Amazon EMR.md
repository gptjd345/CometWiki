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
만약 데이터를 장기간 보유하고 싶다면 EMRFS 라는 걸 사용해야한다. 
S3(EMRFS) 은 영구적인 스토리지(Permanent Storage) 


