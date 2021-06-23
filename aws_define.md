현재 CloudFormation에서 지원하는 AWS 리소스들
Auto Scaling
RDS
CloudFront
Redshift
CloudWatch
Route 53
DynamoDB
S3
EC2
SimpleDB
ElastiCache
SNS
Elastic Beanstalk
SQS
Elastic Load Balancing
VPC
Identity and Access Management

<pre><code>
{

    "Description" : "이 곳에는 템플릿의 Description을 입력한다.",
  
    "Parameters": {
       // 스택을 생성할 때 사용자가 입력할 매개변수들이다.
    },
 
    "Resources" : {
       // AWS 리소스들의 설정 내용 그리고 리소스들간의 관계에 대해 정의할 수 있다.
    },
  
    "Outputs" : {
       // 스택을 생성 후 출력할 내용이다.
    },
  
    "AWSTemplateFormatVersion" : "2010-09-09"
    
 }
 </pre></code>
 

Description : 각 매개변수의 설명이다.

Type : 매개변수 타입이다. Keypair의 Type같이 AWS에서 지정된 타입이 올 수도 있으며 String과 같이 일반 문자열로 정의할 수도 있다.

Default : 매개변수의 기본값이다. Keypair의 경우에는 이미 있는 Key Pair의 이름을 사용하였다.

ConstraintDescription : 제약 조건에 대한 설명이다.

MinLength/MaxLength : 값에 대하여 제약 조건을 정의할 수 있다.


<p>
Lamda 기반 CloudFormation 확장하기 문서

https://aws.amazon.com/ko/blogs/korea/cloudformation-macros/

CloudFormation 으로 CloundWatch의 Alram기능 사용 문서

https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/aws-properties-cw-alarm.html

Using Cloud Watch with Cloud Formation

https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/quickref-cloudwatchlogs.html

Ref 함수

 

- 지정한 파라미터나 리소스의 값을 취득하는 함수

 

- 동적으로 값이 정해지는 항목을 템플릿에 기재할 때 사용 

(VPC와 Subnet을 만들 때 Subnet 측에서 VPC의 ID를 지정할 필요가 있다. 하지만 VPC의 ID는 구축 시에 동적으로 결정되므로 템플릿에 기재가 불가능 하다. 이런 경우 Ref 함수를 이용해 동적으로 정해지는 항목을 기재)

 

- 유사 파라미터라고 하는 AWS가 사전에 정의한 값을 Ref 함수에서 참조할 수 있음

(Ex. < !Ref "AWS::Region > 이라고 하면 CloudFormation이 실행된 리전을 취득 / < !Ref "AWS::AccountId" > 라 하면 실행 계정의 계정 ID를 취득)"

 

- Ref 함수를 잘 사용하면 리전이나 계정에 의존하지 않는 템플릿 작성 가능

 

- 사용 예제

SampleVPC :

    Type : 'AWS::EC2::VPC'

    Properties :

        CidrBlock : '192.168.0.0/16'

SamplePublicSubnet :

    Type : 'AWS::EC2::Subnet'

    Properties :

        AvailabilityZone : 'ap-northeast-1a'

        CidrBlock : '192.168.0.0/24'

        VpcId : !Ref SampleVPC
 

 

FindInMap 함수

 

- Mappings 섹션에서 값을 취득하는 함수

 

- 사전에 맵 형식의 정수를 정의할 수 있음

(Ex. EC2 인스턴스를 시작할 때 AMI의 이미지 ID를 지정해야 하지만 이 이미지 ID는 리전마다 다름, 그래서 Mappings 섹션에서 이미지 ID의 매핑을 정의함)

 

- 사용 예제

Mappings :

         RegionMap :

                us-east-1 :

                       hvm : "ami-a4c7edb2"

                 ap-northeast-1 :

                       hvm : "ami-3bd3c45c"
 

 이 RegionMap에 대해 Fn::FindInMap 함수를 사용해 이미지 ID를 지정하면 대상 리전에 연관된 이미지 ID를 취득할 수 있음

Ex. ImageID: !FindInMap [ RegionMap, !Ref "AWS::Region", hvm ] )

