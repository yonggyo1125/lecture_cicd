# AWS EC2 프리티어 신청하기 (모든 서버 공통)
- 인스턴스 생성: ubuntu 서버로 선택
- 하드 공간은 30G 최대로 늘리기
- 탄력적 IP : 고정 공인 IP 발급하고 실행중인 인스턴스에 연결하기

> AWS EC2 서버 인스턴스는 유동 IP로 IP가 바뀔 수 있습니다. 변경되지 않도록 고정 IP 설정

# AWS 스왑 공간 늘리기 (모든 서버 공통)
> 젠킨스 서버나 스프링부트 서버를 구축하는 경우 AWS EC2 프리티어 사양에서 제공하는 램1G, 스왑공간 0MB 설정은 서버의 다운 현상을 발생시켜 정상적인 서비스가 어렵게 만듭니다. 
> 스왑메모리를 다음 명령어를 통해 충분히 늘려줍니다(스왑공간 128MB * 32 = 4GB), 최소 32MB 유지).

```
sudo dd if=/dev/zero of=/swapfile bs=128M count=32
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo swapon -s
sudo vi /etc/fstab  (파일열고)
/swapfile swap swap defaults 0 0  (를 끝 줄에 추가)
```

# 도커 설치 (모든 서버 공통)

- 우분투 시스템 패키지 업데이트

```
sudo apt-get update
```

- 필요한 패키지 설치

```
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

- Docker의 공식 GPG키를 추가

```agsl
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

- Docker의 공식 apt 저장소를 추가

```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

- 시스템 패키지 업데이트

```
sudo apt-get update
```

- Docker 설치

```
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

- Docker가 설치 확인

```
sudo systemctl status docker
```

# 젠킨스 서버 설치

> 도커 로그인 : 로그인을 해야 docker hub 사용 가능 합니다.

- [가입하기](https://www.docker.com/)


> 도커를 이용한 젠킨스 서버 설치시 JDK 버전에 신경써야 합니다. 설치시에 JDK 버전을 태그로 붙여 주면 버전에 맞게 생성된 도커 이미지를 다운 받을 수 있습니다(버전이 있는 경우).

```
docker pull jenkins/jenkins:jdk17
```

> 젠킨스 도커 이미지 다운이 안되는 경우 docker search jenkins를 통해 검색 후 설치


```
docker run --name jenkins -d -p 8080:8080 jenkins/jenkins:jdk17
```

- --name : 컨테이너 이름
- -d : 백그라운드 실행
- -p : 연결 포트  / -p 서비스 포트:컨테이너 포트

## AWS 인바운드 보안 규칙 추가 

> 8080 TCP 포트 모든 IP에 허용

# 젠킨스 설정

## Unlock Jenkins

- 웹 브라우저를 실행해서 IP주소:8080에 접속
- 초기 패스워드는 터미널 프로그램을 통해 서버에 접속하여 아래 명령어를 실행하면 확인할 수 있습니다.

![image1](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image1.png)

```
cat /var/lib/jenkins/secrets/initialAdminPassword
```

- 초기 패스워드를 Administrator password 항목에 입력 후 \[Continue\] 버튼을 클릭합니다.

## Jenkins 플러그인 설치

![image2](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image2.png)

- <b>Install suggested plugins</b>: Jenkins 커뮤니티에서 가장 유용하다고 알려진 플러그인들을 설치합니다.

![image3](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image3.png)

## Admin 계정 생성

![iamge4](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image4.png)

> Jenkins Admin 계정을 생성합니다. Admin 계정의 패스워드는 기호(예: /, ., !, *…)와 더불어 소문자와 대문자의 영문/숫자 문자를 사용하는 것을 권장합니다.

- Jenkins Admin 계정의 아이디를 입력합니다.
- Jenkins Admin 계정의 암호를 입력합니다.
- Jenkins Admin 계정의 이름을 입력합니다.
- Jenkins Admin 계정의 Email Address을 입력합니다.
- \[Save and Finish\] 버튼을 클릭합니다.

## Instance Configuraion 설정

![image5](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image5.png)

> Jenkins Instance Configuraion을 설정합니다. Jenkins Resource에서 절대값으로 사용할 URL 값을 설정하는 단계입니다.

- Jenkins root URL을 입력합니다. (Default URL로 설정하는 것을 권고하고 있습니다.)
- \[Save and Finish\] 를 클릭합니다.

## 완료

![image6](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image6.png)

- Start using Jenkins 버튼을 클릭하여 Jenkins 초기화를 완료합니다.


## 추가 플러그인 설치 - sshagent

> Jenkins 관리 -\> Plusins -\> Aailable plugins -\> ssh 검색 -> shaagent 체크 -> install 버튼 클릭


# 데이터베이스 설치 - Oracle XE 18c

## 도커 오라클 XE 이미지 다운로드

```
docker pull gvenzl/oracle-xe:18
```

![image7](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image7.png)

> 혹시라도 설치가 안되는 경우 <code>docker search oracle-xe</code> 로 검색 후 가장 별점(STARS)가 많은 이미지를 다운 받습니다.

```
docker images
```

> 정상적으로 다운받은 경우 다음과 같이 이미지가 생성이 됩니다.

![image8](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image8.png)


## 컨테이너 생성 및 백그라운드 실행

```
docker run --name oracle-xe -d -p 1521:1521 -e ORACLE_PASSWORD=\<SYSTEM 비밀번호\> gvenzl/oracle-xe:18
```

> 정상적으로 실행되는 경우 <code>docker ps</code> 실행시 다음과 같이 실행 중인 컨테이너가 보이게 됩니다.

![image9](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image9.png)


## sqlplus 접속

> 다음 명령어를 통해서 컨테이너의 bash 쉘에 접근 합니다.

```
docker exec -it oracle-xe bash
```

- -i : 입력 가능
- -t : 터미널 사용 가능

```
sqlplus system/설정한 비밀번호
```

## AWS 인바운드 보안 규칙 설정

>  1521 TCP 포트 모든 IP 허용 설정  


# 스프링 부트 서버 설정 

> 아마존에서 인스턴스 생성시 다운받은 인증서 파일(pem 파일)은 젠킨스 서버에 연동할 때 필요하므로 잘 보관할 것

## OpenJDK17 설치

```
sudo apt-get install openjdk-17-jdk
```


## 프로젝트로 사용할 사용자 추가

> 최고 관리자 계정으로 변경

```
sudo -s
```

> 추가시에 비밀번호 설정

```
adduser project
```

![image10](https://raw.githubusercontent.com/yonggyo1125/lecture_cicd/main/images/image10.png)

> 로그인 되는지 확인

```
login project

비밀번호 입력
```

> project 사용자를 생성하면  /home/project 디렉토리 역시 생성되며 사용자 공간입니다. 이 공간에 스프링 부트가 배포될 것입니다.

