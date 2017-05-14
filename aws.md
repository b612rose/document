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

# S3
## S3
- S3 : Simple Storage Service
## 객체 스토리지
- 여러 ZA에 내부 복제하여 가용성 증가
- 단점 : 복제 완료전에 사용자별로 다른 응답이 올 수도 있음
- 파일 수정 : 동일한 경로에 파일을 재생성하는 방식
- metadata : key-value, 부가정보를 저장
- 주소값 : Global Unique, 버킷명+키값+(버전ID)
- http(s)로 upload/download
## Bucket (바구니)
- 생성시 region 선택해야 함
- 버켓명 : Global Unique
- 소유자는 AWS 계정
- 버켓 단위로 기능을 on/off
- 버켓 단위로 접근제어 가능
    - Bucket ACL : 예전방식, 비권장
    - Bucket Policy : 권장
## Object (파일,metadata)
- 접근제어 : Object ACL
## S3 부가기능
- Static Website Hosting : 정적컨텐츠(image, video, html..) 저장용
- Versioning : 버켓단위로 버전별 보관 ▶ 이전버전의 데이터 보관에 대한 공간 요금 지불됨
- Lifecycle : 컨텐츠 백업, 로그저장, 필요시 보관주기 경과된 데이터 삭제/이관
## 기타
- S3 앞에 CloudFront를 설치하여 Cache Layer를 구성하면 S3 부하를 줄일 수 있음
- 접근주소
    - Website endpoint : 버켓이름.s3.리전.amazonaws.com/경로/파일
    - API endpoint : s3.리전.amazonaws.com/버켓이름/경로/파일
