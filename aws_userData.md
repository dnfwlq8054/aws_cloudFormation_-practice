# cloudofrmation ec2 userdata

EC2를 생성할 때 UserData를 넣어줘서 인스턴스가 실행될 때, 자동으로 스크립트를 실행하도록 설정할 수 있다.(root 권한으로)

reference : https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html

샘플코드는 다음과 같다.
```yaml
Ec2Instance: 
  Type: AWS::EC2::Instance
  Properties: 
    ImageId: 
      Fn::FindInMap: 
        - "RegionMap"
        - Ref: "AWS::Region"
        - "AMI"
    KeyName: 
      Ref: "KeyName"
    NetworkInterfaces: 
      - AssociatePublicIpAddress: "true"
        DeviceIndex: "0"
        GroupSet: 
          - Ref: "myVPCEC2SecurityGroup"
        SubnetId: 
          Ref: "PublicSubnet"
```

여기에 유저 데이터를 넣어보자

```yaml
Parameters:
  Value:
    Type: String
    Default: Test

Resources:
  Ec2Instance: 
    Type: AWS::EC2::Instance
    Properties: 
      ImageId: !FindInMap [ RegionMap, !Ref AWS::Region, AMI ]
      KeyName: 
        Ref: "KeyName"
      NetworkInterfaces: 
        - AssociatePublicIpAddress: "true"
          DeviceIndex: "0"
          GroupSet: 
            - Ref: "myVPCEC2SecurityGroup"
          SubnetId: 
            Ref: "PublicSubnet"
      UserData:
        Fn::Base64: !Sub 
          - |
            #!/bin/bash -xe
            echo 'hi';
            echo ${Enviroment} is it!! >> text.txt;
            {
              Enviroment: !Ref Value
            }
        
```

이런식으로 가능하다.

그 외 userData에 다양한 도움을 주는 도구들이 존재한다. 일명 핼퍼 스크립트라고 불리운다.

1. cfn-init : https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/cfn-init.html
2. cfn-signal : https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/cfn-signal.html
3. cfn-get-metadata : https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/cfn-get-metadata.html
4. cfn-hup : https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/cfn-hup.html

