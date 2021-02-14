---
title: "type으로 이름 붙여진 구조체의 필드를 사용하기위한 방법"
slug: 
description: null
date: 2013-09-03T17:10:53+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
series:
-
---

c로 만들어져 있던 라이브러리를 cgo를 이용해서 포팅(?)을 하는 작업을 하다보면 난감한 부분들이 종종 나타난다.  

그중에 한가지가 c에서 사용하던 구조체의 파라미터명이 go 에서 미리정의된 명칭일 경우가 있다.  
대표적인 예로 "type"이라는 파라미터명이다.  
c에서는 T_CCI_COL_INFO(cubrid의 cci에서 사용되는 구조체중에 하나) 의 필드중에 하나가 type이라는 필드가 하나 있다.  
이걸 go에서 사용을 하려고 하니,,,   
"...expected selector or type assertion, found 'type' "  이라는 결과를 만나게 된다.  

이걸 도대체 어떻게 사용해야 하나 고민을 하던중, golang.org 에 있는 문서 http://golang.org/cmd/cgo/ 에서 해결을 할 수 있었다.  

> Within the Go file, C identifiers or field names that are keywords in Go can be accessed by prefixing them with an underscore: if x points at a C struct with a field named "type", x._type accesses the field.
