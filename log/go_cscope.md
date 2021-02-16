---
title: "cscope 사용(Go)"
slug: 
description: null
date: 2018-08-21T16:34:04+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
- cscope
series:
-
---



Go언어 개발 환경(추가)
---
Go언어로 작성한 코드를 분석하기 위해서 cscope가 필요할 때가 있다.  
vim에서 cscope를 사용하기 위한 과정을 정리해 보려고 한다.

cscope를 설치한다.
---

```
$ sudo apt-get install cscope
```

mkscope.sh 파일을 만든다.  
---
mkscope.sh의 내용은 다음과 같다.  
이 스크립트는 https://www.cnblogs.com/shaohef/p/7358111.html 의 내용을 수정해서 사용했다.

```
#!/bin/bash

# 기존에 존재하던 files와 out 파일을 제거한다
rm cscope.files cscope.out

# $GOROOT가 존재하지 않으면 설정한다.
if ["$GOROOT" = ""]; then
    echo "GOROOT is not set"
    GOROOT=`go env | grep "GOROOT" | cut -d "=" -f2`
    GOROOT=${GOROOT#\"}
    GOROOT=${GOROOT%\"}
fi
echo $GOROOT

# Go 표준 패키지
go_src=$GOROOT/src

# Go언어 표준 패키지로 제공되는 go 파일의 경로를 cscope.files로 만든다.
find $go_src -name "*.go" -print > cscope.files
# 현재 디렉토리 존재하는 go 파일의 경로를 cscope.files로 만든다.
find . -name "*.go" -print > cscope.files


# cscope 옵션으로 -b 옵션을 사용해서 데이터 베이스 생성 후에 심볼 찾기 화면이 안보이게 하고, 
# -k 옵션을 사용해서 default include(/usr/include) 경로의 내용을 사용하지 않도록 한다.
if cscope -b -k; then
    echo "Done"
else
    echo "Failed"
    exit 1
fi

```

mkscope.sh 의 사용권한을 변경한다.
---

```
$ sudo chmod 755 mkscope.sh
```

mkscope.sh를 사용하기 편리하게 경로를 이동한다.(/usr/local/bin)
---

```
$ sudo mv mkscope.sh /usr/local/bin
```

cscope를 편리하게 사용하기 위해서 quickr-cscope.vim을 설치
---
vimrc.local에 vundle을 추가

```
Plugin 'ronakg/quickr-cscope.vim'
```

quickr-cscope의 단축키는 다음과 같다.

```
<leader>g : 커서 아래의 함수가 선언되어 있는 곳으로 이동한다.
<leader>s: 커서 아래의 단어를 검색을 한다.
<leader>c: 커서 아래의 함수를 호출하는 함수들을 찾는다.
<leader>f: 커서 아래의 단어로된 파일을 검색한다.
<leader>i: 커서 아래의 함수 또는 키워드의 정의 내용을 출력한다.
<leader>t: 커서 아래의 단어로된 문자열을 검색한다.
<leader>d: 커서 아래의 함수가 호출하는 함수들을 출력한다.
<leader>e: egrep를 사용한 검색을 한다.
```

그리고 다음과 같이 직접 사용할 수 도 있다.

```
:cs find {g|s|c|f|i|t|d|e} keyword 
```

분석하기
---
vi를 시작하기 전에 항상 mkscope.sh를 실행해서 수정된 내용이 cscope에서 사용하는 데이터베이스인 cscope.out에 반영될 수 있도록 한다.
