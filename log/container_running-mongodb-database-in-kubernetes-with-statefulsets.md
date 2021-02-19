---
title: "6.Running Mongodb Database in Kubernetes With Statefulsets"
date: 2019-01-11T17:52:48+09:00
---

지금까지의 과정 중에서 가장 어려웠다.;;; 그리고 제대로 이해를 한건지도 잘 모르겠다.

일단을 이해했다고 생각되는 정도만 정리를 해봤다.



이번 과정은 StatefulSet에 대한 이해를 목표로 하고 있다.

StatefulSet에 대한 이해를 위해서는 Headless Service에 대한 이해와 StorageClass에 대한 이해가 필요했다.



StorageClass는 상태를 갖는걸 확인하기 위한 실습이 필요하다보니, Volume을 사용해야 하고 StatefulSet의 scale을 조정하게 되는 것에 따라서 Volume도 같이 조정이 되어야 해서 StorageClass 컨트롤러를 통해서 Volume을 동적으로 조정할 수 있도록 하려고 사용 된 것 같다.

(StorageClass는 다음에 기회가 되면 다시 좀 알아 봐야 할 것 같다.)



StatefulSet의 목적은 기존에 Deployment에서 상태가 없는 Pod들을 관리하기 위한 목적이 포함되어 있었다고 하면, 이 컨트롤러는 상태를 갖는 Pod를 관리하기 위한 목적이라고 이해를 했다. 데이터라는 상태를 갖는 mongodb를 실습에서 사용을 한 것 같다.



그리고 Deployment같은 컨트롤러의 경우에 Service 컨트롤러를 통해서 노출을 시켜줄 수 있었던 것 처럼 StatefulSet도 Service 컨트롤러를 이용해서 노출을 한다. 여기에서 StatefulSet은 Headless Service라고 불리는 설정으로 노출이 되게 된다. Service를 정의하고 있는 yaml파일을 보시면 “clusterIP: None”으로 지정이 되어 있는걸 볼 수 있다. 이는 로드밸런서의 영향을 받지 않는 서비스를 정의할 때 사용한다고 한다.



StatefulSet 컨트롤러를 통해서 관리되는 Pod는 생성되는 순서도 중요하기 때문에 Pod를 조회해보면 이름이 mongo-0, mongo-1... 과 같이 순서를 확인할 수 있는 숫자가 붙어 있다.

생성되는 순서도 0부터 차례대로 생성이 되고 scale을 통해서 replicas를 조정하면 차례대로 늘어나고 역순으로 줄어드는걸 확인 할 수 있었다.



Volume에 대한 이해가 없이 StatefuleSet을 바로 이해를 하려고 한다는게 무리가 있어 보인다. 그래서 이후에 꼭 Volume에 대한 이해를 해보는 기회를 갖어야 할 것 같다.

