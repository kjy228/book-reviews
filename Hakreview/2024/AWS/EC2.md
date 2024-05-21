AWS billing information은 IAM 사용자 접근을 허용하면 IAM 사용자도 접근할 수 있다.

# EC2
Elastic Compute Cloud의 약자이며 서비스형 인프라스트럭쳐이다.
- 가상머신 rental(EC2)
- 데이터를 가상 드라이브, EBZS볼륨에 저장(EBS)
- Elactric Load Balancer를 사용해 로드를 분산시킬 수 있다.(ELB)
- auto scaling을 통해 서비스를 확장 할 수 있다.(ASG)

## EC2 Sizing, configuration options
- OS 선택 : linux, windows, mac os
- cpu(core 수) 선택
- ram 선택
- storage space ( ebs, efs, ec2 instance store) 선택
- network card 선택 : ec2인스턴스에 연결할 네트워크 종류 선택, 속도가 빠른 카드를 사용할지 공용 IP를 사용할지   
- 방화벽 규칙 선택
- 인스턴스를 구성하기위한 bootstrap 스크립트(최초에는 user data만 있다)

### EC2 User data
- bootstrapping은 머신이 시작할때 최초에만 실행된다.부팅작업을 자동화 하기때문에 bootstrapping이라는 이름을 가지게된다.
- ec2 user data는 특정한 목적이 있다.
    - update, software 설치
    - 인터넷의 공용파일 다운로드

사용자 데이터 스크립트는 루트 계정에서 실행되기 때문에 `sudo` 로 실행해야된다.

## EC2 Types

### Instance naming convention
m5.2xlarge
- m : instance class
- 5 : 세대
- 2xlarge : 인스턴스 클래스의 크기

### 목적
- 범용 인스턴스 : 웹서버, 코드 저장소와 같은 다양한 작업에 적합하다.
- 컴퓨팅 최적화 인스턴스 : 컴퓨터 집약적인 작업에 최적화되어있으며 아래와 같은 목적으로 사용한다. 또한 C로 시작하는 명명규칙을 따른다.
    - batch processing
    - media transcoding
    - high performance web servers
    - high performance computing (HPC)
    - machine learning
    - 전용 gaming server
- 메모리 최적화 인스턴스 : 대규모 데이터셋을 처리하는 유형의 작업에 적합하다. 아래의 경우에 사용한다. 기본적으로 네임이 규칙은 R로 시작하지만 x1, z1와 같은 대용량 메모리도 있다.
    - 고성능 db
    - 분산 웹스케일 캐쉬 저장소
    - Business intelligence에 최적화된 인메모리 데이터베이스

- 스토리지 최적화 인스턴스
로컬 스토리지에서 대규모 데이터셋에 접근할떄 적합한 인스턴스.아래의 경우에 사용한다. 기본적으로 네이밍 규칙은 I,D,H1으로 시작한다.
    - OLTP(High frequency online transaction processing) 시스템
    - database
    - 인메모리 db를 위한 cache(Redis)
    - data warehousing application
    - 분산파일 시스템
## Security Groups
- 트래픽 제어
- 허용 규칙만 포함
- ip주소를 참조해 규칙을 만들 수 있다. 보안그룹끼리도 참조할 수 있다.
- 통제 범위
    - 포트 접근
    - authorised ip ranges (IPv4, IPv6)
    - i/obound 네트워크

### Security Group이란
- 여러 인스턴스에 적용할 수 있다.
- region/VPC 조합으로 통제한다. 그래서 지역을 전환하면 새 보안그룹을 생성하거나 다른 VPC를 생성해야한다. 
- EC2 밖에 존재한다. 
- SSH 액세스를 위해 하나의 별도 보안 그룹을 유지하는것이 좋다.
- timeout으로 application에 접근할 수 없다면 보안그룹 문제다.
- 연결거부 오류가 발생하면 이건 application의 문제다.
- 기본적으로 모든 인바운드 트래픽은 거절, 아웃바운드는 허용된다.


### 다른 보안그룹을 참조하는 방법