유사 파라미터와 Ref 함수를 조합해서 "!Ref "AWS::Region"" 으로 실행 리전을 취득하고 있음 (템플릿을 정의하는 것으로 리전 정보의 하드코딩을 피할 수 있음)

 

 

Select 함수

 

- Select는 리스트에서 지정한 값을 취득하는 함수

 

- 사용 예제

!Select [ "1", [ "ap-northeast-1a", "ap-northeast-1c" ] ]
 

ap-northeast-1c를 취득할 수 있음 (리스트의 선두를 0으로 나타내므로 여기서는 2번째 요소가 취득)

Fn::GetAZs 함수와 함께 사용하는 경우가 많음 

 

 

GetAZs 함수

 

- GetAZs는 지정한 리전의 가용 영역(AZ)의 리스트를 취득하는 함수

 

- 예를 들어 서브넷을 AZ마다 1개씩 작성하고 싶다면 Select 함수와 조합해서 작성

 

- 사용 예제

SamplePublicSubnet :

         Type : 'AWS::EC2::Subnet'

         Properties :

                 AvailabilityZone :

                        Fn::Select:

                              - 0

                              - Fn::GetAZs: ""

                 CidrBlock : '192.168.0.0/24'

                 VpcId : !Ref SampleVPC

SamplePublicSubnet2 : 

         Type : 'AWS::EC2::Subnet'

         Properties : 

                 AvailabilityZone :

                        Fn::Select:

                              - 1

                              - Fn::GetAZs: ""

                 CidrBlock : '192.168.1.0/24'

                  VpcId : !Ref SampleVPC
                    

 

ImportValue 함수

 

- 다른 스택에서 출력된 값(Output - export => importvalue)을 취득할때 사용 (값을 가져오는데만 사용)

 

- 시스템의 규모가 커지면 레이어마다 스택을 나누는 경우가 있음

(예를 들어 VPC나 서브넷을 만드는 네트워크 스택과 EC2 인스턴스를 생성하는 서버 스택처럼 분리해서 템플릿을 단순하게 만듬, 이때 네트워크 스택이 만든 서브넷의 ID를 서버 스택 측에서 참조해야 하지만 이런 경우에 ImportValue 함수를 이용하면 서브넷 ID를 취득할 수 있음)

 

- 동일 리전에서만 값을 가져올 수 있고 다른 스택에서 참조중인 스택은 수정하거나 삭제할 수 없음

 

- 출력의 경우 리소스에 의존하는 Ref 또는 GetAtt 함수를 Export의 Name 속성값으로 사용할 수 없음

 

- 선언 방식

1) JSON

{ “Fn::ImportValue” : sharedValueToImport }
 

2) YAML

Fn::ImportValue: sharedvalueToImport 또는 !ImportValue sharedValueToImport
!ImportValue가 포함되면 !Sub의 짧은 형식을 사용할 수 없음 (CloudFormation은 유효하지만 YAML에 대해 유효하지 않음)

!ImportValue

   !Sub “{NetworkStack}-SubnetID

Fn::ImportValue:

    !Sub "${NetworkStack}-SubnetID"
 

- 사용 예제 (스택 A에서 스택 B로 값 내보내기)

1) 스택 A (Output 스택에서 Export로 값 내보내기)

Outputs :

    VPC

       Description : reference VPC

       Value : !Ref VPC

         Export :

              Name : ProdVPC
 

2) 스택 B (Resources 스택에서 ImportValue 함수로 값 가져오기)

Resources :

   MyTargetGroup :

       Type : AWS::ELBV2::TargetGroup

       Properties :

           VpcId :

                Fn::ImportValue: ProdVPC
 

Sub 함수

 

- 입력 문자열의 변수를 지정한 값으로 바꿈

 

- 템플릿에 이 함수를 사용해 스택을 생성하거나 업데이트해야만 사용할 수 있는 값이 포함된 명령 또는 출력을 만들 수 있음

 

- 템플릿 파라미터, 리소스의 논리적 ID 또는 String 파라미터의 리소스 속성만 바꾸는 경우 변수 맵을 지정하면 안됨

 

- 사용예제-1

