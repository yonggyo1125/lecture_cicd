# AWS EC2 프리티어 신청하기 (모든 서버 공통)
- 인스턴스 생성: ubuntu 서버로 선택
- 하드 공간은 30G 최대로 늘리기
- 탄력적 IP : 고정 공인 IP 발급하고 실행중인 인스턴스에 연결하기

> AWS EC2 서버 인스턴스는 유동 IP로 IP가 바뀔 수 있습니다. 변경되지 않도록 고정 IP 설정

# AWS 스왑 공간 늘리기 (모든 서버 공통)
> 젠킨스 서버나 스프링부트 서버를 구축하는 경우 AWS EC2 프리티어 사양에서 제공하는 램1G, 스왑공간 16MB 설정은 서버의 다운 현상을 발생시켜 정상적인 서비스가 어렵게 만듭니다. 
> 스왑메모리를 다음 명령어를 통해 충분히 늘려줍니다.

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

