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

### Classic ports to know
- 22 : SSH linux에서 ec2 인스턴스로 로그인 가능하도록 함
- 21 : FTP 파일전송 프로토콜, 파일을 업로드하는데 사용됨
- 22 : SFTP(Secure File Transfer Protocol) ssh를 이용한 파일업로드
- 80 : http 접근용
- 433 : https 접근용
- 3389 : RDP(Remote DeskTop Protocol) 윈도우 인스턴스에 로그인할때 사용

## SSH
CLI를 통해서 원격머신이나 서버를 제어할 수 있게 해준다.
### SSH 실행방법
```
### key file directory 이동
### public ip 확인후 아래 command 입력
ssh -i EC2Tutorial.pem ec2-user@3.87.167.84
### 만약 권한이 없을경우
chmod 0400 EC2Tutorial.pem

### 입력후 ec2-user로 접속 되었다면 
whoami
```
### SSH Trouble shooting

1. 연결 시간 초과가 발생하는 경우

보안 그룹에 문제가 있는 경우 발생합니다. 모든 유형의 시간 초과(SSH뿐만 아니라)는 보안 그룹 또는 방화벽과 관련 있습니다. 보안 그룹이 다음과 같이 설정되었는지 확인하고, EC2 인스턴스에 올바르게 할당되었는지 확인

2. 연결 시간 초과 문제가 계속해서 발생할 경우

위와 같이 보안 그룹이 올바르게 설정되었으나 여전히 연결 시간 초과 문제가 발생하는 경우, 회사 방화벽 또는 개인 방화벽이 연결을 차단한다는 뜻입니다. 다음 강의에서 설명하는 대로 EC2 Instance Connect를 사용하세요.

3. SSH가 Windows에서 동작하지 않을 경우
- ssh command not found라고 뜨면 PuTTY를 사용해야 합니다.
- 동작하지 않는다면 다음 강의에서 설명하는 대로 EC2 Instance Connect를 사용하세요.

4. 연결이 거부될 경우

이는 인스턴스에는 도달할 수 있지만 해당 인스턴스에서 SSH 유틸리티가 실행되고 있지 않다는 뜻. 인스턴스를 재시작하세요.

여전히 동작하지 않는다면, 인스턴스를 종료하고 새 인스턴스를 만드세요. 반드시 Amazon Linux 2를 사용하세요.

5. 권한 거부(publickey,gssapi-keyex,gssapi-with-mic)

이는 다음의 두 상황에서 발생합니다.

- 보안 키가 틀렸거나 보안 키를 사용하지 않는 경우입니다. EC2 인스턴스 구성을 확인하여 올바른 키를 할당했는지 확인하세요.

- 잘못된 사용자를 사용할 경우에도 발생합니다. Amazon Linux 2 EC2 인스턴스에서 실행했는지 확인하고, ec2-user 사용자를 사용하고 있는지 확인하세요. SSH 명령어 또는 PuTTY 구성에서 ec2-user@<public-ip> (ex:ec2-user@35.180.242.162) 코드를 실행할 때 지정해야하는 사항입니다.


6. 어제는 연결이 가능했는데 오늘은 되지 않습니다.

EC2 인스턴스를 중단했다가 오늘 다시 시작했을 경우, 이런 일이 발생할 수 있습니다. 이 경우, EC2 인스턴스의 퍼블릭 IP가 변경됩니다. 따라서 명령어 또는 PuTTY 구성에서 새 퍼블릭 IP로 업데이트하여 저장.