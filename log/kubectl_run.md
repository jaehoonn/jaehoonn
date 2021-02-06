---
title: "Windows기반의 minikube로 로컬 환경의 image 사용해서 배포하기"
description: ""
date: "2020-05-19T01:05:57+09:00"
thumbnail: ""
categories:
  - til
tags:
  - ""
---

실제 클러스터에 배포하기 전에 로컬 환경의 minikube를 이용해서 배포하고 테스트를 하기 위한 방법이 필요하면 다음과 같이 한다.

minikube를 시작하고, minikube에 내장되어 있는 docker 데몬을 사용하도록 한다. 이렇게 하면 호스트 머신의 Docker Registry를 사용하고 image도 호스트 머신에 생성해 놓은 것을 사용할 수 있다.   
(https://kubernetes.io/docs/setup/learning-environment/minikube/#use-local-images-by-re-using-the-docker-daemon)

```shell
PS> minikube docker-env
$Env:DOCKER_TLS_VERIFY = "1"
$Env:DOCKER_HOST = "tcp://192.168.182.22:2376"
$Env:DOCKER_CERT_PATH = "C:\Users\jaehoonn\.minikube\certs"
# Run this command to configure your shell:
# & minikube docker-env | Invoke-Expression

PS> minikube docker-env | Invoke-Expression
```

이렇게 하면, minikube vm 안에 존재하는 docker 데몬과 통신을 하게 된다.

이제 만들어 놓은 이미지를 실행한다.

```shell 
PS> kubectl -n jaehoonn run sample-app --image=sample-app:latest --port=9000 --serviceaccount=jaehoonn-controller 
```

다음과 같이 배포가 된 것을 확인 할 수 있다.
```shell
PS> kubectl -n jaehoonn get pods
NAME                             READY   STATUS              RESTARTS   AGE
sample-app-685bbd44c6-kgc4z      1/1     Running   0          5h24m
```

웹브라우저를 통해서 연결 상태를 확인하기 위해서 Service를 생성한다.

```shell
PS> kubectl -n jaehoonn expose deployment sample-app --type="LoadBalancer"
```

생성된 Service를 확인하면, 다음과 같이 pending 상태이다.

```shell
PS> kubectl -n jaehoonn get services
NAME             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
sample-app       LoadBalancer   10.102.191.6   <pending>     8500:32416/TCP   11s
```

이제 minikube를 통해서 url을 생성한다.
```shell
PS> minikube -n jaehoonn service sample-app --url
* http://192.168.182.22:32416
```

필요한 테스트 작업을 완료하고 난 다음 해당 이미지를 중지하고자 할 때, 

```shell
PS> kubectl -n jaehoonn delete pods/sample-app-685bbd44c6-kgc4z
pod "sample-app-685bbd44c6-kgc4z" deleted
```

삭제 되었다는 메세지가 나오지만 실제로는 삭제 되지 않는다.
```shell
PS> kubectl -n jaehoonn get pod
NAME                             READY   STATUS              RESTARTS   AGE
sample-app-685bbd44c6-kgc4z      1/1     Running             0          5h24m
```

모든 리소스를 조회해보면 다음과 같다.

```shell
PS> kubectl -n jaehoonn get all
NAME                                 READY   STATUS              RESTARTS   AGE
pod/sample-app-685bbd44c6-fxl8f      1/1     Running             0          7m3s




NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/sample-app      1/1     1            0           5h36m

NAME                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/sample-app-685bbd44c6      1         1         0       5h36m
```

여기에서 deployment를 삭제하면 관련된 모든 리소스가 삭제된다.

```shell
PS> kubectl -n jaehoonn delete deployment/sample-app
deployment.extensions "discovery" deleted
```

다시 모든 리소스를 조회해보면 다음과 같이 모두 삭제된 걸 확인할 수 있다.

```shell
PS> kubectl -n jaehoonn get all

No resources found.
```

이 작업으로 Service까지 삭제 되지는 않는다.


