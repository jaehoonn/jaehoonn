---
title: "map에 key가 존재하는지 확인"
slug: 
description: null
date: 2018-07-12T16:52:08+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
series:
-
---

map을 사용할때 해당하는 키가 map에 존재하는지 확인이 필요할 때가 있다.

```go
var target[string]*item
...
target["apple"] = itm1
target["orange"] = itm2
...

value, ok := target["kiwi"]
```
