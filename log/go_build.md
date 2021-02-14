---
title: "build"
slug: 
description: null
date: 2017-05-25T15:43:40+09:00
type: posts
draft: false 
categories:
- til
tags: 
- go
series:
-
---

소개
---
Go언어로 개발을 하다보면 debug모드 release모드 같이 별도의 빌드 환경을 구성하고 싶을때가 있다.  
그래서 build 옵션으로 특정한 환경별로 결과를 달리 할 수 있는 방법에 대해서 테스트한 내용을 정리해본다.

빌드
---
go 파일들을 빌드하기 위해서는 일반적으로 다음과 같이 한다.
```
$ go build
```

Go언어 문서중에 보면 https://golang.org/pkg/go/build/#hdr-Build_Constraints[Build Constraints] 에 관한 내용이 있다.  
이 내용은 build 할 때 조건을 줄 수 있다는 내용이고 방법은 다음과 같이 하면 된다고 한다.   

코드의 상단에 "// +build linux" 하고 한 칸을 띄우고 "package blah" 를 시작한다.  

빌드를 하기 위해서는 "go build -tags linux" 와 같이 하면 "// +build linux"가 명시되어 있는 파일들을 빌드한다.   
그리고 "// +build !linux" 라고 되어 있는 파일의 경우 tags 플래그로 "linux" 가 들어오면 빌드에서 제외된다.  

이렇게 build 조건을 주는 방법을 이용해서 특정한 테스트 또는 디버깅을 위한 코드를 별도로 작성하고 확인 할 수 있을것 같다.  

그래서 다음과 같은 예제를 작성해 봤다.  

기본 코드를 다음과 같이 작성을 했다.  

main.go 파일은 다음과 같다.

```go
package main

func main() {
    Render()
}
```

이제 Render() 함수가 존재하는 render.go 파일을 다음과 같이 작성해 본다.  
Render() 함수는 화면에 "release render"라는 내용을 보여주는 기능을 한다.  

render.go 파일은 다음과 같다.  

```go
package main

import "fmt"

func Render() {
    fmt.Println("release render")
}
```

이제 빌드하고 실행을 해보자.

```go
$ go build
$ ./build_constraint
release render
```

화면에 "release render"라는 내용이 보여지는걸 확인 할 수 있다.  

이제 Render() 함수의 내용을 debug 용도로 "debug render" 라고 보여주려고 한다.  

그래서 render_debug.go 파일을 다음과 같이 작성했다.  

```go
package main

import "fmt"

func Render() {
    fmt.Println("debug render")
}
```

이 상태로 그냥 build를 하게 되면 다음과 같은 메세지를 보게 된다. + 

```
$ go build
# build_constraint
./render_debug.go:5: Render redeclared in this block
	previous declaration at ./render.go:5
```

Render() 함수가 중복해서 선언되어 있으니 당연한 결과이다.  

이제 위의 코드에 build 옵션을 지정해 보자.  

```go
// +build debug

package main

import "fmt"

func Render() {
    fmt.Println("debug render")
}
```

코드 상단에 "// +build debug" 를 추가하고 반드시 한 칸을 띄워야 한다.  

이제 build를 실행하면 build가 성공적으로 된다.  
결과를 확인해 보자.

```
$ ./build_constraint
release render
```

화면에 보여지는건 여전히 "release render" 이다.  
build 옵션을 "debug"로 해서 build를 시도해 보자.

```
$ go build -tags debug
# build_constraint 
./render_debug.go:7: Render redeclared in this block
	previous declaration at ./render.go:5
```

Render() 함수가 중복되어 선언 되었다고 한다.  
render.go 파일과 render_debug.go 파일에 Render() 함수가 2번 선언된 것으로 확인이 되는것이다.  

그래서 render.go에도 "debug"라는 빌드 옵션이 지정될때는 build에서 제외가 되도록 "// +build !debug" 를 추가한다.  

```go
// +build !debug

package main

import "fmt"

func Render() {
    fmt.Println("release render")
}
```

이렇게 추가한 다음 build를 하고 내용을 확인해 본다.

```
$ go build -tags debug
$ ./build_constraint
debug render
```

디버그용 메세지인 "debug render"가 화면에 보여진다.  

일반적인 빌드를 시도해 본다.

```
$ go build
$ ./build_constraint
release render
```

이제 아무런 옵션 없이 build를 하고 확인을 해보면 "release render"가 화면에 보여지는걸 확인 할 수 있다.  

위 내용의 예제는 다음의 경로에서 볼 수 있다.  
https://github.com/jaehoonn/build_constraint
