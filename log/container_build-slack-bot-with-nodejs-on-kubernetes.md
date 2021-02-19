---
title: "7.Build Slack Bot With Node.js on Kubernetes"
date: 2019-01-14T13:17:21+09:00
---

이번 과정은 Secret 객체에 대해서 좀 더 자세하게 알아볼 수 있는 과정이었다.



서비스를 제공하는 프로그램(node.js 코드)을 Docker image로 만든 다음 Registry Server에 Push를 한다.

제공하는 서비스에서 외부의 서비스(slack)를 사용하기 위해서 민감한 정보인 token이 존재한다. 이 내용이 코드상 또는 image 상에 존재하지 않게 Image를 만들고자 한다.

그래서 별도로 "slack-token" 파일을 만들어서 파일을 통해서 token 정보를 제공하도록 만들어 놓았다. 해당 파일을 경로와 파일명을 포함해서 환경변수 "slack_token_path"에 등록해 놓고, 코드상에서는 환경 변수인 "slack_token_path"에 지정되어 있는 경로의 파일로 부터 token 정보를 읽어서 사용한다.



실습 과정에서 보여지는 Docker image를 만드는 Dockerfile에는 token 정보를 다루고 있지 않다. 이를 통해서 해당 정보는 배포가 되지 않는다는걸 알 수 있다.



이제 Kubernetes 클러스터에서 배포를 하기 위해서 token 정보를 container들이 인식할 수 있도록 해주어야 한다.

그래서 token 정보를 Secret으로 생성해서 클러스터에 배포한다.



다시 반복해서 확인을 해보면, container에서 제공되는 서비스는 "slack_token_path" 라는 이름의 환경 변수를 읽어 파일 경로를 구할 수 있다. 이 경로를 통해서 token 정보를 알아낸다. 그리고 구해진 token 정보를 이용해서 slack이 제공하는 기능을 이용할 수 있다.



이렇게 container에서 token 정보를 구할 수 있도록 하기 위해서, Deployment의 yaml 파일에서 container가 사용할 환경 변수를 등록할 수 있는 "env"를 이용해서 "slack_token_path"를 등록한다. 이 환경 변수(slack_token_path)는 token 정보가 존재하는 "경로와 파일명"의 조합이다.

이 경로에 해당 파일을 만들기 위해서 volumeMounts를 이용해서 지정된 경로에 volume을 mount 한다.



이때 사용되어질 "secret" 타입의 volume은 token 정보를 Pod에서 사용할 수 있도록 하는 "Secret" 객체이다.



token은 민감한 정보이기 때문에 별도의 과정을 통해서 전달되어 진다고 상상해 볼 수 있다. 어떠한 방식으로든지 전달된 token을 이용해서 "Secret"을 생성하는 과정을 거칠 수 있다.



Secret을 생성하는 인자 중 에 "generic"을 사용한다. generic은 로컬의 파일등을 이용해서 Secret을 만들겠다는 의미로 type에 따라서 Secret을 생성하는 방식을 달리 할 수도 있다.


