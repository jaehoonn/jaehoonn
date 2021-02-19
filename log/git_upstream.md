---
title: "upstream 설정"
date: 2021-01-03T03:29:09+09:00
---

프로젝트 진행하는 과정에서 필요한 upstream 설정

해당 내용은 kubernetes의 GitHub Workflow 문서를 참고 함
( https://github.com/kubernetes/community/blob/master/contributors/guide/github-workflow.md )


1. 프로젝트를 https://github.com/kubernetes/kubernetes 에서 내 github 계정으로 Fork한다.

2. Fork한 프로젝트를 내 로컬로 clone 한다.
```
$ git clone git@github.com:{your-account}/kubernetes.git

// 작업 디렉토리로 이동
$ cd kubernetes 

// 현재 브랜치를 확인한다.
$ git branch
* master
```


3. upstream으로 사용할 remote를 추가한다. 최초 프로젝트를 추가한다.
```
$ git remote add upstream git@github.com:kubernetes/kubernetes.git
```

4. remote를 확인해 보면 upstream이 생성되어 있다.
```
$ git remote -v
origin  git@github.com:{your-account}/kubernetes.git (fetch)
origin  git@github.com:{your-account}/kubernetes.git (push)
upstream        git@github.com:kubernetes/kubernetes.git (fetch)
upstream        git@github.com:kubernetes/kubernetes.git (push)
```

5. clone 받은 프로젝트에서는 upstream으로 직접 push하지 않도록 한다.
```
$ git remote set-url --push upstream no_push
```

6. remote의 push가 no_push로 변경된 것 을 확인한다.
```
$ git remote -v
origin  git@github.com:{your-account}/kubernetes.git (fetch)
origin  git@github.com:{your-account}/kubernetes.git (push)
upstream        git@github.com:kubernetes/kubernetes.git (fetch)
upstream        no_push (push)
```

7. upstream의 변경된 내용을 fetch 받는다.
```
$ git fetch upstream
remote: Enumerating objects: 20, done.
remote: Counting objects: 100% (20/20), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 12 (delta 7), reused 8 (delta 5), pack-reused 0
Unpacking objects: 100% (12/12), 2.00 KiB | 48.00 KiB/s, done.
From github.com:kubernetes/kubernetes
 * [new branch]      259    -> upstream/259
 * [new branch]      100    -> upstream/100
 * [new branch]      master       -> upstream/master
 * [new branch]      release      -> upstream/release
```

8. fetch 받은 내용을 master 브랜치에 적용한다.
```
$ git checkout master

$ git rebase upstream/master
Successfully rebased and updated refs/heads/master.
```

9. master 브랜치에 upstream의 내용이 적용된 걸 확인한다.
```
$ git log
.....
```

10. fork 받은 리모트에 적용한다.
```
$ git push origin master
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:{your-account}/kubernetes.git
   f89481d..922151e  master -> master
```
