![Docker](http://kimstar.kr/blog/wp-content/uploads/2017/05/docker.png)

# 도커 소개
## 도커
- 공식 홈페이지 : [Docker](https://www.docker.com)
- 리눅스에서 제공하는 컨테이너를 이용하여 애플리케이션을 묶어서 실행/배포
- 기존 VM에 비해 오버헤드가 적음
- 이미지 버전 관리가 쉬움
- 각 컨테이너 사이의 독립적 동작 환경
- 별도의 OS를 가상화 하지 않음. 직접 호스트 OS의 자원을 사용하여 VM에 비해 빠름
![VM vs Container](http://kimstar.kr/blog/wp-content/uploads/2017/05/docker-containers-vs-vms.png)
- 리눅스 커널 기반으로 만들어진 컨테이너 가상화 기술이므로 Host OS로 리눅스 커널 필요함
- 대표적인 Docker 전용 Linux 배포판
	- RHEL Atomic Host : kubernetes(컨테이너 운영툴) 설치됨, Cockpit(GUI 컨테이너 조작) 내장, RHEL subscription 구매해야 함
	- Core OS : 오픈소스중 가장 활발
- 참고 : 마스코드 고래는 Moby Dock, 등에 실은 것은 컨테이너	

## 도커의 기능
- 주요기능 : 이미지 생성, 컨테이너 동작, 이미지 공유
- 이미지
	- 실행파일, 라이브러리, 설정파일을 포함
	- 배포의 단위, 저장소에 pull/push
	- Docker Hub에서 공유
- 컨테이너
	- 이미지를 실행한 형태
	- Docker는 컨테이너 단위로 실행함
	- 하나의 커널에서 여러개의 컨테이너 실행 가능
![Images and Containers](http://kimstar.kr/blog/wp-content/uploads/2017/05/docker-archi.png)

## 도커 컴포넌트
- **Docker Engine** : 코어 기능 수행, Dockerfile을 통한 이미지 생성, 컴포넌트 구동, Docker 커맨드 실행
- Docker Kinematic : GUI 툴, 이미지 관리, 컴포넌트 구동
- Docker Registry : 이미지 공유
- Docker Compose : 여러 컨테이너를 통합 관리
- Docker Machine : 도커 실행환경을 커맨드로 생성하기 위한 툴
- Docker Swarm : 여러 도커 호스트를 클러스터화 하기 위한 툴
![Docker Component](http://kimstar.kr/blog/wp-content/uploads/2017/05/docker-products.png)

## 도커 동작구조
- Linux namespace
	- 도커 컴포넌트는 리눅스 커널의 namespace 기능을 사용하여 독립적으로 실행
	- PID namespace : 프로세스 ID, 서로 다른 namespace의 프로세스는 서로 액세스 못함
	- Network namespace : IP, 포트, 라우팅 테이블 등을 namespace 별로 할당. 호스트에서 사용중인 포트도 컨테이너 안에서는 사용 가능
	- UID namespace : UID(User ID), GID(Group ID)를 namespace 안에서 독립적으로 사용
	- Mount namespace : 기기와 저장장치를 namespace 내의 파일 시스템 트리에 구성
	- UTS namespace : namespace 별로 독립적인 호스트명/도메인명 사용 가능
	- IPC namespace : 프로세스간 통신 오브젝트를 namespace별로 가질 수 있음
- Linux cgroup
	- 도커 컨테이너들은 물리머신의 리소스를 공유한다. 이때 cgroup을 사용하여 리소스를 할당한다.
	- cpu(cpu 사용량 제한), memory(메모리와 스와프 사용량 제한), devices(디바이스 액세스 제어)
	- cgroup은 계층구조를 사용, 하위 cgroup은 상위 cgroup 설정된 제한을 넘을 수 없음
- 네트워크
	- 각 컨테이너 마다 가상 NIC(vethxxx) 생기고, 172.17.0.0/16 subnet mask 인 private IP 할당됨.
	- 가상 bridge(docker0, 172.17.42.1) 생성됨 : 컨테이너의 가상 NIC가 연결됨, 물리NIC(eth0)와 통신, 컨테이너간 통신

# 도커

## 도커 명령어
- docker <명령어>
- 명령어 : start, stop, restart, pause, unpause, wait, kill, attach, exec ...
	- 명령어 참조 : [도커 치트 시트](https://gist.github.com/nacyot/8366310)

## **자주 사용하는 명령어**
- docker start my-container ▶ 컨테이너 실행
- docker exec -it my-container /bin/bash ▶ 컨테이너의 shell 실행
- docker logs -ft my-container ▶ 컨테이너의 로그 실행	

## 도커 정보
- docker version ▶ 도커 버전, GO 버전, OS, 아키텍처 등 확인
- docker info ▶ 구동중인 컨테이어 수, 스토리지 드라이버 종류, Boot2Docker 버전 등 확인

## 매번 sudo 입력하여 실행 방지
- sudo usermod -aG docker ${USER} ▶ ${USER}는 현재 사용자를 의미
- sudo service docker restart

# 이미지

## 이미지 찾기
- 이미지는 Docker 컨테이너의 템플릿이다.
- 이미지 공유 : [Docker Hub](https://hub.docker.com/)
- 공식 이미지가 아니면 "사용자id/이미지" 형태임
- tags : 이미지의 변경시, 특정 시점에 tag를 달아서 기록, 특정 태그를 지정하지 않으면 latest(마지막)
- docker search <키워드> ▶ Docker Hub에서 이미지를 검색, STARS가 많을 수록 인기
- docker search --stars=30 centos ▶ Docker Hub에서 별30개 이상인 centos 이미지 검색

## 이미지 가져오기
- docker search redis ▶ redis 이미지 찾기
- docker pull redis:latest  ▶ redis 이미지 최신것 가져오기
- docker images   ▶ 로컬에 설치된 이미지 검색
- docker rmi <이미지이름> ▶ 로컬에서 이미지 삭제

## Docker Hub
- [Docker Hub](https://hub.docker.com/)
- 이미지를 업로드/다운로드 가능
- docker login ▶ 도커 허브에 로그인
- docker logout ▶ 도커 허브에서 로그아웃
- docker push kimstar/my-image ▶ 이미지를 올림
- docker push kimstar/my-image:1.0 ▶ 태그 지정하여 이미지를 올림
- docker pull kimstar/my-image ▶ 이미지를 받음
- docker push ubuntu ▶ 공식이미지에 올릴 권한이 없어서 실패함

# 컨테이너

## 이미지에서 컨테이너 생성
- docker run -i -t --name <컨테이너명> -d <이미지명>  ▶ 이미지에서 컨테이너 생성하여 실행
	- --name : 컨테이너 이름
	- -i : interactive (표준입출력 사용)
	- -t : tty (http://www.myservlab.com/144 참고)
	- -d : 이미지를 백그라운드로 실행 (detach 모드)
	- docker create 로 생성만 할 수도 있음
- docker ps ▶ 현재 실행중인 컨테이너 목록
- docker ps -a ▶ 정지된 컨테이너 포함하여 모든 컨테이너 목록

## 컨테이너 실행/중지
- docker start <컨테이너명> ▶ 컨테이너 실행
- docker stop <컨테이너명> ▶ 컨테이너 중지
- docker restart <컨테이너명> ▶ 컨테이너 재실행
- docker exec -it <컨테이너명> /bin/bash   ▶ 컨테이너의 bash를 실행

## 컨테이너 삭제
- docker rm <컨테이너명>  ▶ 태그를 입력하지 않으면 모든 태그가 삭제됨
- docker rm <컨테이너명>:<태그> ▶ 특정태그만 삭제

## 컨테이너 로그
- doker logs -ft <컨테이너명>  ▶ tail 처럼 로그 확인
	- f : follow log output
	- t : show timestamp

## 컨테이너 네트워크 설정	
- docker run -d -p 8080:80 <컨테이너명>
	- -p <호스트포트>:<컨테이너포트> : 호스트 8080포트를 컨테이너의 80으로 매핑
- docker run --dns=192.168.1.1 <컨테이너명>
	- DNS를 설정
- docker run -it --add-host=test.com:192.168.1.1 <컨테이너명>
	- --add-host=호스트명:IP
	- /etc/hosts 파일에 "192.168.1.1 test.com" 추가됨

## 컨테이너 리소스 설정
- docker run -ti --c 512 ubuntu:16.04 /bin/bash
	- --c : --cpu-share, 기본값은 1024, 여러 컨테이너가 있으면 상대적으로 cpu를 사용
- docker run -ti --m 300M ubuntu:16.04 /bin/bash
	- --m : --memory, 메모리 제한값, 최소 4M, 기본값은 memory 300M / swap 300M
- docker run -ti --m 300M --memory-swap 1G ubuntu:16.04 /bin/bash
	- 메모리+swap 제한값, 위의 경우 memory 300M / swap 700M

## 컨테이너 디렉토리 공유
- docker run -v /c/Users/kimstar/WebPage:/var/www/html httpd
	- 호스트 디렉토리(C:\Users\kimstar\WebPage)를 컨테이너 디렉토리(/var/www/html)와 공유

# 관리

## 이미지 관리 (load/save)
- docker load < my_image.tar.gz   ▶ 이미지 로드
- docker save my_image:my_tag > my_image.tar.gz  ▶ 이미지 저장

## 컨테이너 관리 (import/export)
- cat my_container.tar.gz | docker import - my_image:mytag  ▶ 컨테이너 로드
- docker export my_container > my_container.tar.gz  ▶ 컨테이너 저장
- docker rename <old컨테이너명> <new컨테이너명> ▶ 컨테이너 이름 변경

## 컨테이너/이미지 정리
- docker kill $(docker ps -q)   ▶ 실행중인 모든 컨테이너를 죽임
- docker rm $(docker ps -q)     ▶ 모든 컨테이너를 삭제
- docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm  ▶ 오래된 컨테이너 죽임
- docker rm -v $(docker ps -a -q -f status=exited)   ▶ 정지된 컨테이너 삭제
- docker rmi $(docker images -q -f dangling=true)   ▶ 불필요 이미지 제거
- docker volume rm $(docker volume ls -q dangling=true)   ▶ 불필요 볼륨 제거

## 컨테이너 모니터링
- docker stats <컨테이너명>  ▶ 한개 컨테이너 상태
- docker stats <컨테이너명1> <컨테이너명2>  ▶ 2개 컨테이너 상태
- docker stats $(docker ps -q)  ▶ 모든 컨테이너 상태
- docker stats $(docker ps --format '{{.Names}}')  ▶ 이름순으로 모든 컨테이너 상태
- docker ps -a -f ancestor=ubuntu   ▶ ubuntu 이미지에서 만들어진 모든 컨테이너 찾기 (-f : 필터링)
- docker ps -a -f exited=0 ▶ 종료코드 0인 모든 컨테이너 찾기
- docker top <컨테이너명> ▶ 컨테이너에서 실행중인 프로세스 확인
- docker port <컨테이너명> ▶ 컨테이너에서 사용하는 포트 확인

## 컨테이너 파일 복사
- docker cp test:/etc/password /tmp/etc ▶ test컨테이너 파일을 호스트로 복사 (/etc/password를 /tmp/etc로 복사)
- docker cp /tmp/etc test:/etc/password ▶ 호스트 파일을 test컨테이너로 복사 (/tmp/etc를 /etc/password로 복사)

## 컨테이너 파일 변경 확인
- docker diff test ▶ test컨테이너의 변경된 파일 확인, A(파일추가), D(파일삭제), C(파일변경)

# 이미지 빌드

## 이미지 빌드하는 2가지 방법
- docker commit ▶ 생성과정 추적안됨. 실수하기 쉬움.
- Dockerfile을 사용하여 docker build ▶ 권장

## 1) commit 으로 이미지 빌드 실습
- docker pull ubuntu  ▶ 우분투 이미지 다운로드
- docker run -it ubuntu /bin/bash   ▶ 우분투 이미지를 실행하고 쉘실행
	- apt-get -yqq update  ▶ 목록 업데이트
	- apt-get -y install apache2  ▶ apahce2 설치
	- exit  ▶ 쉘 빠져나감	
- docker ps -a  ▶ 설치한 컨테이너 확인
- docker commit 컨테이너아이디 kimstar/apache2 ▶ 커밋하여 이미지 생성
- docker images kimstar/apache2 ▶ 이미지 생성 확인
- docker commit -m="new apache2 image" --author="kimstar" 컨테이너아이디 kimstar/apache2:webserver ▶ 태그 달아서 커밋
	- -m : 메시지
	- -a, --author="" : 작성자
- docker inspect kimstar/apache2 ▶ 이미지 세부 정보확인
- docker inspect kimstar/apache2 --format="{{ .Os}}" ▶ 이미지 세부 정보중 OS 확인

## 2-1) Dockerfile 작성후 docker build로 이미지 만들기
- **Dockerfile**
	- build시 설계도와 같음. git등 사용시 유용.
	- 첫줄부터 차례대로 실행.
	- 에러 발생 후 재실행시 에러지점부터 다시 시작 (캐쉬됨)
	- 이미지 빌드시 자동으로 캐시를 생성하고, 다른 이미지 빌드시 이를 사용한다.
	- 캐쉬 무시하려면 : docker build --no-cache 
	- 명령어는 대소문자 구분없으나 보통 대문자 사용
	- "#" 으로 주석처리	
- Dockerfile 구성
	- 기반이 되는 이미지 (base image)
	- 컨테이너 안에서 실행되는 명령어
	- 환경변수 등의 설정
	- 컨테이너 안에서 실행되는 데몬 실행
- .dockignore
	- 무시할 파일들 정의 (불필요파일, 보안)
	- context의 root(docker build를 실행하는 위치)에 위치 
	- "#" 으로 주석처리
- .dockignore 패턴	
	- \*/temp\* ▶ 서브디렉토리에서 temp로 시작하는 파일 및 디렉토리 제외
	- \*/\*/temp\* ▶ 2레벨 이하의 서브디렉토리에서 temp로 시작하는 파일 및 디렉토리 제외
	- \*\*/\*.java ▶ 깊이 상관없이 모든 java 파일 제외
	- temp? ▶ tempa, tempb 와 같은 파일 및 디렉토리

## 2-2) Dockerizing 실습 (SSH데몬용 컨테이너 만들기)
- mkdir docker_build
- cd docker_build
- vi Dockerfile ▶ 내용은 아래 참조
- docker build -t "kimstar/sshd:1.0" . ▶ Dockerfile로 이미지 빌드 수행. -t는 태그.
- docker images ▶ 생성한 이미지 확인가능
- docker history kimstar/sshd ▶ Dockerizing과정이 시간순으로 보임 (Dockerfile 역순)
- docker run -d -P --name test_sshd kimstar/sshd ▶ 컨테이너 생성
- docker port test_sshd 22 ▶ 22포트 외부 노출, 0.0.0.0:32768 이라고 출력되면 내부에서는 32768 포트를 사용함을 의미
- docker inspect --format '{{ .NetworkSettings.IPAddress }}' test_sshd ▶ 컨테이너의 IP주소를 확인, 접속시 여기서 확인한 것을 사용
- ssh root@localhost -p 32768 ▶ ssh 접속 (32768 포트)
- docker run -d -p 22:22 -- name test_sshd2 kimstar/sshd ▶ 호스트포트(외부):내부컨테이너 포트(내부)
- ssh root@localhost ▶ ssh 접속 (22 포트)
- docker stop test_sshd test_sshd2 ▶ 컨테이너 중지
- docker rm test_sshd test_sshd2 ▶ 컨테이너 삭제

- [Dockerfile 예제](https://docs.docker.com/engine/examples/running_ssh_service/)
```
FROM ubuntu:16.04 ▶ 사용할 이미지와 태그
MAINTAINER kimstar <abcedfg@gmail.com> ▶ 이미지 만든 사람 정보, 필수 아니지만 보통 입력

RUN apt-get update && apt-get install -y openssh-server  ▶ apt-get 실행
RUN mkdir /var/run/sshd  ▶ 디렉토리 생성
RUN echo 'root:newpassword' | chpasswd ▶ root 새로운 암호 설정
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile" ▶ 환경변수 설정
RUN echo "export VISIBLE=now" >> /etc/profile ▶ 프로파일에 추가

EXPOSE 22 ▶ SSH 포트(22) 노출
CMD ["/usr/sbin/sshd", "-D"] ▶ 이미지 빌드후 컨테이너로 만들때 실행할 내용, ssh를 데몬으로 띄워라.
```

# Dockerfile 명령어
## Dockerfile 명령어
| 명령어     | 설명			 |
|------------|---------------------------|
| FROM       | 베이스 이미지 지정	 |
| MAINTAINER | Dockfile 생성자		 |
| RUN        | 명령어 실행		 |
| CMD        | 데몬 실행 		 |
| LABEL      | 라벨 설정		 |
| EXPOSE     | 포트 export		 |
| ENV        | 환경변수 설정		 |
| ADD        | 파일/디렉토리 추가	 |
| COPY       | 파일 복사		 |
| VOLUME     | 볼륨 마운트		 |
| ENTRYPOINT | 데몬실행			 |
| USER       | 사용자 설정		 |
| WORKDIR    | 작업디렉토리 설정	 |
| ONBUILD    | build 완료후 실행할 명령어|

## ENV 
- 환경변수 key/value 설정
- ${A:-B} : A가 있으면 A, 없으면 B
- ${A:+B} : A가 있으면 B, 없으면 empty
```
FROM ubuntu:16.04
ENV foo /bar
WORKDIR ${foo}
ADD .$foo
```
- ENV 실행시마다 Docker 이미지 생기므로 줄이는것이 좋다.
```
ENV myName="kim star" \
    myNickName=kimstar
```

## RUN (이미지 생성시 실행)
- 문법
	- RUN ["명령어", "param1", "param2"] ▶ Exec형태
	- RUN 명령어 param1 param2 ▶ shell 형태 (/bin/sh 을 거쳐 실행됨)
- 텍스트 파일 생성 팁 : RUN echo "hello" > /aaa/bbb/text.txt						
- RUN 실행시 이미지 새로 생김
	- 이미지는 기존 이미지 레이어에 쌓임
	- Boot2Docker에서의 AUFS는 127개까지 관리할 수 있음
	- 명령어를 줄이는 것이 좋음
	```
	# 3번 실행
	RUN yum -y install httpd
	RUN yum -y install php
	RUN yum -y install php-mbstring

	#1번 실행
	RUN yum -y install httpd php php-mbstring

	#1번 실행
	RUN yum -y install\
	           httpd\
	           php\
	           php-mbstring
	```		
## CMD (컨테이너 구동시 실행)
- Docker 파일에는 하나의 CMD만 가능 (여러개 있으면 마지막것만 실행)
- 문법
	- CMD ["명령어", "param1", "param2"] ▶ Exec형태
	- CMD 명령어 param1 param2 ▶ shell 형태
	- CMD ["param1", "param2"] ▶ ENTRYPOINT 사용
- CMD vs RUN
	- RUN은 build때 각 명령을 실행하고 commit
	- CMD는 build때 실행안되며, 컨테이너를 run할때 실행됨	
	```
	FROM centos:latest

	# httpd 설치 - 이미지 생성시 실행
	RUN ["yum", "install", "-y", "httpd"]

	# httpd 실행 - 컨테이너 구동시 실행
	CMD ["/usr/sbin/httpd", "-D", "FORGROUND"]
	```

## ENTRYPOINT
- Docker 파일에는 하나의 ENTRYPOINT만 가능 (여러개 있으면 마지막것만 실행)
- 문법
	- ENTRYPOINT ["명령어", "param1", "param2"] ▶ Exec형태
	- ENTRYPOINT 명령어 param1 param2 ▶ shell 형태
- 오버라이드
	- build 시점(docker run 실행시)에 Dockerfile의 CMD 설정은 오버라이드 가능하다
	- 실행시 옵션을 바꿔서 run 할때 유용
	- 구축담당자가 임의의 데몬 실행으로 변경하지 못하게 하려면 CMD 대신 ENTRYPOINT 사용할것
	```
	FROM centos:latest
	ENTRYPOINT ["top"]
	CMD ["-d","10"]

	# docker run <이미지>      : top -d 10
	# docker run <이미지> -d 2 : top -d 2
	```

## ONBUILD
- build 후 생성된 이미지로를 base image로 다른 이미지 build시 실행할 명령어
- 관리자가 base image 배포하고 별도의 파일을 추가할때 유용
	1. 관리자는 base image 생성을 위한 Dockerfile에 "ONBUILD ADD website.tar /var/www/html" 추가하여 이미지 생성
	2. 개발자는 필요한 website.tar 생성
	3. 개발자는 관리자가 생성한 base image를 사용한 Dockerfile 파일로 자신의 이미지 생성한다. build시 website.tar 파일을 통해 /var/www/html 에 파일이 추가된다.

## WORKDIR
- 명령을 실행할 디렉토리
	- RUN, CMD, ENTRYPOINT, COPY, ADD
- 상대/절대경로 가능
- 경로가 없으면 생성됨

## USER
- 명령을 실행할 유저를 지정
	- RUN, CMD, ENTRYPOINT
- 미지정시 root

## VOLUME
- VOLUME으로 지정한 디렉토리는 다른 컨테이너에서 마운트할 수 있는 볼륨이 된다.

## ADD
- 호스트의 파일/디렉토리를 이미지 안에 추가
- 문법
	- ADD <호스트파일경로> <이미지파일경로>
	- ADD ["호스트파일경로", "이미지파일경로"]
- ADD hom* /mydir/
- ADD hom?.txt /mydir/
- ADD http://example.com/foo.zip /mydir/bar.zip 
	- 가져온 파일의 권한은 600
	- 압축이 풀리지는 않는다.
	- 인증(로그인 등)이 필요하면 불가
	- 인증 필요시 RUN 명령에서 wget 또는 curl 등으로 인증하여 사용
- ADD foo.tar.gz /mydir/  ▶ 압축을 풀어서 넣음
- ADD ../test /mydir/  ▶ docker build를 실행하는 디렉토리 외부에서 복사 불가

## COPY
- ADD와 유사, 압축/url 불가
- COPY hom* /mydir/
- COPY hom?.txt /mydir/

## LABEL
- 이미지 정보를 포함시킬때
- key=value 형식
- docker inspect --format="{{ .Config.Labels}} <이미지명> ▶ 생성한 이미지의 정보 확인시 노출

## EXPOSE
- 실행중인 컨테이너가 listen할 포트를 정의
- 다른 컨테이너와의 link에 사용

# Docker Compose
![Docker Compose](http://kimstar.kr/blog/wp-content/uploads/2017/05/docker-compose-2.png)

## Docker Compose
- 개요
	- 컨테이너가 많을때, 동일한 호스트에서 여러 컨테이너를 한번에 올리거나 내릴 수 있음.
	- CI 환경에서 활용
	- [Docker Compose 명령어](https://docs.docker.com/compose/reference/)  
	- [Docker Compose 배포](https://github.com/docker/compose/releases/)  
	- windows용 docker toolbox에는 포함되어 있지 않음
	- docker-compose --version ▶ 버전 확인
	- docker-compose.yml 파일에 정의	
- 명령어
	```
	serverA:
	   image: httpd

	serverB:
	   image: mysql
	```      
	- docker-compose up -d ▶ 백그라운드 실행
	- docker-compose down ▶ 모두 중지
	- docker-compose restart ▶ 모두 재실행
	- docker-compose restart serverA ▶ serverA 컨테이너만 재실행	
	- docker-compose rm ▶ 모든 컨테이너만 삭제
	- docker-compose scale serverA=10 serverB=10 ▶ 생성할 컴포넌트 갯수 지정
	- docker-compose ps ▶ 컨테이너 목록 확인
	- docker-compose logs ▶ 컨테이너 로그 확인
	- docker-compose run serverA /bin/bash ▶ serverA에서 명령어 실행

## docker-compose.yml	
- 파일 설정
	- 참고 : 
		- [설정 참고](https://docs.docker.com/compose/compose-file/)
		- [Docker Compose YAML Editor](https://lorry.io)
	- image : 사용할 이미지 설정
	- dockerfile : 사용할 dockerfile 설정
	- links : 다른 컨테이너와 링크. /etc/hosts 설정됨.
	- volumes : 마운트할 볼륨
	- ports : 사용할 포트
	- dns : 사용할 dns 주소
	- command : 컨테이너에서 실행할 명령어
	- environment : 환경변수 지정
	- env_file : 환경변수 저장한 파일	
- YAML
	- 들여쓰기(탭X,공백O)로 계층 표기
	- 배열앞에 "- "를 붙임
	- 주석은 #을 사용
	- [공식사이트](http://yaml.org)


## 워드프레스 실습
- [따라하기](https://docs.docker.com/compose/wordpress/)
- docker-compose.yml 예제
```
version: '2'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    db_data:
```

# 멀티호스트 환경
## 클러스터링
- 클러스터링 : 가용성을 위해 시스템이 정지하지 않도록 여러대의 서버와 하드웨어를 1대처럼 묶는 기술
- Docker 컴포넌트 비교
	- Dokcer Compose : 1대의 호스트에서 여러 컨테이너 관리
	- Docker Swarm   : 여러 호스트에서 여러 컨테이너를 클러스터링
	- Docker Machine : 여러 호스트에서 실행환경을 구축
- 아직은 마이너 버전이므로 운영에서의 사용은 고려 필요	
## Docker Machine
![Docker Machine](https://docs.docker.com/machine/img/provision-use-case.png)
- [Docker Machine](https://docs.docker.com/machine)
- [github](https://github.com/docker/machine)
- Docker 실행환경을 커맨드로 생성하여 관리
- docker-machine create --driver <드라이버명> <생성할도커머신명> ▶ 실행환경 생성
	- [드라이버명 목록](https://docs.docker.com/machine/drivers) : AWS, Azure, Hyper-V, OpenStack 등 
- docker-machine ls ▶ 관리하는 실행환경 목록
- docker-machine status <머신명> ▶ 머신의 상태 확인
- docker-machine ssh <머신명> ▶ SSH 접속
- docker-machine env --shell cmd <머신명> ▶ cmd(windows환경)를 실행하여 환경변수를 설정
- docker-machine start <머신명> ▶ start/stop/restart/kill
- docker-machine scp <머신명>:<파일경로> . ▶ 현재 디렉토리에 머신의 파일을 SCP(Secure Copy Protocol)로 다운로드
- docker-machine rm -f <머신명> ▶ 실행환경 삭제
- docker-machine ip <머신명> ▶ IP확인
- docker-machine inspect <머신명> ▶ 실행환경의 상세정보 확인(CPU, Memory...)

## Docker Swarm
![Docker Swarm](https://blog.docker.com/media/2015/11/image00.png)
- [Docker Swarm](https://docs.docker.com/swarm/)
- 클러스터 환경을 구축
- docker run swarm create ▶ 클러스터 구축을 위한 토큰 발행
- docker-machine create -d vitualbox --swarm --swarm-master --swarm-discoverty token://<토큰> master ▶마스터 노드 구축
- docker-machine create -d vitualbox --swarm --swarm-discoverty token://<토큰> node01 ▶ node01 구축
- docker-machine create -d vitualbox --swarm --swarm-discoverty token://<토큰> node02 ▶ node02 구축
- docker-machine ls ▶ 클러스터링 노드 확인
- eval "$(docker-machine env --shell bash --swarm master)" ▶ master 노드의 쉘 접속
- docker info ▶ 노드 정보 확인

# AWS에서 Docker 사용
- [Docker for AWS Setup & Prerequisites](https://docs.docker.com/docker-for-aws/)
- AWS에서의 Docker 지원
	- Amazon EC2 : EC2 가상서버에 Docker, Docker Machine, Docker Swarm 설치 가능 ▶ 세세한 설정시 사용
	- Amazon ECS : Amazon EC2 Continer Service, EC2를 사용하여 Docker 클러스터링을 위한 관리형 서비스 ▶ 추천
	- Elastic Beanstalk : PasS, Docker를 사용하여 애플리케이션 개발~운영까지 지원 ▶ 단기간 애플리케이션 개발에 유리
