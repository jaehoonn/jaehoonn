---
title: "C언어의 배열을 Go언어의 slices로 변환"
date: 2013-09-17T17:10:53+09:00
---

cgo를 이용하다보면, C언어로 작성된 라이브러리의 리턴값으로 포인터에 구조체가 할당되어서 넘어올때가 있다.  
C언어에서 넘어오는 구조체 배열이 포인터 형태로 넘어오니, Go의 포인터에 할당을 해서 사용 해봤다.  
당연히 제대로 동작하지 않는다. C언어로 작성된 라이브러리에서 넘어오는 값은 배열이니, Go언어에서는 slices에 할당을 하는게 맞는것 같다.     

다음의 내용을 보면, array를 어떻게 slices에 할당을 해야 할지에 대해서 잘 설명이 되어 있다.  
"Turning C arrays into Go slcies" ( https://code.google.com/p/go-wiki/wiki/cgo )  

아래는 cubrid driver를 만들면서 사용한 코드이다.

```
slcieHeader := (*reflect.SliceHeader)(unsafe.Pointer(&go_col_info)))
sliceHeader.Cap = int(col_count)
sliceHeader.Len = int(col_count)
sliceHeader.Data = uintptr(unsafe.Pointer(c_col_info))
```

* 예제  
https://github.com/jaehoonn/cubrid/blob/master/cas_gci.go
