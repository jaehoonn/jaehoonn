---
title: "docker image 저장소 위치 변경"
date: 2020-03-05T12:05:00+09:00
---


* 새로운 디스크를 추가한다.  
  * 여기에서는 "/docker" 에 mount 되어 있는걸로 가정한다.  
* "etc/docker/daemon.json" 에 다음의 내용을 추가한다.  

```shell
{
  "data-root": "/docker"
}
```

* docker 서비스를 재시작한다.  


```shell
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

