애플리케이션 로드를 처리하는 데 사용할 수 있는 Amazon EC2 인스턴스의 정확한 수를 확보하는 데 도움이 된다. 조정 정책을 지정하면 Amazon EC2 Auto Scaling은 애플리케이션 수요가 증가하거나 감소함에 따라 인스턴스를 시작하거나 종료할 수 있다.

예를 들어 다음 Auto Scaling 그룹의 최소 크기는 인스턴스 4개, 원하는 용량은 인스턴스 6개, 최대 크기는 인스턴스 12개인 경우는 다음과 같이 나타낼 수 있다.

![[auto-scaling-group.png]]

# I. Auto Scaling Groups(ASG)
p 191

[https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html)

Amazon EC2 Auto Scaling을 사용하면 EC2 인스턴스가 Auto Scaling 그룹으로 구성되어 확장 및 관리 목적으로 하나의 논리 단위로 처리될 수 있다. Auto Scaling 그룹은 시작 템플릿(또는 시작 구성)을 EC2 인스턴스의 구성 템플릿으로 사용한다.

## 1. Dynamic Scaling Policies(동적 조정)
[https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/as-scale-based-on-demand.html](https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/as-scale-based-on-demand.html)

자동 스케일링 그룹(ASG)에서 동적 스케일링 정책은 다음과 같이 구성할 수 있다.

- **Target Tracking Scaling (대상 추적 조정 정책)**
    
    - **가장 간단하고 쉽게 설정 가능**:
        - 특정 지표를 목표로 설정하여 스케일링을 자동으로 관리
        - ex) 평균 ASG CPU 사용률이 40%를 유지하도록 설정
    
- **Simple / Step Scaling (단순/단계별 조정 정책)**
    - **CloudWatch 경보를 기반으로 스케일링**
    - **작동 방식(공통)**
        1. Auto Scaling 그룹의 지표를 모니터링하는 CloudWatch 경보를 생성
        2. 경보 위반을 결정하는 지표, 임계값, 평가 기간 수를 정의
        3. 경보 임계값 위반 시 그룹을 조정하는 방법을 정의하는 단계별 조정 정책을 생성
        4. 정책에 단계 조정을 추가(경보의 위반 규모에 따라 다양한 단계 조정을 정의 가능)
            1. ex) CPU 사용률이 70%를 초과하면 인스턴스 2개 추가
            2. ex) CPU 사용률이 30% 미만이면 인스턴스 1개 제거
    - **차이점**
        - **단계별 조정 정책**
            - 지정한 단계 조정에 따라 그룹 크기를 더 크게 또는 더 작게 변경 가능
            - 조정 활동 또는 상태 점검 교체가 진행 중인 동안에도 정책이 추가 경보에 계속 응답함
            - 즉, Amazon EC2 Auto Scaling은 경보 메시지를 수신할 때 모든 경보 위반을 평가함
            - 따라서 조정 조정이 한 번뿐이더라도 단계 조정 정책을 대신 사용하는 것이 좋음
        - **단순 조정 정책**
            - 진행 중인 조정 활동 또는 상태 점검 교체가 완료되고 휴지 기간이 종료될 때까지 기다려야 함
            - 추가 경보에 응답하기 전에 휴지 기간이 종료되어야 함
    
- **Scheduled Actions (예약된 작업)**
    - [https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/ec2-auto-scaling-scheduled-scaling.html](https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/ec2-auto-scaling-scheduled-scaling.html)
    - **알려진 사용 패턴을 기반으로 스케일링**
        - 특정 시간에 맞춰 자동으로 용량을 조절함
        - ex) 금요일 오후 5시에 최소 용량을 10으로 증가

## 2. Predictive Policies(예측 조정)

[https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/ec2-auto-scaling-predictive-scaling.html](https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/ec2-auto-scaling-predictive-scaling.html)

자동 스케일링 그룹(ASG)에서 예측 정책은 다음과 같이 구성할 수 있다.

