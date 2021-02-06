---
title: "http 패키지의 HandleFunc과 Handle"
slug: 
description: null
date: 2016-07-17T17:10:53+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
series:
-
---

HandleFunc
---

* http.ResponseWriter 와 \*http.Request를 인자로 받는 함수를 전달받는다.
* 내부적으로 ServeHTTP가 구현된 ServeMux를 사용한다.  

```go
package main
import (
 "net/http"
)
func indexHandler(w http.ResponseWriter, r *http.Request) {
 .... }
func main() {
 http.HandleFunc("/", indexHandler)
 http.ListenAndServe(":8080", nil)
}
```

Handle
---
* http.Handler 인터페이스를 구현한 객체를 전달받는다.
* 인자로 전달받은 객체는 http.Handler의 ServeHTTP(http.ResponseWriter, \*http.Request)가 구현되어 있어야 한다.  

```go
package main
import (
 "net/http"
)
type indexHandler struct {
}
func (index *AppHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
 ...
}
func main() {
 index := new(indexHandler)
 http.Handle("/", indexHandler)
 http.ListenAndServe(":8080", nil)
}
```
