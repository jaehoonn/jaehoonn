---
title: "Go코드로 HTML Form 전송"
slug: 
description: null
date: 2016-12-06T01:13:41+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
series:
-
---

HTML, Javascript를 사용해서 Post방식으로 Form을 전송하는 동작을 Go 코드를 작성해 보자.  
우선 POST로 전송된 Form을 받아서 처리를 하는 서버쪽을 다음과 같은 구성을 갖는 코드로 만든다.  


```go
package main

import {
    "net/http"

    "github.com/gorilla/mux"
}

func main() {
   r := mux.Newrouter().StrictSlash(false)
   r.HandleFunc("/upload", upload).Methods("POST")

   http.ListenAndServe(":8080", r)
}

func upload(w http.ResponseWriter, r *http.Request) {
    if r.Method == "POST" {

    }
}
```

이렇게 "http://localhost:8080/upload" 를 통해서 POST 방식으로 전달되는 요청을 받아서 처리하기 위한 큰틀을 만들었다.  

이제 upload() 를 구현해 보자.  
우선 Form의 필드로 "user"를 받아오는 부분을 구현해 보면 다음과 같다.  

```go
func upload(w http.ResponseWriter, r *http.Request) {
    if r.Method == "POST" {
        user := r.FormValue("user")
    }
}
```

위의 코드는 다음과 같은 HTML의 Form을 받아서 처리 하기 위한 구현이다.  

```html
<html>
    <body>
        <form action="/upload" method="POST">
            user: <input type="text" name="user">
            <input type="submit"  name="submit" value="submit">
        </form>
    </body>
</html>
```
이 HTML을 Go 코드로 구현을 하기 위해서는 http 패키지의 PostForm() 함수를 사용할 수 있다.  

```go
resp, err := http.PostForm("localhost:8080/upload", url.Values{"user", {"jaehoonn"}})
```

그럼 HTML의 Form을 통해서 파일을 업로드를 할 경우에는 어떻게 해야 할까?  
그냥 url.Values를 사용해서 파일을 업로드 할 수 있을까? 안되는것 같다.  

http 통신에서 파일을 전송하기 위해서는 mime/multipart 패키지를 사용할 수 있다.  

우선 Form을 받아서 각 필드를 처리하기 위한 부분을 구현해 보면 다음과 같다.  

```go
func upload(w http.ResponseWriter, r *http.Request) {
    if r.Method == "POST" {
        user := r.FormValue("user")
        file, handler, _ := r.FormFile("image")
        defer file.Close()

        f, err := os.OpenFile("./" + handler.Filename, os.O_WRONLY | os.O_CREATE, 0666)
        if err != nil {
            return
        }
        defer f.Close()
        io.Copy(f, file)
    }
}
```

위의 코드는 http.Request를 통해 전달된 Form의 필드 중 "image" 필드의 파일을 FormFile() 함수를 사용해서 구한 다음 전달된 파일이름으로 서버에서 파일을 생성하도록 하고 있다.  

이제 File을 전달하는 HTML 코드를 살펴보자.  

```html
<html>
    <body>
        <form action="/upload" method="POST" enctype="multipart/form-data">
            user: <input type="text" name="user"><br>
            file: <input type="file" name="image">
            <input type="submit"  name="submit" value="submit">
        </form>
    </body>
</html>
```

이렇게 하면 user라는 문자열 필드와 image라는 파일 필드를 갖는 Form이 전송된다.  

이제 이 HTML의 내용을 Go코드로 구현을 하면 다음과 같다.

```go
func main() {
    url := "http://localhost:8080/upload"

    var b bytes.Buffer
    w := multipart.NewWriter(&b)
    f, err := os.Open("./sample.jpg")
    if err != nil {
        return err
    }
    defer f.Close()
    
    fw, err := w.CreateFormFile("image", "./sample.jpg")
    if err != nil {
        return
    }
    if _, err = io.Copy(fw, f); err != nil {
        return
    }
    if fw, err = w.CreateFormField("user"); err != nil {
        return
    }
    if _, err = fw.Write([]byte("jaehoonn")); err != nil {
        return
    }

    w.Close()

    req, err := http.NewRequest("POST", url, &b)
    if err != nil {
        return
    }

    req.Header.Set("Content-Type", w.FormDataContentType())

    client := &http.Client{}
    res, err := client.Do(req)
    if err != nil {
        return
    }
}
```

위의 코드는 로컬의 현재 위치에 있는 "sample.jpg" 파일과 "jaehoonn"이라는 이름을 전송하려고 한다.  

mime/multipart의 Writer를 생성해서 File 필드를 하나 만들고(CreateFormFile), 일반 필드를 하나 만든다.(CreateFormField)  
HTML의 Form에서 action에 해당하는 url과 파일 버퍼를 사용해서 request를 만든다(NewRequest)   
만들어진 request와 http.Client를 이용해서 POST방식의 Form을 전달한다.

