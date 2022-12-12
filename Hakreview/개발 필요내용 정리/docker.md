사전 준비사항
spring project 생성

docker desktop 설치

ide에 Docker plugin 설치

 

Docker 명령어, option 정리 
docker build : 도커파일 빌다 

docker run : 도커 실행

 

option 

-t : container

-p : port  
ex: docker run -p 1000:8080 [containerName] : 1000포트로 접근시 원격서버의 8080포트가 실행됨. (외부로 포트를 노출시키지 않기위해 사용) 

-it : -i + -t 이며 컨테이너를 대화형으로 실행하는 커맨드라고 생각하면 됨

-d : 공식적으로 문서에서 분리됨을 의미 이옵션을 사용하면 실제로 백그라운드에서 실행되어 로그가 보이지 않는다 → docker conatainer logs [containerid] 를 통해 로그 확인 

 

간단한 도커 이미지 띄우기 
컨셉

ubuntu 환경의 컨테이너 사용할 예정

docker pull ubuntu

 

root directory에 Dockerfile 생성


//Docker file sample
FROM eclipse-temurin:17-jdk
VOLUME /tmp
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
FROM 
확장하고 있는 이미지를 명시 

MAINTAINER
이 이미지를 작성한 사람 명시 → 현재 docker docs에서는 deprecated 라고 되어있으나 예제에서는 현재사용중..(?) 
그래도 deprecated이니 LABEL을 사용하는것을 권장.

LABEL 
map처럼 key-value 형식으로 사용 



LABEL maintainer = "xxxx"
key-value 에 대한 제약이없어서 원하는대로 작성하면 된다.

 

ARG

Gradle :  ARG 의 디렉토리 위치를 build/libs/*.jar 

Maven : ARG 의 디렉토리 위치를 target/*.jar 로 작성

Dockerfile을 커맨더에서 실행한다는 것은 베이스 이미지 내부에서 Docker가 해당 커맨드를 실행해야된다는것을 의미

터미널 열어서 

`docker build -t [repository-name] . `
ex) docker build -t myorg/myapp . 

docker images 명령어를 통해 생성된 이미지 확인 (IMAGE ID 도 확인)


docker 앱으로도 생성된 이미지를 확인할 수 있음.

 

이미지 : 컨테이너의 정의이며 애플리케이션을 실행하는데 필요한 모든 소스코드와 환경이 들어 있다.

 

Image 생성 방법
1. 실행중인 컨테이너의 스냅샷으로 생성 방법
ubuntu image를 사용하여성 ( docker pull ubuntu)

docker container run -it ubuntu : 이미지를 터미널과 대화형으로 연결

연결된 ubuntu cli 에서 자바설지 : apt-get install -y opendjk-17-jdk

실습하면서 발생한 에러 사항
도커파일에  FROM eclipse-temurin:17-jdk-alpine 을 사용했을때 에러 발생 



해결방법
stackoverflow에서는 도커를 리셋하고 다시 빌드하면 해결가능하다하였으나 해결 되지 않음 
alpine 제거 
원인은 모르겠음….

 

