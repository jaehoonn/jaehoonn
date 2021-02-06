---
title: "tmux"
slug: 
description: null
date: 2016-10-12T01:28:09+09:00
type: posts
draft: false 
categories:
- til
tags:
- tmux
series:
-
---

tmux 시작
---

```
$ tmux  
$ tmux new-session(or new) -s [세션 이름] -n [윈도우 이름]
```

tmux 종료
---

```
$ ctrl-d  
$ exit
```

tmux detach(현재 클라이언트)
---

```
$ ctrl-b, d
```

tmux attach
---

```
$ tmux attach -t [세션 이름]
```

window 생성
---

```
$ ctrl-b, c
```

window 리스트
---

```
$ ctrl-b, w
```

window 이동(현재의 이전(다음, 이전)
---

```
$ ctrl-b, l(n, p)
```

window 종료
---

```
$ ctrl-b, &
```

window 이름 변경
---

```
$ ctrl-b, ,
```

pane 가로 나누기
---

```
$ ctrl-b, %
```

pane 세로 나누기
---

```
$ ctrl-b, "
```

pane 의 번호를 화면에 출력
---

```
$ ctrl-b, q
```

pane 크기 조절
---

```
$ ctrl-b, (ctrl + 방향키)
```

모든 pane의 크기 동일하게 만들기
---

```
$ ctrl-b alt-1 # 모든 vertical split 넓이를 동일하게 변경
$ ctrl-b alt-2 # 모든 horizontal split의 넓이를 동일하게 변경
```

pane 이동하기
---

```
$ ctrl-b, o
```

pane 이동하기
---

```
$ ctrl-b, 방향키  
$ ctrl-b {(})
```

pane 삭제하기
---

```
$ ctrl-d
```

pane 화면 스크롤 모드 시작
---

```
$ ctrl-b, [
```

pane 화면 스크롤 모드 종료
---

```
$ q
```

입력되는 내용을 모든 pane에 동일하게 적용
---

```
$ ctrl-b

: setw synchronize-panes
```

http://nodeqa.com/nodejs_ref/99  
http://nodeqa.com/nodejs_ref/100  
http://nodeqa.com/nodejs_ref/101  
http://superuser.com/questions/209437/how-do-i-scroll-in-tmux  
