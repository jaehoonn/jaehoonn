---
title: "개발환경 설정"
slug: 
description: null
date: 2015-05-23T01:28:09+09:00
type: posts
draft: false 
categories:
- til
tags:
- first
series:
-
---

업데이트
---
```
$ sudo apt-get update  
$ sudo apt-get install build-essential
```

vim 설치
---
```
$ sudo apt-get install vim
```

vim 8.0
---
```
$ sudo add-apt-repository ppa:jonathonf/vim  
$ sudo apt-get update
```

xclip 설치
---

```
$ sudo apt-get install xclip
```

wget 설치(사용이 필요한 경우에만 설치)
---
```
$ sudo apt-get install wget
```

git 설치  
---

1. git 설치와 설정

```
$ sudo apt-get install git  
$ git config –-global user.name “blah”  
$ git config –-global user.email “blah@blah.com”  
$ git config --global core.editor vim  
$ git config --global core.commentchar "*"  
$ ssh-keygen -t rsa -C “blah@blah.com”  
$ xclip -sel clip < ~/.ssh/id_rsa.pub
```

2. github/bitbucket에 ssh key 값을 등록

GOPATH로 사용할 위치 생성
---

```
$HOME/work  
bin  
pkg  
src
```

Go 설치
---

1. 기존에 설치된 버전이 있다면 삭제한다.
```
$ rm -r /usr/local/go
```
2. http://golang.org/doc/install 에서 다운로드  
   (또는$ wget --no-check-certificate https://storage.googleapis.com/golang/go1.7.1.linux-amd64.tar.gz 와 같이 원하는 OS 및 버전에 맞는 Go 바이너리를 wget으로 받을 수도 있다.)  
3. /usr/loca/go에 압축을 푼다.  
```
$ sudo tar -C /usr/local -xzf gox.x.x.linux-xxx.tar.gz
```

4. $HOME/.profile 에 다음을 추가한다.  
```
export PATH=$PATH:/usr/local/go/bin  
export GOPATH=$HOME/work
```
추가) app engine도 사용 할 경우에는 PATH에 app engine의 위치도 추가  

5. 적용  
```
$ source ~/.profile
```


ctags 설치
---

1. 설치
```
$ sudo apt-get install ctags
```
2. go가 설치된 위치로 이동(/usr/local/go)해서 tag 생성   
```
$ sudo ctags -R
```
3. vimrc 파일에 다음을 추가한다.  
```
set tags +=/usr/local/go/tags
```

python과 cmake 설치
---

* YouCompleteMe라는 vim 플러그인의 정상적인 설치를 위해서 python과 cmake를 설치한다.  
  ```
  $ sudo apt-get install python-dev python3-dev  
  $ sudo apt-get install cmake
  ```

Vundle.vim을 이용한 NERDTree와 fatih/vim-go 설치
---

* Vundle.vim 설치
  ```
  $ git clone https://github.com/gmarik/Vundle.vim.git ~/.vim/bundle/Vundle.vim
  ```
* /etc/vim/vimrc.local 을 생성해서 아래의 내용을 붙여 넣는다.

```Shell
set nocompatible " be iMproved, required
filetype off " required

set autoindent
set smartindent
set shiftwidth=4
set number
set expandtab
set tabstop=4

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'gmarik/Vundle.vim'

" The following are examples of different formats supported.
" Keep Plugin commands between vundle#begin/end.
" plugin on GitHub repo
Plugin 'tpope/vim-fugitive'
" plugin from http://vim-scripts.org/vim/scripts.html
Plugin 'L9'
" Git plugin not hosted on GitHub
Plugin 'git://git.wincent.com/command-t.git'
" git repos on your local machine (i.e. when working on your own plugin)
"Plugin 'file:///home/gmarik/path/to/plugin'
" The sparkup vim script is in a subdirectory of this repo called vim.
" Pass the path to set the runtimepath properly.
Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
" Avoid a name conflict with L9
"Plugin 'user/L9', {'name': 'newL9'}

Plugin 'The-NERD-tree'
Plugin 'bling/vim-airline'
Plugin 'fatih/vim-go'
Plugin 'nsf/gocode'
Plugin 'Tagbar'
Plugin 'Valloric/YouCompleteMe'

" All of your Plugins must be added before the following line
call vundle#end() " required
filetype plugin indent on " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList - lists configured plugins
" :PluginInstall - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line
```

* vim 실행해서 “ :PluginInstall “ 을 해서 지정된 플러그인들을 설치한다.  

* NERDTree의 설정을 한다.  
```
$ sudo vim /etc/vim/vimrc.local
```

을 열어서 적당한 곳에 아래의 내용을 붙여넣는다.

```
nmap <F2> :NERDTreeToggle<CR>
let NERDTreeWinPos="left"
```

* Tagbar 설정  
 위의 NERDTree 설정 부분 아래에 추가한다.

```
nmap <F3> :Tagbar<CR>
let Tagbar="right"
let g:tagbar_type_go = {
    \ 'ctagstype' : 'go',
    \ 'kinds'     : [
        \ 'p:package',
        \ 'i:imports:1',
        \ 'c:constants',
        \ 'v:variables',
        \ 't:types',
        \ 'n:interfaces',
        \ 'w:fields',
        \ 'e:embedded',
        \ 'm:methods',
        \ 'r:constructor',
        \ 'f:functions'
    \ ],
    \ 'sro' : '.',
    \ 'kind2scope' : {
        \ 't' : 'ctype',
        \ 'n' : 'ntype'
    \ },
    \ 'scope2kind' : {
        \ 'ctype' : 't',
        \ 'ntype' : 'n'
    \ },
    \ 'ctagsbin'  : 'gotags',
    \ 'ctagsargs' : '-sort -silent'
    \ }
```

추가) vim-go 설치가 완료되고 난 다음에,  
$GOPATH/bin 에 생성되어 있는, errcheck, gocode, gofmt, golint, gotags, goimports, gorename, oracle
 를 /usr/local/go/bin 에 복사/붙여넣기 한다.

[참고] https://gist.github.com/jaehoonn/47d81a29b620a0d64f80

* YouCompleteMe 설치  
YouCompleteMe의 설치를 완료하기 위해서 Bundle 디렉토리로 이동해서 Go언어 자동완성이 가능하도록 옵션을 지정해서 install을 한다.

```shell
$ cd ~/.vim/bundle/YouCompleteMe
$ ./install.py --gocode-completer
```

* cscope를 사용하기 위한 설정  
[참고] https://github.com/jaehoonn/log/blob/master/cscope.md  
[script] https://gist.github.com/jaehoonn/bc257991fe8932944d8fa9a9556eb223  
