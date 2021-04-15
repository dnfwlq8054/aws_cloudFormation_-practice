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
