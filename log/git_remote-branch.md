---
title: "원격지의 branch 사용하기"
date: 2021-02-01T07:17:00+09:00
---

github에 있는 branch를 로컬로 받아와서 작업하고 싶을때 할 수 있는 방법

원격지에 있는 branch 정보를 확인 할 수 있도록 업데이트한다.
```
$ git remote update
```

원격지의 branch 확인
```
$ git branch -r
  origin/HEAD -> origin/master
  origin/dev
  origin/tapping
  origin/master
  origin/9999
  origin/release
  upstream/dev
  upstream/master
  upstream/8888
  upstream/release
```
branch들 중에서 "origin/9999"를 갖고 오고 싶다.
```
$ git checkout -t origin/9999
```
