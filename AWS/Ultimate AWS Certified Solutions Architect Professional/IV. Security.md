# IV- I ~ III. CloudTrail, EventBridge Integration, SA Pro

AWS 계정에서 발생하는 모든 API 호출 및 관련 이벤트를 기록하고, 로그파일을 저장하여 사용자 활동과 API 사용 내역을 추적할 수 있도록 도와주는 서비스

아래에서 발생한 모든 이벤트를 확인 가능  
* AWS Management Console
* SDK
* CLI
* IAM Users & IAM Roles(AWS Services)


CloudTrail Events
##### 1. Management Events
* AWS 리소스의 생성, 수정, 삭제와 같은 관리작업을 기록한다.
	ex) Amazon EC2 인스턴스를 시작하거나 중지하는 작업, IAM 사용자를 생성하는 작업 등

##### 2. Data Events
* S3 객체 수준 작업과 AWS Lambda 함수 호출과 같은 데이터 작업을 기록한다.
	ex) S3 버킷에 객체를 업로드하거나 삭제하는 작업 , Lambda 함수를 호출하는 작업 등

* 기본적으로 비활성화되어있으며, 특정 버킷이나 함수에 대해 


##### 3. CloudTrail Insights Event
### EventBridge Integration

>[!question] EventBridge Integration?
EventBridge Integration = AWS CloudTrail + Amazon EventBridge 를 의미한다.





