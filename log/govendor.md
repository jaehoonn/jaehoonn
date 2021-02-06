---
title: "vendor 관리"
slug: 
description: null
date: 2016-10-14T18:16:35+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
series:
-
---

vendoring되어 있는 패키지를 관리해야 할 이유가 무엇인지에 대해서 찾아보다가 좋은 글을 발견했다.  
https://gocodecloud.com/blog/2016/03/29/go-vendoring-beginner-tutorial/  

이 글을 읽고 이해한 정도만을 요약  

---

vendoring을 왜 하나?
---

Project A에서 사용하는 Pkg X의 리비전 1을 사용하고 있다. 그런데 Project B에서는 Pkg X의 리비전 2를 사용하려 한다.  
그런데 $GOPATH 안에는 같은 패키지를 하나만 갖고 있을 수 있다.  
Pkg X의 리비전을 Project A에 맞추면 Project B가 빌드를 실패하고, Project B에 맞추면 Project A가 빌드에 실패하게 된다.  
그래서 패키지들을 vendor 아래에 위치 시킨다.  

프로젝트를 진행하면서 vendoring 되어 있는 패키지들의 리비전을 공유, 관리하기 위해서 godep, govendor 등과 같은 걸 사용할 수 있다.  
다른 도구들은 무시하고 여기에서는 govendor의 사용법만 알아본다.  
govendor를 사용하면, $GOPATH에는 무조건 최신 리비전의 패키지를 받아 놓고, 프로젝트에서 필요한 리비전의 패키지만을 vendor에 복사해 놓고 사용할 수 있다.  

govendor 사용  
---

govendor를 설치한다.

```
$ go get -u github.com/kardianos/govendor
```

프로젝트 폴더로 이동한다.
```
$ cd yourproject
```

govendor를 사용하기 위한 초기화를 한다.
```
$ govendor init
```

이제 vendor 폴더가 생기고 그 안에 vednor.json이 생기게 된다.  

사용할 패키지들을 github 등에서 clone 하거나 go get을 한다.  
$GOPATH/src 아래에 받아져 있게 된다.  

이제 yourproject 폴더안에서 "govendor add +external" 을 한다.  
+external 은 src 아래 받았던 패키지이다.  
govendor add 를 하면 yourproject의 vendor 폴더로 옮겨지게 된다.  
그리고 vendor.json에 현재 add 한 패키지의 리비전등의 정보를 기록한다.  

이 후에 필요에 의해서 패키지를 업데이하기 위해서는  
```
$ govendor update
```

를 하면 최신의 패키지로 갱신된다.  

그리고 현재 사용중이었던 패키지를 다시 받아오고 싶으면,  
```
$ govendor sync 
```
를 사용하면 vendor.json에 기록되어 있는 패키지를 다시 받아온다.
