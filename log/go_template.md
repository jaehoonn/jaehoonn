---
title: "template을 생성할때 왜 이름을 지정할까?"
date: 2016-09-03T17:10:53+09:00
---

Go언어의 Template 엔진을 사용할때, 왜 New()의 파라미터로 이름을 만들어서 넘겨야 하는지가 궁금했었다.  
  
Template and Associated templates라는 글을 읽고 어느 정도 이해를 하게 되었다.  
  
다음은 위 글의 내용을 요약한 것이다.  
  
template.New() 로 template을 만들때, 이름을 정해서 만든다.  
이 때 FuncMap과 템플릿 리스트를 갖는 그룹(그룹의 이 글에서 사용하는 용어로 Associated Templates를 가르킨다)이 생성된다.  
FuncMap에 대해서는,  
https://golang.org/pkg/html/template/#FuncMap  
http://goinbigdata.com/example-of-using-templates-in-golang/  
http://technosophos.com/2013/11/23/using-custom-template-functions-in-go.html  
를 확인해본다.

New()를 했어도 아직 템플릿 리스트에서 관리되는 대상은 아니다.  
Parse(...)를 하면 템플릿 리스트에 추가가 된다.  
  
처음에 생성한 template "t1"으로 New()를 하면 연관된 템플릿을 생성할 수 있다.  
"t1"으로 생성한 template들은 동일한 템플릿 리스트에서 관리되어진다.  
  
현재 템플릿 리스트에서는 "t1" 템플릿을 더 이상 만들 수 없다.  
  
Lookup()으로 현재 템플릿 리스트에서 관리되고 있는 template을 찾을 수 있다.  
  
Templates() 함수로 템플릿 리스트의 template들을 모두 구한다.  
  
Execute() 와 ExecuteTemplate() 의 차이  
Execute() 는 호출한 template 객체를 실행한다.  
ExecuteTemplate() 는 호출한 template 객체가 관리되는 템플릿 리스트에서 인자로 넘겨진 이름의 template 객체를 찾아서 실행한다.  
  
ParseFiles() 는 파라미터로 넘겨진 파일 이름들을 템플릿 리스트에서 관리하는 리스트의 New()로 template을 생성할때 인자로 넘기는 name의 역할로 사용된다.
