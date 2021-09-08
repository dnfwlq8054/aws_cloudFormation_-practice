# AWS::RDS::DBInstance

RDS는 AWS에서 제공해주는 DB서비스 입니다.

사용자는 MYSQL을 설치할 필요 없이 원하는 버전을 선택하여 자유롭게 사용할 수 있습니다.

즉, 마이크로 서비스식 아키텍처를 쉽게 구성할 수 있습니다.

RDS의 가장 큰 장점은 사용자가 관리를 하지 않아도 된다는 점인데, 

예를들어 이중화를 해야 한다거나, 분산처리를 해야 한다거나 할 때, 이러한 부분을 AWS에서 지원을 해줍니다.

따라서 버튼 하나만 클릭하면 이중화가 되고, 백업, 마이그레이션 등이 가능해 짐니다.

# Cloudformation

```yaml
Type: AWS::RDS::DBInstance
Properties: 
  AllocatedStorage: String
  AllowMajorVersionUpgrade: Boolean
  AssociatedRoles: 
    - DBInstanceRole
  AutoMinorVersionUpgrade: Boolean
  AvailabilityZone: String
  BackupRetentionPeriod: Integer
  CACertificateIdentifier: String
  CharacterSetName: String
  CopyTagsToSnapshot: Boolean
  DBClusterIdentifier: String
  DBInstanceClass: String
  DBInstanceIdentifier: String
  DBName: String
  DBParameterGroupName: String
  DBSecurityGroups: 
    - String
  DBSnapshotIdentifier: String
  DBSubnetGroupName: String
  DeleteAutomatedBackups: Boolean
  DeletionProtection: Boolean
  Domain: String
  DomainIAMRoleName: String
  EnableCloudwatchLogsExports: 
    - String
  EnableIAMDatabaseAuthentication: Boolean
  EnablePerformanceInsights: Boolean
  Engine: String
  EngineVersion: String
  Iops: Integer
  KmsKeyId: String
  LicenseModel: String
  MasterUsername: String
  MasterUserPassword: String
  MaxAllocatedStorage: Integer
  MonitoringInterval: Integer
  MonitoringRoleArn: String
  MultiAZ: Boolean
  OptionGroupName: String
  PerformanceInsightsKMSKeyId: String
  PerformanceInsightsRetentionPeriod: Integer
  Port: String
  PreferredBackupWindow: String
  PreferredMaintenanceWindow: String
  ProcessorFeatures: 
    - ProcessorFeature
  PromotionTier: Integer
  PubliclyAccessible: Boolean
  SourceDBInstanceIdentifier: String
  SourceRegion: String
  StorageEncrypted: Boolean
  StorageType: String
  Tags: 
    - Tag
  Timezone: String
  UseDefaultProcessorFeatures: Boolean
  VPCSecurityGroups: 
    - String
```

이중화를 하는 방법중 가장 쉬운 방법은 `MultiAZ:true` 로 바꿔주는 것입니다.

이렇게 하면 웹 콘솔상에서 보이는 RDS는 1개 이지만, 내부적으로 다른 하나가 다른 리전에서 복제되고 있으며,

Main RDS가 장애가 났을 경우 다른 리전에 있는 잠자고 있던 RDS가 살아나면서 Main을 대체해주는 형식으로 진행됩니다.

그 밖에도 Aurora DB를 사용하거나 `SourceDBInstanceIdentifier` 를 사용해 replica를 만들 수 있습니다.

