---
title: "Kubernetes에서 Private Container Registry 사용"
description: ""
date: "2020-04-10T16:31:48+09:00"
thumbnail: ""
categories:
  - til
tags:
  - "kubernetes"
---

Kubernetes에 배포하기 위해서 Container Registry(Private)을 사용하려면, 별도의 secret을 등록해야 한다.

```shell
$ kubectl create secret docker-registry {secret} \
--docker-server={registryserver} \
--docker-username={name} \
--docker-password={password} \
--docker-email={email} \
--namespace={namespace}
```

namespace는 지정하지 않으면 default로 지정된다.

그리고 Container Registry로 부터 image를 받아서 사용하기 위한 yaml 파일에 다음과 같이 추가한다.

```shell
apiVersion: v1
kind: Pod
metadata:
  name: private-reg
spec:
  containers:
  - name: private-reg-container
    image: regcred/projectaa/private-reg:latest
  imagePullSecrets:
  - name: regcred
```

"imagePullSecrets" 항목을 추가하고 containers.image에서 iamgePullSecretes.name을 base로 사용한다.