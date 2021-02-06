---
title: "8.Setting Up a Private Kubernetes Cluster"
slug: setting-up-private-kubernetes-cluster
description: null
date: 2019-01-16T01:46:28+09:00
type: posts
draft: false 
categories:
- til 
tags:
- studyjam
series:
-
---

이번 과정의 실습을 진행하면서 클러스터에 대한 접근을 제한 할 수 있도록 하기 위한 방법에 대해서 알 수 있었다. 하지만 전체 내용에 대해서 정확하게 이해를 하지 못했다.



해당 내용은 이후에 비공개 클러스터 설정(https://cloud.google.com/kubernetes-engine/docs/how-to/private-clusters) 에 대한 내용이 실습 내용과 동일하므로 추후에 이해가 가능한 시점이 되면 다시 한번 시도해 보려고 한다.



아래는 내용을 이해하기 위해서 학습했던 내용들의 링크이다.



- 네트워크 입문: http://kujung.tistory.com/category/%EB%98%A5%20%EC%8B%B8%EA%B8%B0/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC

- IPv4 네트워크 주소와 호스트 주소/서브넷팅/IP Class: http://itsaessak.tistory.com/174

- IP 주소체계와 클래스 구별법 (IPV4): http://korean-daeddo.blogspot.com/2015/12/ip.html

- 서브넷 마스크와 서브넷팅 계산법: http://korean-daeddo.blogspot.com/2016/01/blog-post_26.html



그리고 구글 클라우드 플랫폼 서비스에서 제공하는 GKE 관련 문서(https://cloud.google.com/kubernetes-engine/docs/) 들 중에서 함께 보면 도움이 되는 부분들이다.

- 별칭 IP를 사용하여 VPC 네이티브 클러스터 만들기: https://cloud.google.com/kubernetes-engine/docs/how-to/alias-ips?hl=ko

- 네트워크 개요: https://cloud.google.com/kubernetes-engine/docs/concepts/network-overview?hl=ko



그 외에도 "CIDR" 에 대해서 학습을 할 수 있었다.
