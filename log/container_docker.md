---
title: "docker 시작하기"
date: 2017-07-27T00:25:51+09:00
---




docker ce 설치
---

* 오래된 버전 제거
```Shell
$ sudo apt-get remove docker docker-engine docker.io
```

* 패키지 인덱스 업데이트
```Shell
$ sudo apt-get update
```

* apt가 https를 통해서 저장소를 사용할 수 있도록 패키지를 설치
``` Shell
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
* docker의 공식 GPG 키를 추가
``` Shell
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
* fingerprint를 확인
``` Shell
$ sudo apt-key fingerprint 0EBFCD88
```
* 안정화 버전의 저장소를 추가
``` Shell
$ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
```
* apt 패키지 인덱스 업데이트
``` Shell
$ sudo apt-get update
```
* 최근 버전의 docker ce를 설치
``` Shell
$ sudo apt-get install docker-ce
```
* 특정 버전의 docker ce를 설치하기 위해서 사용 가능한 버전을 나열 
```Shell
$ apt-cache madison docker-ce
```
* docker-ce의 특정 버전 설치(<VERSION>은 위에서 나열된 리스트 중에서 선택(2번째 컬럼)
```Shell
$ sudo apt-get install docker-ce=<VERSION>
```