- **Predictive scaling (예측 조정 정책)**
	- **사용하기 적합한 상황**
	    - 주기적 트래픽
	        - ex) 정규 업무 시간 동안 리소스 사용량이 많고 저녁 및 주말에는 리소스가 적게 사용되는 경우)
	    - 반복되는 on-and-off 워크로드 패턴
	        - ex) 일괄 처리, 테스트 또는 주기적 데이터 분석)
	    - 초기화하는 데 시간이 오래 걸려 스케일 아웃 이벤트 중에 애플리케이션 성능에 눈에 띄는 지연 영향을 받는 애플리케이션
	    
    - **작동 방식**
        1. 모니터링 및 분석할 CloudWatch 지표를 지정하는 예측 규모 조정 정책 생성
            1. 예측 척도를 통해 미래 값을 예측하려면 지표에 최소 24시간 분량의 데이터 필요
        2. 패턴을 식별하기 위해 최대 지난 14일까지의 지표 데이터 분석 시작
            1. 향후 48시간 동안의 용량 요구 사항에 대한 시간별 예측 생성
            2. 최신 CloudWatch 데이터를 사용하여 6시간마다 업데이트
            3. 새로운 데이터가 들어오면 미래 예측의 정확도를 지속적으로 개선
        3. 처음 활성화 시 예측 전용 모드로 실행됨
            1. 예측의 정확성과 적합성 평가 → Auto Scaling 그룹을 실제로 확장하지는 않음
        4. 조정 정책을 예측 및 규모 조정 모드로 전환
            1. 부하가 증가 예상 시: 규모를 확장하여 용량 늘림
            2. 부하 감소가 예상 시: 용량을 줄일 수 있을 정도로 규모를 축소하지는 않음 → 더 이상 필요하지 않은 용량을 제거하려면 동적 조정 정책을 생성해야 함

# II. **스케일링의 기반이 되는 좋은 지표(metrics)**
p 193

[https://choiblog.tistory.com/175](https://choiblog.tistory.com/175)
[https://velog.io/@combi_jihoon/Auto-Scaling-Group-Scaling-Policies](https://velog.io/@combi_jihoon/Auto-Scaling-Group-Scaling-Policies)

자동 스케일링 그룹(ASG)에서 확장하기에 적합한 메트릭은 애플리케이션의 성능과 안정성을 보장하기 위해 중요하다.

- **CPUUtilization(CPU 사용률)**
    - 인스턴스 전체의 평균 CPU 사용률 모니터링
    - ex) 평균 CPU 사용률이 70%를 초과하면 인스턴스를 추가하여 부하 분산
    
- **RequestCountPerTarget(대상별 요청수)**
    - EC2 인스턴스당 요청 수가 안정적인지 확인
    - 인스턴스가 안정적으로 작동하는 경우 유용
    - ex) 특정 인스턴스당 요청 수가 일정 임계값을 초과하면 인스턴스를 추가하여 요청 처리
    - 아래의 경우 인스턴스 당 RequestCountPerTarget는 3
	    ![[RequestCountPerTarget 1.png]]
    
- **Average Network In / Out(평균 네트워크 입력/출력)**
    - 네트워크 바인딩된 응용 프로그램의 경우 네트워크 트래픽 모니터링
    - ex) 평균 네트워크 입력/출력 값이 특정 임계값을 초과하면 인스턴스 추가
    
- **Any custom metric (사용자 지정 메트릭)**
    - CloudWatch를 사용해 애플리케이션에 특화된 메트릭 모니터링
    - 이러한 사용자 정의 메트릭을 기반으로 스케일링 정책 설정 가능
    - ex) 특정 애플리케이션 로그의 에러 발생 횟수, 메모리 사용량, 디스크 I/O 등.

# III. Auto Scaling Groups(ASG)의 알아두면 좋은 것

- **Spot Fleet support**
    - **Spot 및 On-Demand 인스턴스 혼합 사용 → 비용 절감**
        - 비용 절감을 위해 Spot 인스턴스를 사용하면서도 안정성을 위해 On-Demand 인스턴스를 함께 사용 가능
    
- **Lifecycle Hooks**
    - **인스턴스 라이프사이클 중 특정 작업 수행 → 자동화**
        - 인스턴스가 서비스에 들어가기 전 또는 종료되기 전에 특정 작업 수행 가능
        - ex) 클린업 작업, 로그 추출, 특별한 헬스 체크 등
    
- **AMI 업그레이드**
    - **런치 구성/템플릿 업데이트 → 쉬운 관리**
        - AMI를 업그레이드하려면 런치 구성 또는 템플릿을 업데이트 필요
        - 이후 기존 인스턴스를 수동으로 종료(CloudFormation로 자동화 가능)
        - 또는 EC2 Instance Refresh 기능을 사용하여 자동으로 인스턴스 새로 고침 사용


