---
title: "5.Continuous Delivery With Jenkins in Kubernetes Engine"
slug: continuous-delivery-with-jenkins-in-kubernetes-engine
description: null
date: 2019-01-10T11:40:50+09:00
type: posts
draft: false 
categories:
- til 
tags:
- studyjam
- kubernetes
series:
-
---

이번 과정은 Jenkins와 Kubernetes를 이용한 배포 자동화를 실습해 볼 수 있었다.

분량은 많지만 이해가 어렵지는 않은 내용이었다.



실습을 해보면서 Namespace라는 객체와 Helm이라는 패키지 관리 도구를 사용해 볼 수 있다. Namespace는 일반적으로 우리가 알고 있는 용도인데, 논리적으로 무언가를 구분지어서 사용하고 싶을때 사용하는게 목적이라고 보면 될 것 같다.

이전 과정에서 배웠던 배포를 위한 전략에서는 label을 사용했었다면, 이번에는 Namespace를 사용해서 비슷한 문제 상황을 해결하는걸 경험해 볼 수 있다.



그리고 helm은 Kubernetes의 클러스터에 올라갈 수 있도록 미리 정의해서 배포해 놓은걸 사용할 수 있게 해주는 도구였다. 이번 실습 과정 중에는 Jenkins를 helm을 이용해서 설치를 했다.

(뒤에 있는 과정 중에서 helm을 좀 더 자세하게 다루는것 같으니 여기서는 이렇다는 정도만 확인하고 넘어가도 될 것 같다.)



이제 Jenkins를 통해서 배포가 자동화 되는 시나리오를 학습해 보기 위해서 서비스가 제공되는 과정을 만들어 놓는다.

실제 서비스와 Canary 배포를 위한 Namespace를 "production", "canary"로 만들어서 배포를 하고 Service를 적용해 놓았다.



그리고 Jenkins에서 Pipeline을 만들어 놓는다.

이 과정중에서 가장 중요한건 "Scan Multibranch Pipeline Trigger"가 아닐까 싶다.

이 설정으로 인해서 지정한 git 경로의 branch들에 변화가 생기는걸 지속적으로 확인하고 변화가 있을때 배포 작업을 시작한다. branch에 변화가 생긴걸 확인하게 되면 build 및 기타등등의 Jenkins가 목적으로 하는 작업들을 수행 후, Jenkins에서 Kubernetes의 클러스터에 해당하는 branch 명으로 Namespace를 만들어서 배포하게 된다.



개발하는데 사용이 되었던 branch가 "new-feature"라면 "new-feature"라는 이름의 Namespace가 생성되고 해당 Namespace에 배포를 하게 된다. Canary 배포를 하고자 한다면, branch를 "canary"로 만들어서 배포를 하면 된다. 그리고 master branch의 경우에는 Jenkinsfile에 보면, master branch의 경우 "production" Namespace를 이용하도록 지정되어 있다.



이번 과정에서는 Namespace를 활용하면, 하나의 Kubernetes 클러스터 상에서도 각자 목적이 다른 솔루션들이 영역 구분을 하고 배치해도 흐름이 끊기지 않고 계속해서 작업이 이어지도록 할 수 있다는걸 학습했다.

각 객체들(Deployment, Service, Namespace ...)의 정의와 특성 그리고 한계점에 대해서 명확하게 파악해 놓으면 필요한 상황별로 설계를 하는데 도움이 많이 될 것 같다.
