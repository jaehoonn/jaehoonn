---
title: "md5 패키지 사용예"
slug: 
description: null
date: 2013-10-08T17:10:53+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
series:
-
---



Qt로 만드는 클라이언트에서 이미지 파일에 대해서 만들어진 md5 hash를 검증 해보기 위해서, 같은 이미지 파일에 대해서 Go언어로 md5 hash를 만들었을때 동일한 값이 생성되면 제대로 만들어진거 아닐까 해서, 만들어 봄.  
(사실은 일하기 싫어서 괜히 한번 해 본...)  

```go
package main

import (
  "io/ioutil"
  "crypto/md5"
  "fmt"
)
 
func main() {
  data, err := ioutil.ReadFile("1.jpg")
  if err != nil {
    return;
  }
 
  hash := md5.New() 
  hash.Write(data)
  hashData := hash.Sum(nil)
 
  fmt.Printf("%x", hashData)
}
```
