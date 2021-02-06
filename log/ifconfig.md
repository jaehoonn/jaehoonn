---
title: "ifconfig 사용"
slug: 
description: null
date: 2016-07-21T01:28:09+09:00
type: posts
draft: false 
categories:
- til
tags:
- linux
series:
-
---

임시로 사용할 개발환경을 만들기 위해서 ubuntu 이미지를 사용하는데,  
ifconfig 명령이 동작하지 않아서 보니, net-tools가 온전하게 설치되었지가 않은것 같다.

```
$ apt-get install --reinstall net-tools
```
