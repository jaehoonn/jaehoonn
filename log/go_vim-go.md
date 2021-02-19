---
title: "vim-go 기본 템플릿 비활성화"  
date: 2016-09-05T01:28:09+09:00
---

vim-go가 언제부터인가 main.go 를 만들면 템플릿으로 fmt.Println("vim-go") 를 찍어주는 코드를 자동으로 생성해 주기 시작했다.

나는 아무것도 나오지 않기를 바랬는데, 이런게 나와서 매번 지우는 작업을 하는게 싫었다.
그래서 설정중에 go_template_autocreate 를 조절했다.

```
let g:go_template_autocreate = 0
```

와 같이 하면 이제 아무것도 없는 빈 화면이 나온다.

혹시라도 반복적으로 나오는게 좋은데 "vim-go"를 찍는게 아닌 특정한 코드가 필요하다면, ".vim/bundle/vim-go/templates/hello_world.go" 파일을 수정해도 된다.
또는 특정한 템플릿을 지정하고 싶다면, 다음과 같이,

```
let g:go_template_file = "hello_world.go"
```

로 해도 된다. 여기에서 hello_world.go 대신에 템플릿으로 지정하고 싶은 파일을 입력 하면된다.
