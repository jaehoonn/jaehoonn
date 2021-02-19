---
title: "GitHub 페이지에 Hugo 올리기"
date: 2016-10-13T14:17:19+09:00
---

Blogger에서 Hugo( http://gohugo.io )로 갈아타는 과정을 정리해 본다.

Hugo 를 설치한다
---

https://github.com/spf13/hugo/releases 에서 본인의 환경에 맞는걸 찾아서 설치한다.  
Linux는 deb를 제공하고 있지만, Windows는 exe파일을 제공하고 있어서 환경변수(PATH)를 잡아주던가, 환경변수가 잡혀있는곳에 복사를 해준다.  
(이름도 hugo.exe로 바꿔주는게 사용하기에 더 편리한것 같다.)  
MAC은 안 써봐서 잘 모르겠다...;;;  

github에 저장소를 만든다
---

github에서 호스팅을 받아서 hugo를 사용하기 위해서는 2개의 저장소를 만들어야 한다.  
hugo의 컨텐츠를 관리하기 위한 저장소가 필요하다.(ex: https://github.com/jaehoonn/blog)  
그리고 컨텐츠를 보여주기 위한 github의 페이지 저장소(ex: https://github.com/jaehoonn/jaehoonn.github.io)

로컬에서 기초 작업을 한다
---

이제 로컬에서 hugo를 이용하여 컨텐츠를 관리하기 위한 워크 스페이스를 만들어 보자.  
다음과 같이 blog이라는 새로운 사이트를 만든다.

```
$ hugo new site blog 
```

blog 디렉토리로 들어가 보면, themes가 있다.
```
$ cd blog   
```

여기에 원하는 테마(ex: hemingway)를 받는다.  
테마는 http://themes.gohugo.io/ 에서 골라보면 된다.

```
$ cd themes  
$ git clone https://github.com/tanksuzuki/hemingway.git  
```

hemingway의 리모트를 제거한다.(이건 해도 그만 안해도 그만이다.)

```
$ cd hemingway    
$ git remote rm origin
```

이제 /themes/hemingway 에서 blog로 돌아온다.

```
$ cd ../..
```

config.toml을 수정해서 사이트의 기본 설정을 한다.  
이 부분은 개인 취향과 테마에서 필요한 설정들을 하도록 한다.

이제 로컬의 blog 디렉토리에 처음에 만들었던 github의 blog( github.com/jaehoonn/blog ) 을 리모트로 등록한다.

```
$ git init  
$ git remote add origin git@github.com:jaehoonn/blog.git
```

그리고 github.com/jaehoonn/jaehoonn.github.io를 blog의 서브모듈로 등록한다.

```
$ git submodule add -b master git@github.com:jaehoonn/jaehoonn.github.io.git public
```

컨텐츠를 만든다
---

컨텐츠를 만들기 위한 md 파일을 생성한다.

```
$ hugo new post/blah.md
```

위와 같이 하면 /content/post/blah.md 파일이 생성되어 있다.  
blah.md 파일의 상단 부분을 적당하게 수정하고, 아래쪽의 "+++" 다음 부터 내용을 작성한다.

컨텐츠 블로그에 반영
---

Linux의 경우 http://gohugo.io/tutorials/github-pages-blog/ 의 제일 하단에서 제공하는 deploy.sh를 사용하면 된다.  
Windows는 배치파일을 만들어서 사용해도 되겠지만, 귀찮으면 deploy.sh의 내용이 별로 어렵지 않은 내용이니, 하나씩 따라하면 된다.

내용을 간략하게 보면, 다음과 같다.

로컬의 /blog 경로에서 시작한다.

```
$ hugo -t hemingway   
```

를 실행해서 테마가 적용된 블로그 내용을 public에 생성한다.  
(혹시 개인 도메인을 사용하고 싶으면 CNAME 파일도 여기에서 등록을 해주면 된다.)

```
$ cd public
```

으로 public 디랙토리로 이동한다.

```
$ git add -A
```

public 디렉토리의 모든 파일을 add 한다.

```
$ git commit -m "blah"
```
이제 변경된 내용을 커밋한다.

```
$ git push origin master
```
지금 까지 작업한 내용을 github.com/jaehoonn/jaehoonn.github.io에 반영한다.

```
$ cd ..
```
blog 디렉토리로 돌아온다.

이제 blog의 변경된 내용을 github.com/jaehoonn/blog 에 반영을 한다.
```
$ git add -A  
$ git commit -m "blahblah"  
$ git push origin master
```

새로운 환경에서 컨텐츠 관리
---

항상 동일한 장소에서 컨텐츠를 만들고 있다면 모르겠지만,  
회사, 집 그리고 노트북에서 사용을 하고 있다거나 OS를 새로설치하고 다시 사용하기 위해서는,  
"github.com/jaehoonn/blog"을 새로 clone 받아서 사용해야 한다.  
이때 필요한 절차는 다음과 같다.

blog을 clone 받는다.

```
$ git clone git@github.com:jaehoonn/blog.git
```

submodule로 등록해 놓은 public의 내용을 업데이트 한다.
```
$ git submodule init  
$ git submodule update
```

테마(themes/hemingway)의 리모트를 추가하고 테마를 갖고 온다.  
(themes도 submodule로 등록해 놓고 사용하면 편리하지 않을까 싶다.)

```
$ cd themes  
$ cd hemingway    
$ git init  
$ git remote add origin https://github.com/masa0221/hemingway  
$ git pull origin master  
```

이렇게 하고 글을 작성하고 deploy.sh 를 실행하면, 잘 안된다.  
원인은 public으로 들어가서 확인을 해보면, 

```
$ cd public  
$ git status
```

다음과 같은 메세지를 보게 된다.

```
HEAD detached from 185cdaf  
nothing to commit, working tree clean
```

이게 무슨 상황인지는 다음의 링크를 확인해 보면 된다.  
[서브모듈 사용할 때 주의할 점들](https://git-scm.com/book/ko/v1/Git-%EB%8F%84%EA%B5%AC-%EC%84%9C%EB%B8%8C%EB%AA%A8%EB%93%88#서브모듈-사용할-때-주의할-점들)

그래서 링크에 나와 있듯이...
```
$ git checkout master
```

이제 다시 "blog/" 로 돌아가서, deploy를 하면 잘 올라간다.