- S3용 GUI툴    
    - [CloudBerry Explorer](https://www.cloudberrylab.com/explorer/amazon-s3.aspx)
    - [S3 Browser](http://s3browser.com/)

# VPC
## VPC
- VPC : Virtual Private Cloud
    - Virtual : 가상 네트워크
    - Private : 사설IP, 외부와 통신하려면 IGW(Internet Gateway) 또는 VPG(Vitual Private Gateway) 필요

## CIDR 주소값
- 10.0.0.0/16 ▶ 00001010.00000000.00000000.00000000
    - 00001010.00000000 : network ID - 네트워크 주소영역은 동일값을 가짐
    - 00000000.00000000 : host ID - 호스트별 상이한 객체

## VPC 환경구성
1. VPC 객체 생성
    - VPC : VPC Subnet의 집합 (따라서 여러 AZ를 포함. subnet의 사설IP 주소대역을 포함해야 함)
    - 하나의 region에 구성
2. VPC 하위객체로 VPC Subnet 생성
    - VPC Subnet : 하나의 AZ에 구성, 생성후 변경 못함
    - VPC Subnet 생성후 
        - Route Table 설정 : 목적지
        - Network ACL 설정 : 방화벽

## VPC 관리 객체
- ENI : Elastic Network Interface
    - 일종의 네트워크 카드 (NIC)        
    - 기본적으로 1개의 사설IP 가짐 (추가 가능)
    - EC2에는 여러개의 ENI 가능 (따라서 여러개 사설IP 가능)
    - 방화벽 기능 : Security Gruop을 최대 5개까지 매핑
- Network ACL : VPC Subnet의 방화벽 (In/Out 접근제어)
- Route Table : 목적지 지정 - IGW(인터넷 연결구간), VPG(사설네트워크 연결), ...

| Dest         | Target  |설명                                                          |
|--------------|---------|--------------------------------------------------------------|
| 10.0.0.0/16  | local	 | 10.0.[0~255].[0~255], 10.0.*.*는 더 구체적인 경로인 이것을 사용 |
| 0.0.0.0/0    | IGW     | 이전에 지정되지 않은 모든 IP 주소값 (Anywhere)                  |

## VPC Subnet 구분
- Public Subnet : Route Table에 IGW로 가는 Routing 있음
- Private Subnet : Route Table에 IGW로 가는 Routing 없음

## VPC 제공 서비스
- VPC Peering
    - VPC간 통신
    - 조건 : 같은 region 이여야 함. 중복안되는 사설 IP여야 함
    - 설정 : 한쪽이 VPC Peering 신청, 다른쪽이 수락, 양쪽이 Routing Table에 서로 추가

- NAT Gateway
    - 관리형 서비스 (인터넷 통신구간)    
    - 설정 : Elastic IP 생성 → public subnet에 NAT Gateway 객체 생성 → Routing Table에 추가

- VPC Endpoint    
    - S3는 public이므로 private subnet과 통신하기 위해 사용함

## 방화벽
- Security Group : ENI에서 동작, Stateful 방식
- Network ACL (NACL) : VPC Subnet에서 동작, stateless 방식 (IN/OUT 따로 설정)
- 보통 리소스간 통신은 Security Group, DDOS 거부정책은 NACL을 사용함

# EC2
## EC2 
- 가상서버
- 단위는 instance

## 기존 VM과 차이점
- ASIS : Host(물리서버), 하이퍼바이저(논리적플랫폼), Guest(하이퍼바이저 상의 OS)
- AWS : EC2(Guest, Elastic Compute Cloud), Instance Store(EC2가 점유한 디스크)

## EC2 임대형태
- Shared Instance : 1개의 Host, 여러 계정
- Dedicated Instance : 1개의 Host, 1명 계정
- Dedicated Host : 1개의 Host, 1명 계정

## Instance Type
- C4.2xlarge 일 경우
    - C4 : Instance Family (CPU, Memory, GPU 등의 스펙을 결정)
    - 2xlarge : Instance Size (Host를 4개로 분할하여 그중에서 1개를 사용)

## EC2 Action
- Reboot : 동일 Host 유지, IP/Store 유지
- Stop/Start : 새로운 Host/Store 초기화
- terminate

## 저장소
- Instance Store
    - Host 서버에 위치 (따라서 Host 변경시 삭제됨)
    - 별도의 비용 없음
- EBS (Elastic Block Storage)
    - Host 의존성 없음 (따라서 Host 변경시 유지됨)
    - Network 구간을 거쳐 Mount 됨 (여러개 마운드 가능)
    - snapshot 으로 백업
    - EBS-Backed Instance : EBS 볼륨이 루트, stop/start 가능
    - EBS는 동일 AZ 내에서 내부복제를 수행 (가용성)    
    - public IP는 변경될 수 있으므로, 고정IP로 사용하기 위해 EIP를 사용
    - EIP는 무료이나, 사용하지 않으면 과금됨

# RDS
## RDS
- RDBMS 용 관리형 서비스 (업무의 일부를 대신 관리)
- 설정 / 플러그인은 Parameter Group, Option Group으로 지원
- Aurora
    - MySQL 5.6 이상 완벽호환
    - 읽기복제본(read replica) 구성이 쉬움
    - 여러 AZ에 공유 가능
    - 데이터량에 따라 10GB 단위로 자동 증설
- Minor 수준의 패치는 자동으로 수행됨    

## Multi AZ
- 두개의 AZ에 Mater, Slave 노드로 구성 (이중화)
- mater는 slave에 log를 전송
- failover는 DNS가 수행함

## Read Replica
- 복수의 읽기복제본 만들어 읽기 부하를 분산
- write는 master에, mater는 slave에 log 전송, read는 slave
- 다른 region에도 생성 가능 (글로벌 서비스에 사용)

## Backup
- 자동 또는 수동
- 데이터 복원은 신규 RDS Instance를 만들어서 수행
- Multi AZ 사용시 slave의 스냅샷을 사용

# ELB
## LB (LoadBalancer)
- 서버 목록을 관리
- 각 서버의 상태를 체크
```
Client <--------> LoadBalancer <--------> node1 (11.0.0.1)
                               <--------> node2 (11.0.0.2)
                               <--------> node3 (11.0.0.3)
```                               

## ELB (Elastic LoadBalancer)
- AWS에서 제공하는 관리형 LoadBalancer
- EC2 기반의 서비스
    - Security Group으로 접근제어
    - 여러개의 EC2 Instance를 묶어주는 역할
- Endpoint는 도메인으로만 제공 (VIP는 차후 서비스될 예정)    
- ELB 노드는 요청에 따라 Scale up/down/in/out
- ELB 노드를 미리 늘려놓으려면 ELB Prewarm 을 신청해야 함

## ELB 형태
- Classic LB
    - HTTP(S), TCP, SSL
    - 백엔드에 EC2 Instance를 설정
    - Layer4 기반 : 일반적 웹서비스에 사용        
- Application LB
    - HTTP(S), HTTP/2, websocket
    - 백엔드에 Target Group을 설정
    - Layer7 기반 : Docker, Websocket 등에 사용

## ELB 종류
- ELB 생성시 Internet Facing 여부 설정 : External ELB / Internal ELB

## ELB 기능
- Health Check : 비정상 서버로 메시지 전달하지 않음
- SSL Termination : SSL 처리를 수행(ELB에 인증서 설치 필요), 백엔드는 평문으로 통신
- Sticky Session : HTTP 쿠키 관리
- Crosszone LB : 다른 AZ로도 LB 수행, Application ELB는 기본값 설정됨

# Auto Scaling
## Auto Scaling
- EC2 Instance 수를 자동으로 조절 (Scale in/out)
- Scale in시 고려사항
    - 저장할 내용은 없는가?
    - 전달할 내용은 없는가?
    - 어느 Instance를 먼저 삭제할 것인가?
- Scale out시 고려사항
    - 어떤 AMI 사용할 것인가?
    - 어느 위치에?
    - 설정은?

## Auto Scaling 구성절차
1. Launch Configuration 설정
    - 사용할 AMI 결정 : 보통 생성한 EC2 Instance에서 이미지를 생성하여 사용함
    - 환경값은 구성시 Userdata 활용한다.
2. Autoscaling Group 설정
    - Launch Configuration 객체 생성
    - VPC, VPC Subnet 설정
    - ELB 매핑 설정
    - Health Check 조건 설정 : 가급적이면 Health check는 ELB를 사용할것
3. Scaling Policy
    - Manual : 수동
    - Scheduled : 시간에 따라
    - Dynamic : 조건에 따라
        - Cloudwatch 에서 Alarm 발행할때
        - Simple 방식 : 1개의 임계치 alrarm
        - Step 방식 : 여러개 임계치 alrarm

# CloudFront
## CDN
- CDN : Content Delivery Network
- 캐시 서버 분산 (Global)        
- 요청에 가까운 위치의 Cache Server에서 응답
## CloudFront
- Resion과 별개로 70여개 지역에 Cache 서버 분산됨
- Edge : 국가별로 2개 이상의 cache 서버를 묶음 
- 특정 서버 앞단에 배치하여 global 서비스 가능
## 동작원리
1. 캐싱
    - TTL (time to live, Edge에 보관되는 시간)
        - 자주 변경되는 컨텐츠 : TTL 小
        - 자주 변경안되는 컨텐츠 : TTL 大
    - 응답
        - miss : 없을때
        - hit : 있을때        
        - refresh hit : 없어서 컨텐츠 확인 후 똑같을때
        - miss : 없어서 컨텐츠 확인 후 다를때
    - 컨텐츠 갱신
        - Invalidate 수행하여 모든 edge에서 초기화 가능
        - 초기화에 10~20분 소요
2. 라우팅
    - HTTP(S), RTMP 지원
        - HTTP 인증서는 Certificate Manager(amazon 제공)에서 무료 발급 가능 
        - 사용자~CloudFront 구간은 HTTPS, 백엔드는 HTTP 권장
        - RTMS는 Live불가, VOD만 가능
    - 보안
        - Signed URL/Cookie : 인증된 사용자(결제한 사용자) 서비스 제공
        - Restriction : 국가별 제약에 사용 
        - WAF(웹 방화벽) 연동
            - L3/L4의 DDOS 공격은 CloudFront로 방어
            - L7은 WAF 연동하여 보호

    - 통계 제공 : 요청수, 사용자 device 등








