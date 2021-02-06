---
title: "vendor 사용 팁(내부 패키지)"
slug: 
description: null
date: 2016-03-09T14:12:58+09:00
type: posts
draft: false 
categories:
- til
tags:
- go
- vendor
series:
-
---

프로젝트 내부에서 별도의 패키지를 만들어서 사용을 하려고 할때면,  
import 경로를 "github.com/blah/project/internalpkg" 처럼 다 써야 했었다.  
이제 vendor를 사용하면 pkg 이름만 import 해서 사용할 수 있게 되었다.

샘플 프로젝트는 다음과 같다.  
https://github.com/jaehoonn/lotto

샘플 프로젝트에 대해서 약간의 설명을 더하면,  
기존에는 command 패키지를 commands.go 에서 import 하기 위해서,  
**import "github.com/jaehoonn/lotto/command"** 와 같이 사용해야 했었다.

이렇게 되면 lotto 프로젝트를 다른 사람이 fork 해서 사용하려고 하면 일일이 import 경로를 변경해줘야 하는 문제가 생기게 된다.

그래서 go1.6 부터는 vendor를 사용하면 이러한 문제를 해결할 수 있게 되었다.

```
src
  + github.com
     + jaehoonn
        + lotto
           + vendor
               + command
```

와 같이 command 패키지를 vendor 디렉토리 아래에 배치를 한다.  
이제 commands.go 에서 command를 사용하기 위해서,  
**import "command"** 와 같이 변경이 될 수도 있는 경로들은 제외하고, pkg 이름만 지정해주면 된다.
