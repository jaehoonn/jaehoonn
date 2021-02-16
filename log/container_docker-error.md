---
title: "docker 설치 후 에러"
description: ""
date: "2020-04-08T21:27:50+09:00"
thumbnail: ""
categories:
  - til
tags:
  - "docker"
---


docker 설치 후 "Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?" 가 발생  

```shell
$sudo systemctl status docker

$sudo systemctl start docker

$sudo systemctl enable docker
```
