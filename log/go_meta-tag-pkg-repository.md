---
title: "pkg 원격저장소 연결하기"
date: 2015-06-11T13:43:41+09:00
---

최근에 go소스들을 분석하면서 특이한 pkg import 경로들을 보게되었다.  

```go
import "rsc.io/pdf"
```

와 같은 "github.com/rsc/pdf" 와 동일한 내용인데 그리고 rsc는 russ cox가 사용하는 닉네임인데...그럼 같은 pkg인가?   
그래서 웹브라우저에서 rsc.io/pdf 를 입력하면 godoc로 이동을 하네?  
어떻게 한걸까!? 궁금했다. 그리고 왠지 멋있어 보였다...;  

그러다가 우연히 rob pike의 github page(https://github.com/robpike/robpike.github.io)를 보게 되었다.  
index.html 에 이렇게만 코드가 되어 있었다.  

```
<meta name="go-import" content="robpike.io/cmd hg https://code.google.com/p/rspace.cmd">
This is a git repository holding a redirect for the Go repositories referenced by robpike.io.
```

이게 뭐지? 그리고 roppike.github.io/cmd 안에는 여러 폴더들이 존재하고 그 안에는 index.html 파일 하나씩만 덩그러니 있었다.  
그 내용들은 다음과 같았다.  

```
<meta name="go-import" content="robpike.io/cmd hg https://code.google.com/p/rspace.cmd">
```

흠...meta tag를 사용하면 뭔가가 되나 보다 하면서 자료를 찾아봤다.  
단서는 의외로 간단하게 나왔다.  


```
https://golang.org/cmd/go/#hdr-Remote_import_paths  
```

에 보면 meta tag를 사용해서 원격저장소의 경로를 커스텀 도메인으로 사용할 수 있는 방법이 있었다.  
그래서 도메인을 하나 구입했다.  
io도메인(rsc.io, robpike.io 가 왠지 멋있어 보여서..;)을 따라하고 싶어...  
나도 .io 구매했었지만...너무 비싸서 연장을 못했다...;(지금은 jaehoonn.com 으로...)  

그리고 github page(jaehoonn.github.io)를 만들어 놓고, 거기에 내가 만든 pkg중에 하나를 골라서 한번 만들어봤다.  

```
jaehoonn.github.io/cubrid
```

에 index.html 을 다음과 같이 만들었다.  

```
<meta name="go-import" content="jaehoonn.com/cubrid git https://github.com/jaehoonn/cubrid">
```

이제 내 도메인(jaehoonn.com)과 연결을 했다.(github page를 커스컴 도메인에 연결하는 방법은 검색해보니 많이 나와서 쉽게했다.)  

그리고 커맨드 창에서...  

```
$ go get jaehoonn.com/cubrid  
```

명령을 내리고 잠시 기다리니 받아진다!!! (오류가 나온다면 cgo관련한 설정 때문인것 같다.) + 
 + 
그리고 godoc.org에도 등록도 잘된다. + 
cubrid로 검색을 해보면, jaehoonn.com/cubrid 와 github.com/jaehoonn/cubrid 두개가 나온다.  

russ cox는 웹브라우저에서 rsc.io/pdf 를 입력하면 godoc/rsc.io/pdf 로 redirect 가 되도록 해놓은것 같다.  
그래서 나도 meta tag를 하나 더 추가했다.  


```
<meta http-equiv="refresh" content="3;url=http://www.godoc.org/jaehoonn.com/cubrid">
```

이렇게 하니 godoc.org로 이동을 한다.  
음! 왠지 멋있어 보인다..;  

이제 열심히 코딩해서 pkg 많이 만들면 되는건가?;  

html 코드는 https://github.com/jaehoonn/jaehoonn.github.io/blob/master/ringbuffer/index.html 을 참고하면 된다.  

github page에 커스텀 도메인을 연결하면, https에 문제가 좀 있다.;