Fn::Sub:

  - String

  - { Var1Name: Var1Value, Var2Name: Var2Value }
 

String 

변수가 포함된 문자열로 CloudFormation은 실행 시간 시 이를 연결된 값으로 바꿈, 변수를 ${MyVarName}으로 사용

변수는 템플릿 파라미터 이름, 리소스의 논리적 ID, 리소스 속성 또는 키-값 맵의 변수일 수 있음

템플릿 파라미터 이름, 리소스의 논리적 ID 및 리소스 속성만 지정하는 경우에는 키-값 맵을 지정하면 안됨

템플릿 파라미터 이름 또는 리소스의 논리적 ID(Ex. ${InstanceTypeParameter})를 지정하는 경우 CloudFormation에서는 Ref 내장 함수를 사용했을 때와 동일한 값을 반환

리소스 속성(Ex. ${MyInstance.PublicIp})을 지정하는 경우 CloudFormation에서는 Fn::GetAtt 내장함수를 사용했을 때와 동일한 값을 반환

VarName

String 파라미터에 포함된 변수의 이름

VarValue

CloudFormation은 실행 시간 시 연결된 변수 이름을 이 값으로 바꿈

 

- 사용 예제-2 (매핑을 사용하여 ${Domain} 변수를 Ref 함수의 결과 값으로 바꿈)

Name: !Sub

  - www.${Domain}

  - { Domain: !Ref RootDomainName }
 

${Domain}의 값이 test라면 해당 값을 Ref 함수를 사용하여 RootDomainName의 값으로 변경

 

 

GetAtt 함수

 

- 템플릿의 리소스에서 속성 값을 반환

 

- 사용 예제-1

Fn::GetAtt: [ logicalNameOfResource, attributeName ]
 

logicalNameOfResource : 원하는 속성을 포함하는 리소스의 논리적 이름

attributeName : 값을 가져올 리소스 관련 속성의 이름

(쉽게 말해서) logicalNameOfResource객체의 attributeName를 반환한다.
 

- 사용 예제-2 (논리적 이름이 myELB인 로드밸런서의 DNS 이름을 포함하는 문자열을 반환)

!GetAtt myELB.DNSName
 

 

Join 함수

 

- 지정된 구분 기호로 구분된 값 세트를 단일 값에 추가 (구분 기호가 빈 문자열이면 값 세트는 구분 기호 없이 연결)

 

- 사용 예제-1

Fn::Join: [ delimiter, [ comma-delimited list of values ] ]
delimiter : 조각 두 개 사이에서 발생하길 원하는 값 (구분 기호는 조각 사이에서만 발생하고 최종값을 종료하지 않음)

ListOfValues : 결합하려는 값 목록

 

- 사용 예제-2 (a:b:c를 반환)

!Join [ ":", [ a, b, c ] ]
 

- 사용 예제-3

!Join

  - ''  # 빈 문자열로 구분 기호 없이 연결

  - - 'arn:'

    - !Ref Partition

    - ':s3:::elasticbeanstalk-*-'

    - !Ref 'AWS::AccountId'
 

출력 결과 - arn:!Ref Partition:s3:::elasticbeanstalk-*-:!Ref ‘AWS::AccountId'

 
ARN

aws의 고유한 리소스라는 뜻.

 IAM(Amazon Relational Database Service) 태그 및 API 호출과 같은 모든 Amazon RDS에서 리소스를 명료하게 지정해야 하는 경우 ARN이 필요합니다.
 

Split 함수

 

- 결과로 얻은 문자열 목록에서 요소를 선택할 수 있도록 문자열을 분할

 

- 쉽표(,)와 같은 구분 기호를 사용하여 분할 위치 지정

 

- 문자열 분할한 후 Fn::Select 함수를 사용하여 특정 요소를 선택

 

- 사용 예제-1

Fn::Split: [ delimiter, source string ]  # 전체 함수 이름 구문



!Split [ delimiter, source string ]  # 짧은 형식 구문
 

delimiter : 원본 문자열을 분할할 위치를 결정하는 문자열 값

source string : 분할할 문자열 값

 

- 사용 예제-2 ([“a”, “b”, “c”] 를 반환, 각 세로 막대[ | ]에서 문자열을 분할하는 예제)

