---
title: "9.Helm Package Management"
date: 2019-01-17T15:04:00+09:00
---

이번 과정은 kubernetes에서 사용할 수 있는 패키지 관리자에 대해서 알아 볼 수 있었다.

Helm은 클라이언트 역할을 하는 helm, 서버 역할을 하는 tiller 그리고 설정 정보들의 관리를 위한 chart로 이루어져 있다.

여기에서 클라이언트(helm)라고 하는건 클러스터의 외부에서 작업을 지시하기 위한 도구이고, 실제로 클러스터 안쪽에서 동작 하는건 서버(tiller)라고 보면 된다.



helm의 설치는 간단합니다.


```
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh

$ chmod 700 get_helm.sh

$ ./get_helm.sh
```


근데 이 과정 다음에 갑자기 tiller를 위한 계정을 만들고 해당 계정을 무언가에 바인딩을 한다.

이 과정은 tiller가 클러스터 전체에서 권한을 갖기 위한 방법으로 여기면 될 것 같다.

비교가 적절할지 모르겠지만, 리눅스에서는 "sudo" 로 권한을 얻고, 윈도우에서 "관리자 권한"을 얻는 것 처럼 tiller에게도 더 높은 권한을 갖을 수 있도록 해주는 과정이라고 이해를 했다.

(권한에 대한 내용은 "http://bcho.tistory.com/1272" 를 참고하면 좋을것 같다.)



계정을 생성하고 권한을 설정하는 과정은 다음과 같다.


```
$ kubectl -n kube-system create serviceaccount tiller

$ kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
```


그리고 권한을 제거하고 계정을 삭제하는 과정은 다음과 같이한다.


```
$ kubectl delete clusterrolebinding tiller

$ kubectl -n kube-system delete serviceaccount tiller
```




이러한 과정은 kubernetes의 권한 관리 방식을 rbac를 이용할 경우에 이렇게 하고, rbac 이외에 abac라는 방식은 다른 방법이 존재하는것 같다.



계정을 생성했으면 tiller를 설치한다.


```
$ helm init --service-account=tiller
```




tiller가 설치된 후에 "kubectl get po --namespace kube-system" 로 확인해 보면 "tiller-deploy-blahblah"가 실행되고 있는걸 확인 할 수 있다.



그리고 tiller를 제거하기 위해서는


```
$ kubectl -n kube-system delete deployment tiller-deploy
```


를 해주고 위에서 이야기했던 권한 제거하는 작업을 해주면 된다.



그리고 chart를 업데이트하고 chart를 이용해서 설치하는 과정은 아무런 고민없이 사용이 가능할 정도로 간단다.

