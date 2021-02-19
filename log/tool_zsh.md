---
title: "zsh(oh-my-zsh) 사용" 
date: 2016-04-21T14:39:36+09:00
---

zsh 설치하고 기본 쉘 변경
---

```
$ sudo apt-get install zsh  
$ which zsh  
/usr/bin/zsh  
$ chsh -s /usr/bin/zsh  
```

oh-my-zsh 설치
---

```
$ curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
```

설정(개인 취향)
---

.zshrc 의 ZSH_THEME를 minimal 로 변경
