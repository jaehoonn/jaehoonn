---
title: "10.Nginx Ingress Controller on Google Kubernetes Engine"
slug: nginx-ingress-controller-on-google-kubernetes-engine
description: null
date: 2019-01-21T17:05:30+09:00
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

이번 과정에서는 "Ingress"에 대해서 알아 볼 수 있었다.



"Ingress"는 resource와 controller로 구성되어 있다.

resource는 "Ingress"의 동작에 대한 규칙을 정해 놓은 yaml이며,

controller는 layer7에 해당하는 로드밸런서의 역할을 제공한다. 즉 http 요청에 대한 처리와 부하 분산을 제공한다.

controller로는 gce, nginx, envoy, haproxy, istio, kong, traefik 을 이용할 수 있다.



과정에서는 "hello-app" 이라는 "Service"를 노출한다. 그리고 helm을 이용해서 nginx-ingress를 kubernetes cluster에 설치한다.

kind가 "Ingress"인 yaml 파일을 만든다. 이때 "path: /hello"로 지정하며, backend를 "serviceName: hello-app"과 "servicePort: 8080" 으로 지정을 한다.



이렇게 해서 "nginx-ingress-controller"가 제공하는 "EXTERNAL-IP"에 "/hello"로 웹브라우저에서 연결을 해보면 서비스가 제공되는 것을 확인해 볼 수 있다.



"Ingress"를 통해서 로드밸런서를 제공하기 위해서는 "Service" 생성 과정에서 type의 지정이 필요하다.



"Pod"는 죽고 사는 과정이 계속해서 반복되고 ip가 계속해서 변동된다.

이러한 "Pod"를 선택적으로 동일한 ip로 접근하기 위해서 "Service"를 사용한다.

이 "Service"들이 제공하는 타입에 따라서 서비스가 제공되는 형태가 변경이 된다.



clusterip는 클러스터 내부에서만 접근 가능한 형태의 service 이다.

그리고 nodeport는 "node이름:port"로 외부에서도 접근이 가능하도록 서비스를 제공하는 형태이다.

loadbalancer는 클라우드 서비스를 제공하는 벤더들에서 제공하는 로드밸런서를 사용한다.

마지막으로 externalname은 외부의 서비스를 클러스터 내부에서 사용하고자 할 때 사용하는 형태이다.



이와 같이 클라우드 서비스 벤더에서 제공하는 로드밸런서를 사용할때는 "Service"의 타입을 loadbalancer로 지정하고, "Ingress"를 통해서 제공하는 로드밸런서를 사용할때는 nodeport를 사용해서 "Service"를 생성한다.


