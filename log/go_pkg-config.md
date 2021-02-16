---
title: "pkg-config 사용"
slug: 
description: null
date: 2015-03-06T01:28:09+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
- pkgconfig
series:
-
---

Go언어를 사용하다보면 C로 만들어진 라이브러리를 사용하기 위해서 cgo가 필요할 때 가 있다.
라이브러리를 링크하고 하는 몇 가지 절차를 거치게 되는데, 이때 pkg-config라는걸 사용하면 편리하다.

pkg-config를 사용하면 컴파일하는데 필요한 라이브러리와 헤더정보를 Go코드상에서 동일한 경로로 제공해줄 수 있게 된다.

pkg-config가 설치되어 있지 않을 경우에는 다음과 같이 설치를 해준다.

```Shell
$ sudo apt-get install pkg-config
```

이제 설치되어 있는 라이브러리 목록을 한번 보자.

```Shell
$ pkg-config --list-all
```
현재 설치된 라이브러리들의 정보가 나오게 된다.

그런데 내가 원하는 라이브러리의 정보가 나타나지 않는다.

라이브러리 정보의 관리는 /usr/lib/pkgconfig 경로에 있는 .pc 파일들을 통해서 되어진다.
직접 소스를 컴파일해서 사용하는 라이이브러리의 경우 /usr/local/lib/pkgconfig 에 있는 경우도 있다. 또는 Qt와 같은 경우에는 /home/{user}/Qt5.4.1/5.4/gcc_64/lib/pkgconfig 의 경로에 pc파일이 존재한다. 

이렇게 기본경로가 아닌곳에 존재하는 pc파일들을 관리하기 위해서 PKG_CONFIG_PATH라는 환경변수를 사용할 수 있다.

```Shell
$ export PKG_CONFIG_PATH=”$PKG_CONFIG_PATH:/home/{user}/Qt5.4.1/5.4/gcc_64/lib/pkgconfig”
```

와 같이 해준다. 구분은 ":" 로 해준다.  

[참고] http://ko.wikipedia.org/wiki/Pkg-config  
[참고] http://tjcplpllog.blogspot.kr/2014/08/pkg-config.html  
[참고] http://blog.daum.net/heyjun/15705389  
[참고] http://mwmw7.tistory.com/160  
[참고] http://ubuntuforums.org/showthread.php?t=751456  
[참고] http://manual-archive.blogspot.kr/2011/08/pkg-config-pc-pkgconfigpath.html  
[참고] http://ubuntuforums.org/showthread.php?t=1091717  
[참고] http://blog.naver.com/supsup5642/60161284657  