# IV. Auto Scaling 주요 기능

## 1. Instance Refresh(**인스턴스 새로고침)**
p 195

[https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/asg-instance-refresh.html](https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/asg-instance-refresh.html)

- **목표**
    - 런치 템플릿을 업데이트한 후 모든 EC2 인스턴스를 재생성하는 것.
    
- **Instance Refresh 기능 사용의 이점**
    - Instance Refresh 기능을 사용하면 수동으로 인스턴스를 종료하고 다시 시작할 필요 없이, 자동으로 인스턴스 새로 고침 가능
    
- **설정 방법**
    1. **최소 건전 백분율(Minimum Healthy Percentage) 설정**
        - 새로 고침 중에도 일정 비율의 인스턴스가 건강한 상태로 유지되도록 설정 가능
        - ex) 최소 건강 비율을 80%로 설정하면, 전체 인스턴스 중 80%는 항상 가용 상태로 유지
    2. **최대 건전 백분율(Maximum Healthy Percentage) 설정**
        - 인스턴스를 교체할 때 Auto Scaling 그룹이 늘릴 수 있는 원하는 용량의 백분율
        - 최소값과 최대값의 차이가 100을 초과할 수 없음
    3. **워밍업 시간(Warm-up Time) 지정**
        - 새 인스턴스가 시작되고 실제로 사용 가능해질 때까지(InService)의 시간 설정
        - ex) 워밍업 시간을 300초(5분)로 설정하면, 새 인스턴스가 시작된 후 5분 동안은 트래픽을 받지 않고 준비 상태에 있게 됨
	 ![[Instance-Refresh.png]]
    
- **설정 예시**
    AWS Management Console 또는 AWS CLI를 통해 Instance Refresh를 설정할 수 있습니다.
    
    - **AWS Management Console**
        1. Auto Scaling 그룹을 선택한다.
        2. "Instance Refresh" 탭으로 이동한다.
        3. "Start Instance Refresh" 버튼을 클릭한다.
        4. 최소 건강 비율과 워밍업 시간을 설정한다.
        5. 설정을 저장하고 새로 고침을 시작한다.
    - **AWS CLI**
        - bash
            ```bash
aws autoscaling start-instance-refresh --auto-scaling-group-name <AutoScalingGroupName> --preferences '{"MinHealthyPercentage":<Percentage>,"InstanceWarmup":<TimeInSeconds>}'
            ```
            - `<AutoScalingGroupName>`: Auto Scaling 그룹의 이름
            - `<Percentage>`: 최소 건강 비율 (예: 80)
            - `<TimeInSeconds>`: 워밍업 시간 (예: 300초)
    - **예시 설정**
        - bash
            ```bash
aws autoscaling start-instance-refresh --auto-scaling-group-name MyAutoScalingGroup --preferences '{"MinHealthyPercentage":80,"InstanceWarmup":300}'
            ```

## 2. Scaling Processes
p 196

- **주요 스케일링 프로세스**
    1. **Launch**: 새로운 EC2 인스턴스를 그룹에 추가하여 용량 증가
    2. **Terminate**: 그룹에서 EC2 인스턴스를 제거하여 용량 감소
    3. **HealthCheck**: 인스턴스의 상태 점검. 상태가 불량한 인스턴스 탐지
    4. **ReplaceUnhealthy**: 상태가 불량한 인스턴스를 종료하고, 새로 생성하여 교체
    5. **AZRebalance**: 가용 영역(AZ) 간에 EC2 인스턴스 수를 균형 있게 조정
    6. **AlarmNotification**: CloudWatch 경보를 수신하여 스케일링 작업을 트리거
    7. **ScheduledActions**: 사용자가 생성한 예약된 작업을 수행
    8. **AddToLoadBalancer**: 인스턴스를 로드 밸런서 또는 타겟 그룹에 추가
    9. **InstanceRefresh**: 인스턴스 새로 고침. 새로운 런치 템플릿을 적용하여 인스턴스 업데이트

- **프로세스 일시 중지**
    - 필요에 따라 특정 스케일링 프로세스를 일시 중지 가능
    - ex) 특정 작업을 수행하는 동안 스케일링을 일시 중지하여 예기치 않은 인스턴스 추가/제거 방지

