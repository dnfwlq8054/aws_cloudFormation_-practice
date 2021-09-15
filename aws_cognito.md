# AWS cognito

Amazon Cognito 는 웹 및 모바일 앱에 대한 인증, 권한 부여 및 사용자 관리를 제공합니다. 사용자는 사용자 이름과 암호를 사용하여 직접 로그인하거나 Facebook, Amazon, Google 또는 Apple 같은 타사를 통해 로그인할 수 있습니다.

Amazon Cognito의 두 가지 주요 구성 요소는 사용자 풀과 자격 증명 풀입니다. 사용자 풀은 앱 사용자의 가입 및 로그인 옵션을 제공하는 사용자 디렉터리입니다. 자격 증명 풀을 통해 사용자에게 기타 AWS 서비스에 액세스할 수 있는 권한을 부여할 수 있습니다. 자격 증명 풀과 사용자 풀을 별도로 또는 함께 사용할 수 있습니다.

코그니토를 통해서 회원가입, 로그인 등을 관리할 수 있으며, 사용자 계정들 또한 관리가 가능하다.

코그니토 정책을 통해, 관리자만 회원가입을 시킬지, 클라이언트도 회원가입을 시킬지 결정할 수 있다.

aws cognito를 이용하면 비교적 쉽게 사용자들을 관리할 수 있게 된다. (jwt 까지)

https://docs.aws.amazon.com/ko_kr/cognito/latest/developerguide/what-is-amazon-cognito.html

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-cognito-userpool.html