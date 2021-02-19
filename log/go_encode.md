---
title: "인코딩된 텍스트 디코딩하기"
date: 2016-11-25T00:52:47+09:00
---

Javascript에서 encodeURIComponent() 함수를 사용해서 전달받은 문자열을 디코딩해서 보기 위해서는 "net/url" 패키지의 QueryUnescape() 함수를 사용할 수 있다.

```go
email, err := url.QueryUnescape(user.Email)
```