- **일시 중지 방법**
    - **AWS Management Console**
        1. Auto Scaling 그룹을 선택한다.
        2. "Activity" 탭으로 이동한다.
        3. "Suspend" 또는 "Resume" 버튼을 클릭하여 특정 프로세스를 일시 중지 또는 재개한다.
    - **AWS CLI**
        - bash
            ```bash
aws autoscaling suspend-processes --auto-scaling-group-name <AutoScalingGroupName> --scaling-processes <ProcessName>
            ```
            - `<AutoScalingGroupName>`: Auto Scaling 그룹의 이름
            - `<ProcessName>`: 일시 중지할 프로세스 이름 (예: Launch, Terminate 등)
        - bash
            ```bash
aws autoscaling resume-processes --auto-scaling-group-name <AutoScalingGroupName> --scaling-processes <ProcessName>
            ```
            - `<ProcessName>`: 재개할 프로세스 이름
    - **예시 설정**
        - bash
            ```bash
aws autoscaling suspend-processes --auto-scaling-group-name MyAutoScalingGroup --scaling-processes Terminate
            ```
            - `Terminate` 프로세스를 일시 중지하여 인스턴스가 그룹에서 제거되지 않도록 함
        - bash
            ```bash
aws autoscaling resume-processes --auto-scaling-group-name MyAutoScalingGroup --scaling-processes Terminate
            ```
            - `Terminate` 프로세스를 다시 활성화하여 정상 작동하도록 함

## 3. Health Checks
p 197

- **헬스 체크 옵션**
    1. **EC2 Status Checks**:
        - EC2 인스턴스의 하드웨어 및 소프트웨어 상태 점검
        - EC2 인스턴스 자체에서 수행되는 기본적인 상태 점검
    2. **ELB Health Checks (HTTP)**:
        - ELB(Elastic Load Balancer)가 인스턴스의 상태 점검
        - HTTP 요청을 통해 인스턴스의 응답 상태 확인
        - 로드 밸런서를 사용하는 경우, ELB 헬스 체크를 통해 인스턴스의 가용성 보장
    3. **Custom Health Checks**:
        - 사용자 정의 헬스 체크를 통해 인스턴스의 상태를 Auto Scaling 그룹에 전달 가능
        - AWS CLI 또는 AWS SDK를 사용하여 `set-instance-health` 명령을 통해 인스턴스의 상태설정
        - ex) 특정 애플리케이션의 상태를 점검하여 헬스 체크 결과를 Auto Scaling 그룹에 반영

- **헬스 체크 동작 방식**
    - Auto Scaling 그룹은 불량한 상태로 판정된 인스턴스를 종료, 새로운 인스턴스를 시작하여 교체
    - 이를 통해 건강하지 않은 인스턴스가 지속적으로 운영되는 것을 방지, 시스템의 가용성을 유지
	 ![[Health-Checks.png]]
    
- **헬스 체크 설정 시 주의사항**
    - 단순하고 정확한 헬스 체크를 구성하는 것이 중요하다.
    - 너무 복잡하거나 지나치게 엄격하면, 정상적인 인스턴스가 불량으로 판정될 수 있다.
    - 너무 느슨하면, 실제로 불량한 인스턴스가 탐지되지 않을 수 있다.
    
- **헬스 체크 설정 예시**
    - **EC2 Status Checks 및 ELB Health Checks 설정 (AWS Management Console)**
        1. Auto Scaling 그룹을 선택한다.
        2. "Details" 탭에서 "Health Check Type"을 선택한다.
        3. EC2, ELB, 또는 둘 다 선택할 수 있다.
        4. "Health Check Grace Period"를 설정하여 헬스 체크가 시작되기 전에 대기할 시간을 지정한다.
    - **Custom Health Checks 설정 (AWS CLI)**
        1. 사용자 정의 스크립트를 통해 인스턴스 상태를 점검한다.
        2. 인스턴스 상태를 설정한다.
        - bash
            ```bash
aws autoscaling set-instance-health --instance-id <InstanceId> --health-status <HealthStatus>
            ```
            - `<InstanceId>`: 인스턴스 ID
            - `<HealthStatus>`: `Healthy` 또는 `Unhealthy`
    - **예시 설정**
        - bash
            ```bash
aws autoscaling set-instance-health --instance-id i-0123456789abcdef0 --health-status Unhealthy
            ```
            - 지정된 인스턴스를 불량 상태로 설정.
            - Auto Scaling 그룹은 이 인스턴스를 종료하고 새 인스턴스를 시작