!Split [ "|" , "a|b|c" ]
 

- 사용 예제-3 (연속하는 구분 기호로 문자열을 분할할 경우 결과 목록에 빈 문자열이 포함, 연속하는 구분 기호와 추가 구분 기호로 문자열을 분할하는 방법의 예제, [“a”, ””, ”c”, “”]를 반환)

!Split [ "|" , "a||c|" ]
 

- 사용 예제-4 (Fn::Select 함수에 지정된 대로 가져온 출력 값을 분할한 다음 서브넷 ID의 결과 목록에서 세번째 요소를 선택하는 예제)

!Select [2, !Split [",", !ImportValue AccountSubnetIDs]]
 

 

Transform 함수

 

- 스택 템플릿의 일부부분인 사용자 지정 처리를 수행하는 매크로를 지정

 

- 매크로를 통해 찾기 및 바꾸기 작업 같은 간단한 작업부터 전체 템플릿 변형과 같은 작업까지 템플릿에 대한 사용자 지정 처리를 수행

 

- 사용 예제-1

Fn::Transform:  # 전체 함수 구문의 이름

  Name : macro name

  Parameters :

          Key : value



!Transform { "Name" : macro name, "Parameters" : {key : value, ... } }  # 짧은 형식의 구문
 

Name : 처리를 수행하고자 하는 매크로의 이름

Parameters : 매크로에 전달하는 키-값 페어로 지정된 목록 파라미터

 

- 사용 예제-2 (AWS::Include 변형을 호출하여 템플릿 코드 조각을 검색하는 위치가 InputValue 파라미터에 전달되도록 지정)

'Fn::Transform':

    Name: 'AWS::Include'

    Parameters: {Location: {Ref: InputValue}}
 

- 사용 예제-3 (AWS::Include 변형 호출하여 템플릿 코드 조각을 검색하는 위치가 RegionMap 매핑에 위치하고, 키는 us-east-1, 중첩된 키가 s3Location에 있음을 지정)

'Fn::Transform':

    Name: 'AWS::Include'

    Parameters: {Location: {'Fn::FindInMap': [RegionMap, us-east-1, s3Location]}}
 

 

Base64 함수

 

- 입력 문자열의 Base64 표시를 반환

 

- 일반적으로 UserData 속성을 통해 인코딩된 데이터를 EC2 인스턴스에 전달하는데 사용

 

- 사용 예제-1

Fn::Base64: valueToEncode  # 전체 함수 이름 구문



!Base64 valueToEncode  # 짧은 형식의 구문
valueToEncode : Base64로 변환할 문자열 값

 

- 사용 예제-2 (짧은 형식을 사용하고 valueToEncode 파라미터에 다른 함수를 바로 포함할 경우 최소 하나 이상의 함수에 대해 전체이름을 사용해야 함)

!Base64 !Sub string

!Base64 !Ref logical_ID

=> 잘못된 구문

!Base64

  "Fn::Sub": string
 

=> 정상적 구문

Fn::Base64:

  !Sub string
 

 

Cidr 함수

 

- CIDR 주소 블록의 배열을 반환 (반환된 CIDR 블록의 수는 count 파라미터에 따라 다름)

 

- 사용 예제-1

Fn::Cidr:  # 전체 함수 구문

  - ipBlock

  - count

  - cidrBits
  
  !Cidr [ ipBlock, count, cidrBits ]  # 짧은 형식 구문
 

 

 

ipBlock : 작은 CIDR 블록으로 분활되는 사용자 지정 CIDR 주소 블록

count : 생성할 CIDR의 수 (유효 범위는 1-256)

cidrBits : CIDR에 대한 서브넷 비트 수 (해당 파라미터에 대한 값으로 8을 지정하면 24마스크의 CIDR이 생성, 32Bit-8Bit=24Bit)

 

- 사용 예제-2 (24마스크의 CIDR 내부에서 27 서브넷 마스크의 CIDR 6개를 생성)

!Cidr [ "192.168.0.0/24", 6, 5 ]
 

- 사용 예제-3 (IPv6 활성화 서브넷 생성)

