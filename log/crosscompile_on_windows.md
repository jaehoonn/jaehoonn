---
title: "Powershell에서 할 수 있는 Linux를 위한 빌드"
description: ""
date: "2020-05-18T10:52:23+09:00"
thumbnail: ""
categories:
  - til
tags:
  - ""
---

Windows 환경에서 개발 후 Linux 환경으로 배포하기 위해서 Linux용 바이너리로 빌드가 필요할 때가 있다.   
다음의 방법으로 빌드 할 수 있다.  

Powershell에서 빌드하기 위한 방법은 아래와 같다.  

```shell
PS D:\ws\src\sample> $env:GOOS="linux"; go build
```

빌드 이후에는 필요에 따라서 "GOOS"를 다시 "windows"로 돌려 놓는다.
