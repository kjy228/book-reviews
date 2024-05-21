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
