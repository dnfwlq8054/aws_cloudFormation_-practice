# AWS::ECS::Cluster
Amazon Elastic Container Service(Amazon ECS()) 는 클러스터에서 컨테이너를 쉽게 실행, 중지 및 관리할 수 있게 해주는 컨테이너 관리 서비스로서 확장성과 속도가 뛰어납니다. 컨테이너는 서비스 내에서 개별 작업 또는 작업을 실행하는 데 사용하는 작업 정의에 정의됩니다. 이 컨텍스트에서 서비스는 클러스터에서 지정된 수의 작업을 동시에 실행하고 관리할 수 있게 해주는 구성입니다. 에서 관리하는 서버를 사용하지 않는 인프라에서 작업 및 서비스를 실행할 수 있습니다.AWS Fargate. 또는 인프라에 대한 제어를 강화하기 위해 작업 및 서비스를Amazon EC2인스턴스를 관리할 수 있습니다.

Amazon ECS를 사용하면 간단한 API 호출을 사용하여 컨테이너 기반 애플리케이션을 시작하고 중지할 수 있습니다. 또한 중앙 집중식 서비스에서 클러스터 상태를 검색하고 많은 익숙한Amazon EC2기능을 제공합니다.

리소스 요구 사항, 격리 정책 및 가용성 요구 사항에 따라 클러스터 전체에 컨테이너를 배치할 일정을 수립할 수 있습니다. 다음으로 바꿉니다.Amazon ECS를 사용하면 자체 클러스터 관리 및 구성 관리 및 구성 관리 및 구성 관리 시스템을 운영하거나 관리 인프라 조정에 신경 쓸 필요가 없습니다.

Amazon ECS를 사용하여 일관된 빌드 및 배포 환경을 생성하고, 배치 및 ETL (Extract-Transform-Load) 워크로드를 관리 및 크기 조정하고, 마이크로 서비스 모델에 정교한 애플리케이션 아키텍처를 구축할 수 있습니다. Amazon ECS 사용 사례 및 시나리오에 대한 자세한 내용은 컨테이너 사용 사례를 참조하십시오.

example
```yaml
ECSService:
  Type: AWS::ECS::Service
  DependsOn:
  - Listener
  Properties:
    Role:
      Ref: ECSServiceRole
    TaskDefinition:
      Ref: ECSTaskDefinition
    DesiredCount: 1
    LoadBalancers:
    - TargetGroupArn:
        Ref: TargetGroup
      ContainerPort: 80
      ContainerName: sample-app
    Cluster:
      Ref: ECSCluster
```

reference : https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/aws-resource-ecs-service.html

다음은 Amazon ECS 클러스터에 관한 일반 개념입니다.

클러스터는 리전별로 고유합니다.

클러스터는 다음 상태 중 하나일 수 있습니다.

ACTIVE: 
클러스터는 해당하는 작업에 적용될 준비가 되었으며, 클러스터로 컨테이너 인스턴스를 등록할 수 있습니다.

PROVISIONING: 
클러스터에 연결된 용량 공급자가 있으며 용량 공급자에게 필요한 리소스가 생성 중입니다.

DEPROVISIONING(프로비저닝 취소 중): 
클러스터에 연결된 용량 공급자가 있으며 용량 공급자에게 필요한 리소스를 삭제 중입니다.

FAILED: 
클러스터에 연결된 용량 공급자가 있으며 용량 공급자에게 필요한 리소스를 생성하는 데 실패했습니다.

INACTIVE: 
클러스터가 삭제되었습니다. INACTIVE 상태인 클러스터는 일정 기간 동안 계정에서 검색 가능한 상태로 유지될 수 있습니다. 하지만 이 동작은 향후 변경될 수 있으므로 INACTIVE 클러스터가 지속되는 상태에 의존해서는 안 됩니다.
