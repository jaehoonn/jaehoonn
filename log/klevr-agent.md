---
title: "klevr-agent 빌드 후 Docker Image 만들기"
date: 2021-02-24T03:08:00+09:00
---


```shell
$ git clone git@github.com:jaehoonn/klevr.git
$ cd klevr
$ chmod +x ./build.sh
$ ./build.sh
$ cd cmd/klebr-agent/Dockerfile
$ docker build -t jaehoon/klevr-agent:0.2.6 .
$ docker login -u jaehoon -p {password}
$ docker push jaehoon/klevr-agent:0.2.6
```
