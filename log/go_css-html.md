---
title: "css와 같은 정적 컨텐츠를 html에서 사용하기 위한 팁"
date: 2016-07-18T10:16:09+09:00  
---

Go의 "net/http"와 "html/template"를 이용해서 html과 css로 만들어진 웹페이지를 만들려고 할때, html 파일이 보여질때 css가 적용되지 않은 상태로 보여진다.  
이 때 어떻게 해야 하는지 방법을 찾아가던 과정을 기록으로 남겨 놓는다.  

프로젝트의 디렉토리 구조는 다음과 같다.  

```
src\
 + prj
    + main.go
    + static
        style.css
    + template
        index.html
```

그래서 이렇게...  

```go
func main() {
  http.HandleFunc("/", indexHandler)
  http.ListenAndServe(":8080", nil)
}

func indexHandler(...) {
  // index.html
}
```

와 같이 하면 될 줄 알았다.  
그런데 안된다.  

그래서 어떻게 해야 하는지 찾아보니, FileServer로 css파일을 제공해 주면 된다고 한다. 그래서 다음과 같이 했다.  

```go
func main() {
  fs := http.FileServer(http.Dir("/static"))
  http.Handle("/static/, fs)
  http.HandleFunc("/", indexHandler)
  http.ListenAndServe(":8080", nil)
}

func indexHandler(...) {
  // index.html
}
```

근데 안된다.  
http.Dir에서 만들어 낸 경로는 "/static"이 존재한다.  
그래서 좀 더 찾아보니 StripPrefix를 사용해서 경로를 만들어 줘야 한다.  
fs 핸들러에서 "/static"를 제거해준다.  

```go
func main() {
  fs := http.FileServer(http.Dir("/static"))
  http.Handle("/static/, http.StripPrefix("/static", fs))
  http.HandleFunc("/", indexHandler)
  http.ListenAndServe(":8080", nil)
}

func indexHandler(...) {
  // index.html
}
```

이제 잘된다!!!  

css와 같은 정적 컨텐츠를 제공하기 위해서 사용할 수 있는 팁인것 같다.
