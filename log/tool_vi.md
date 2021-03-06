---
title: "vi 팁"
date: 2016-09-05T01:28:09+09:00
---

현재 작업중인 파일의 경로와 이름 확인
---

현재 작업중인 파일의 경로 또는 이름이 궁금할 경우가 있다.  

```
ctrl + g  
```

또는  

```
: f
```

80컬럼에 버티컬 라인
---

vi에서 80컬럼의 버티컬 라인을 생성하려면 다음과 같이 한다.

```
set colorcolumn=80
```

을 적용하면, 빨간색 라인이 생긴다.  

파일 관리
---

NerdTree를 사용하지 않고 vi에서 파일과 디렉토리를 관리하는 방법 

파일 네비게이션  

1. 표준모드(ex: i, o, a 같은걸 입력해서 입력모드로 전환되기 전 상태)  
2. "e." (설명: "." 는 현재의 디렉토리)  

디렉토리 만들기  

1. 명령라인 모드 (ex: ":wq" 해서 현재 작업중인 내용을 저장하고 종료 하기 위한 명령을 입력하기 위한 모드)  
2. ":!mkidir foo" (ex: "foo" 라는 디렉토리 생성)  

또는 ":!mkdir -p foo/bar" (ex: foo의 하위 디렉토리인 bar까지 생성)


buffer이동  
---

vi를 사용중에 열려있는 buffer를 이동해야 할때가 있다.  

다음과 같이 하면 열려있는 1번 bufffer로 이동한다.  

```
:buffer 1  
```

열려 있는 buffer전체를 보려면,  

```
:buffers  
```

와 같이 한다.  

화면 분리  
---

분리는 가로(vsplit), 세로(split)으로 나눌 수 있다.

```
:[vsp]lit [파일명]  
:[sp]lit [파일명]  
```

화면간의 이동은  

> ctrl + w [h j k l]

현재 화면 닫기는  

> :q

화면을 모두 닫기 위해서는  

> :qa

현재 커서가 있는 화면을 제외하고 모두 닫기  

> ctrl + w, o

vimgrep
---

현재 디렉토리내에서 ".go" 파일들중에서 문자("TEXT")에 대한  검색이 필요할 경우

```
:vimgrep /TEXT/ ./*.go
```

여러개의 결과들을 이동

```
:cfirst
:cnext
:cprev
:clast
```

검색된 결과를 리스트로 보기

```
:cw
```
