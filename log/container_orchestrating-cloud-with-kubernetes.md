---
title: "3.Orchestrating the Cloud With Kubernetes"
date: 2019-01-09T01:26:47+09:00
---

이번 장은 우선 "nginx"와 "monolith"라는 단어에 현혹되지 않도록 주의를 해야 할 것 같다.



최초에 nginx가 실행되는 Pod는 그거대로 Kubnernetes 작동에 대한 내용이고, Pod에 대한 내용에서 언급되는 nginx와 monolith는 또 그것대로의 내용을 설명한다.

그리고 다음에 나오는 port-forward에 대한 내용은 또 완전히 별개의 내용이다.



port-forward는 "Service"로 노출하지 않는 Pod에 대해서 테스트등을 해보기 위해서 아주 유용한 도구인 것 같다.

port-forward로 "monolith" Pod의 80 포트를 10080을 지정하면 local의 10080 포트를 사용하고 있는것 처럼 이용이 가능하다.



"Service"는 Pod가 수시로 죽고 살아나는 과정 중에서 외부에 노출될 수 있는 일정한 IP를 제공하기 위한 목적이다.

"Service"는 "selector"를 이용해서 Pod에 지정된 "label" 별로 Pod들을 선택할 수 있다. 그리고 "Service"의 형태로는 "ClusterIP", "NodePort", "LoadBalancer"가 있다.



"secure-monolith" Pod를 생성하는 과정중에 "secret"와 "configmap"을 생성하는게 나오는데 아직은 크게 신경쓰지 않아도 될 것 같다.

둘 다 비슷한 성격을 갖는데, "secret"는 패스워드 처럼 민감 데이터를 다루는 성격이고, "configmap"은 환경 설정과 같은 성격의 정보를 다룬다. 특성 또는 상태를 갖는 정보를 포함하지 않고 Pod가 관리될 수 있도록 해준다.



"Deployment"는 "replicas" 항목에 지정된 만큼의 Pod를 관리하기 위한 용도이다.



앞부분에서 약간의 착각을 할 수 있는 부분이 있기는 했었지만 전체적으로 간단하게라도 kubernetes를 이용한 관리방법에 대해서 이해를 할 수 있는 장 이었던것 같다.