Resources:

    ExampleVpc:

        Type: AWS::EC2::VPC

        Properties:

            CidrBlock: "10.0.0.0/16"

     IPv6CidrBlock:

        Type: AWS::EC2::VPCCidrBlock

        Properties:

            AmazonProvidedIpv6CidrBlock: true

            VpcId: !Ref ExampleVpc

     ExampleSubnet:

        Type: AWS::EC2::Subnet

        DependsOn: IPv6CidrBlock

        Properties:

            AssignIpv6AddressOnCreation: true

            CidrBlock: !Select [ 0, !Cidr [ !GetAtt ExampleVpc.CidrBlock, 1, 8 ]]

            Ipv6CidrBlock: !Select [ 0, !Cidr [ !Select [ 0, !GetAtt ExampleVpc.Ipv6CidrBlocks], 1, 64 ]]

            VpcId: !Ref ExampleVpc


    labmda를 생성하는 방법은 2가지가 있다.
    
    aws::lambda::function
    aws::serverless::function
    
    lambda function은 일반적인 람다함수 생성으로 룰 같은것들을 지정해줘야한다.
    
    serverless 같은경우 ssm기반으로 동작하며, 람다 함수에 대한 event, role등을 직접 정의해줄 수 있다.
  
    
    airflow 같은 경우에는 내부적으로 sqs, kms를 사용한다.
    
    또한 s3 버킷도 사용하는데 이때 앞에 이름을 airflow로 반드시 정해줘야 한다.
    
    s3에 dags 디렉토리를 넣어둔 후 그 디렉토리에 실행할 스케줄러 스크립트를 저장하면,
   
    알아서 해당 데이터를 가져와 실행시킨다.
    
    이때 s3 버킷은 버전관리를 켜줘야한다.(필수다)
    
    https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-mwaa-environment.html
    
    해당 documents를 참조하자.

    <h1> cloudFront </h1>
    cloudfront에서 cache는 일부 지역을 제외하곤 이전버전을 사용하지 못한다.
    
    이전 버전에 대한 내용이다.
    
    https://aws.amazon.com/ko/elasticache/previous-generation/
    
    
    <h1> aws ElastiCache Redis </h1>
    
    aws에서 제공해주는 ElastiCache를 사용하여 좀 더 빠른 처리가 가능하다.
    
    사용자는 클러스터 형태, memcached형태로 나눌 수 있으며 클러스터 형태는 스냅샷을 기준으로 데이터를 저장한다.
    
    스냅샷을 기준으로 저장할 때에는 S3 버킷에 해당 스냅샷을 넣어두게 되는데, 확장자명은 .rdb로 끝나야 한다.
    
    해당 객체를 S3에 저장하면, 각 객체에 대해서 권한을 줘야한다.
    
    S3같은 경우 policy를 줘서 해당 객체에 접근 할 수 있도록 해줘야 하고,
    
    스냅샷 객체는 피부여자를 추가해 줘야 한다.
    
    https://docs.aws.amazon.com/ko_kr/AmazonElastiCache/latest/red-ug/backups-seeding-redis.html#backups-seeding-redis-default-region
    
    해당 링크에서 확인할 수 있으며, 피부여자 ID는 540804c33a284a299d2547575ce1010f2312ef3da9b3a053c8bc45bf233e4353 로 정의되어야 한다.
    
    물론, 특정 리전에 대해선 다르게 줘야하며 이도 해당 링크의 aws documents에 나와있다.
    
    cloudformation에 대해선 AWS :: ElastiCache :: ReplicationGroup 를 검색하거나 아래 링크를 클릭하면 된다.
    
    https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/aws-resource-elasticache-replicationgroup.html#cfn-elasticache-replicationgroup-numnodegroups
    
    ElastiCache의 노드를 선정할 때 각 리전마다 지원되는 노드가 있고, 지원 안되는 노드가 있다.
    
    이는 AWS에서 지속적인 업데이트를 하면서 이전 세대와 현재 새대를 구분지어 최신화 시키려고 하기 때문이다.
    
    https://aws.amazon.com/ko/elasticache/previous-generation/
    
    해당 링크를 통해 확인이 가능하니 참고 하도록 하자.
