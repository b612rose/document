# AWS 기본
- AWS : IT 인프라, 전체 가용량을 무제한으로 인식
- 주요 서비스
    - Compute : EC2(가상서버), ELB(로드밸랜서)
    - Storage : S3(객체스토리지), CloudFront(CDN)
    - Database : RDS(RDBMS 관리형 서비스), DynamoDB(NoSQL 관리형서비스)
    - Network : VPD(논리적 네트워크 서비스), Route53(DNS)
- 물리 인프라
    - AZ : 데이터센터의 집합, 물리적으로 독립적, 2개이상 사용시 가용성 증가
    - Region : 두개 이상의 AZ 집합, 도시이름으로 명명
    - Edge : 속도에 민감한 서비스(CloudFront, Route53)용 서버팜, 공항코드로 명명
- 과금방식 : Compute(EC2), Stroage(S3, EBS), Data Transfer

# IAM
## IAM
- IAM : 리소스 제어권한, Identity & Access Management
## AWS API
- 모든 요청은 API 문법으로 AWS Endpoint로 요청함
- API 검증
    - 인증 : 인증된 사용자인가? Authentification
    - 권한부여 : 사전에 정의된 권한을 부여. Authorization
- API 사용방법
    - AWS Management Console (WEB으로 접근)
    - AWS CLI
    - AWS SDK : Java, PHP 등
## 인증 및 권한
- 인증 : IAM User, IAM Group, IAM Role
- 권한 : IAM Policy
- 인증 및 권한
    - IAM User + IAM Policy
    - IAM Group + IAM Policy
    - IAM Role + IAM Policy
## root Acount
- 가급적 사용하지 않는다
- 사용시기
    - CloudFront 전용 인증키 페어 생성시
    - EC2의 TCP 25 포트 제약 해제시
## IAM User
- 인증방식 : ID/PW(콘솔용), 인증키(CLI,SDK)
- 1개 이상의 IAM Policy 객체를 Mapping하여 권한을 정의

## IAM Group
- 1개 이상의 IAM User를 묶어서 관리
- Group에 User를 넣고 Policy를 일괄 적용
- 1개의 User는 여러 Group에 포함할 수 있다.

## IAM Role
- 외부 연동시
    - 신뢰할 수 있는 객체가 일정주기로 API 요청한다면, 유효기간이 있는 임시키 발급절차를 통해 응답을 수행
- AWS 서비스간 연동시
    - EC2가 S3에 접근시 인증키 대신 IAM Role을 정의하여 접근 (단, 임시인증키는 주기적으로 재생성 해야함)    

## IAM Policy
- IAM User, IAM Group, IAM Role의 권한을 정의하는 객체
- JSON 포멧
- Managed Policies 객체(AWS가 미리 만들어준 권한) 사용 권장 : 이식성 증가, 버전관리 기능

## Cloudtrail, Config
- 계정 생성 후 안전한 사용을 위해 Cloudtrail, Config 서비스 사용을 권장
- Cloudtrail
    - API Call을 기록하여 사후 추적에 사용
    - S3 버킷에 객체로 기록함
    - [IAM Policy Simulator](https://policysim.aws.amazon.com/) : 정의한 권한을 시뮬레이트 
- Config
    - 리소스 생성/수정/삭제를 기록하여 보안/감사에 사용
    - S3 버킷에 객체로 기록함
    - AWS Config Rules : 주기적으로 아래와 같은 검사를 수행
        - Cloudtrail 활성화 여부
        - ENI에 EIP 매핑 여부
        - EBS 볼륨 암호화 여부
        - root 계정에 MFA(추가인증도구, OTP등) 설정 여부

## ARN
- ARN : AWS Resource Name
- 형태 : arn:aws:서비스:리전:계정ID:리소스타입/리소스ID
- 예제 : arn:aws:ec2:ap-northeast-2:12345:intance/i-3dd4ff12





