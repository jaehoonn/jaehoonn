---
title: "4.Managing Deployments Using Kubernetes"
slug: managing-deployments-using-kubernetes
description: null
date: 2019-01-09T11:15:55+09:00
type: posts
draft: false 
categories:
- til 
tags:
- studyjam
series:
-
---

이번 장에서는 "Deployment"가 어떤 역할을 할 수 있으며, 이를 이용해서 취할 수 있는 배포 전략에 대해서 알아볼 수 있었다.

(이번 장에서 실습을 위해서는 필수적으로 compute/zone 설정을 us-central1-a로 지정해 놓아야 cluster 생성을 정상적으로 할 수 있다.)



Deployment 뿐만이 아니라 다른 객체들을 사용하기 위해서도 yaml을 작성할때 각 필드에 대한 정보를 어디에서 확인해야 하는지가 궁금했다.

```
$ kubectl explain deployment

$ kubectl explain --recursive

$ kubectl explain deployment.metadata.name
```

등과 같이 확인을 할 수 있다.



Deployment는 ReplicaSet으로 Pod를 관리할 수 있도록 한다. 그래서 "replicas"를 조정해서 서비스를 확장하거나 축소할 수 있다.

Deployment가 수정되면 Rolling 업데이트 방식으로 기존의 Pod가 줄어들면서 새로운 Deployment가 적용된 Pod가 늘어나는 방식으로 교체가 된다.

ReplicaSet을 조회해보고  rollout의 history를 조회해서 업데이트의 상태를 확인해 볼 수 있었다. 이렇게 Deployment가 적용되는 방식에 대해서는 "rollout"의 pause, status, resume, undo 등으로 상태등을 조정할 수 도 있었다.



배포를 관리하는 방식으로 Canary, Blue-Green 과 같은 방식들이 있다.



처음에 Canary 배포에 대한 부분은 실습을 하다만 느낌이 들었는데, 그런 느낌은 Canary 배포의 목적에 대해서 알지 못 해서 그랬던것 같다.

실제 배포를 하기 전에 서비스의 실사용 환경에서 제한된 사용자를 선택해서 테스트 해보기 위한 정도라고 생각을 하고 실습을 다시 해보니 괜찮은 방법 같아 보였다. 실 서비스로 사용되어지는 Deployment와 배포되기 전에 확인을 위해서 사용되어질 Deployment(Canary)를 구분 지었을때, 문제가 발생하거나 하면 Deployment(Canary)만 조정을 하면 되니 신속한 관리가 가능할것 같았다.



Blue-Green 전략은 두 개의 Deployment(Blue/Green)를 적용해 놓고 Service를 원하는 Deployment에 적용시켜 놓는 방식으로 클러스터의 자원을 충분히 확보할 수 있을때 사용하면 좋은 전략인것 같다.



배포를 위한 전략들을 하나씩 실습해보면서 Deployment, Service 등의 각 객체의 특성을 잘 파악하고 있어야만, 어떻게 배치 할 수 있는지 그리고 어떤 식으로 운영할지에 대해서 계획 세울수 있겠다 싶었다.


