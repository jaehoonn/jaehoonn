---
title: "core파일 만들기"
date: 2017-08-01T13:59:57+09:00
---

Go언어에서도 core dump를 만드는 방법이 있었다.  

데이브체니님이 정리해 놓은 무려 2015년도의 글이다.  
https://dave.cheney.net/2015/11/29/a-whirlwind-tour-of-gos-runtime-environment-variables  

환경변수로 GOTRACEBACK 을 crash로 설정만 해주면 된다.  

이런것도 모르고 Go언어에서는 dump를 생성할 수 없다고 말하고 다녔다...;  

근데 위에 처럼 환경변수 설정하고도 core 파일이 안생긴다면, 다음 블로그의 글을 참고해 보면 좋을것 같다.  

http://lapan.tistory.com/68  

블로그의 내용처럼 ulimit -a 로 확인 해보니 core file size가 0으로 되어 있다.  
ulimit -c unlimited 로 해주었다.  
이제 panic을 발생시켜 보면 core 파일이 잘 만들어져 있는걸 확인 할 수 있다.
