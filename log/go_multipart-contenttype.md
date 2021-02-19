---
title: "CreateFormFile()을 사용하면 Content-Type이 고정되는 현상"
date: 2016-12-10T18:22:48+09:00
---

mime/multipart를 사용해서 파일을 업로드 하려 할때 파일을 업로드할 필드를 생성하기 위해서 CreateFormFile()을 사용한다.  
이를 이용해서 파일을 업로드하면 "Content-Type"이 "application/octet-stream" 으로 고정되어진다.  

src/mime/multipart/writer.go의 CreateFormFile을 확인해 보면 다음과 같이 고정되어 있는걸 확인 할 수 있다.  

```go
h.Set("Content-Type", "application/octet-stream")
```

관련한 이슈가 올라온게 혹시 있지 않을까 해서 찾아보니.  
이런 이슈가 있었다.  
https://github.com/golang/go/issues/16425

bradfitz는 다음과 같이 답변을 하고 있다.   

> Use CreatePart.
CreateFormFile is a very thin wrapper around CreatePart. Click https://golang.org/pkg/mime/multipart/#Writer.CreateFormField[] and then click the CreateFormFile heading to see its source code.

그래서 난 CreateFormFile() 함수의 구현을 수정해서 CreateFormImage()함수를 만들어서 사용하기로 했다.  

```go
file, fileHeader, _ := r.FormFile("image")
...
var b bytes.Buffer
wr := multipart.NewWriter(&b)

h := make(textproto.MIMEHeader)
h.Set("Content-Disposition",  fmt.Sprintf(`form-data; name="%s"; filename="%s"`, escapeQuotes("image"), escapeQuotes(fileHeader.Filename)))
h.Set("Content-Type", "image/jpeg")
fw, err := wr.CreatePart(h)
```

CreateFormFile()을 사용하던 부분을 위의 코드로 교체해서 전송 하면 Content Type이 원하는 형태(image/jpeg")로 전달이 되어진것을 확인 할 수 있다.  
escapeQuotes() 함수는 .../multipart/writer.go의 구현을 참고하면 된다.

