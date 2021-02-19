---
title: "패키지 만들때 testing 패키지 사용하기"  
date: 2013-08-23T14:12:58+09:00
---

개요
---

Go 언어용 패키지를 만들려면, 테스트코드를 해야 개발이 진행이 좀 더 원활하게 되는 것 같다.  
그래서 간단하게 테스트 코드 작성하는 방법을 정리해봤다.

abc 패키지를 만들어서 테스트하기 
---
src\abc\abc.go  
src\abc\abc_test.go 
와 같이 파일을 만들어 놓는다.

abc.go를 다음과 같이 작성한다.

```go
// abc.go

package abc

import (
    "fmt"
)

func A_Method() {
    fmt.Println("test A Method")
}
```

그리고 abc_test.go를 다음과 같이 작성한다. 

```go
// abc_test.go

package abc

import (
    "testing"
)

func TestA_Method(t *testing.T) {
    A_Method()
}
```

그리고 $GOPATH/src/abc/ 에서

```
$ go test
```

를 하면 테스트가 진행된다.
