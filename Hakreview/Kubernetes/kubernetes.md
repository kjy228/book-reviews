## VM vs Container
### 공통점
host server가 올라가고 그위에 host os(linux, mac os, window)가 올라간다

### 차이점
`VM`

vm을 가상화 시켜주기 위한 hypervisor가 올라가고 그 위에 Geuest os, application이 차례대로 올라가게 된다. 
![image](https://user-images.githubusercontent.com/43670838/193718845-3d086821-75c7-44b8-aed5-8fca56a44f65.png)

`Container`

docker container 들이 여러 자워
리눅스마다 버전이 있고 버전마다 사용가능한 라이프러리가 다른다. 이럴때 다른버전의 리눅스에서 사용하게되면 버전차이 에러가 발생할 수 있다. 그래서 도커를 사용하며 여러 컨테이너간의 호스트 자원을 분리해서 사용하게된다. 
 - namespace : 커널에 관련된 영역을 분리(mnt, pid, net, ipc, uts, user)
 - cgroups : 자원에 대한 영역을 분리(memory, cpu, i/0, network)

쿠버네티스는 여러 컨테이너드를 pod 라는 개념으로 묶을수 있고 이것이 배포의 단위이다.
