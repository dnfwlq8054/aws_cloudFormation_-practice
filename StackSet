
StackSet을 사용하면 크로스 리전으로 cloudformation을 배포할 수 있다.

StackSet을 사용하려면 다음 권한이 필요하다.

```
  AdministrationRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: AWSCloudFormationStackSetAdministrationRole
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service: cloudformation.amazonaws.com
            Action:
              - sts:AssumeRole
      Path: /
      Policies:
        - PolicyName: AssumeRole-AWSCloudFormationStackSetExecutionRole
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Action:
                  - sts:AssumeRole
                Resource:
                  - "arn:*:iam::*:role/AWSCloudFormationStackSetExecutionRole"
  
  ExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: AWSCloudFormationStackSetExecutionRole
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              AWS:
                - !Ref 'AWS::AccountId'
            Action:
              - sts:AssumeRole
      Path: /
      ManagedPolicyArns:
        - !Sub arn:${AWS::Partition}:iam::aws:policy/AdministratorAccess
        
```

StackSet은 다른리전으로 될 때 권한없는 임시 계정이 내부적으로 생성되어 해당 계정이 cloudformation을 stack에 올리는 형태가 된다.

해당 계정은 아무것도 권한을 가진게 없기 때문에 Default로 위 권한들을 찾게되어 있다. 

StackSet은 다음과 같은 형태로 작성하면 된다.

```
StackSetResource:
    Type: 'AWS::CloudFormation::StackSet'
    Properties:
      TemplateURL: # Template URL 
      Capabilities: 
        - CAPABILITY_NAMED_IAM
      Tags:
        - Key: Source
          Value: StackSet Example
      Parameters:
        - ParameterKey: AvailabilityZones
          ParameterValue: !FindInMap [ Parameters, AvailabilityZones, prod ]
      StackInstancesGroup:
        - DeploymentTargets:
            Accounts: 
              - !Ref 'AWS::AccountId'
          Regions: 
            - ap-northeast-1 # Tokyo
      StackSetName: HwanStackSet
      PermissionModel: SELF_MANAGED

```

위 샘플 코드는 서울리전에서 cloudformation으로 스택에 올렸을 때, 해당 StackSetResource는 도쿄 리전에 생성된다.

