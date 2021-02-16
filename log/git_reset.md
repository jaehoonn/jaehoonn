---
title: "git push된 내용을 특정한 commit으로 되돌리기"
slug: 
description: null
date: 2018-07-31T10:09:41+09:00
type: posts
draft: false 
categories:
- til
tags:
- git
series:
-
---

"iss-123" 브랜치에서 작업하던 내용을 실수로 "iss-133" 브랜치로 push 하는 어이없는 실수를 했을때 "iss-133"에 push 된 내용을 이전으로 되돌려 놓아야 한다.

git log로 이전 commit의 id를 확인한다. (ex, d50fd96d2d7e5e5cf689b0943f2b2d20d4c2dda4)


```
[iss-133] $ git reset --hard d50fd96d2d7e5e5cf689b0943f2b2d20d4c2dda4
```

이제 변경된 내용을 push 한다.

```
[iss-13] $ git push --force origin prj
```
