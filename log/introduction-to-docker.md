---
title: "1.Introduction to Docker"
slug: introduction-to-docker
description: null
date: 2019-01-08T10:48:08+09:00
type: posts
draft: false 
categories:
- til 
tags:
- studyjam
series:
-
---

구글에서 지원해주는 “2019 클라우드 스터디잼 입문반” 스터디에 참여하기 시작했다.
이 스터디는 어떠한 형태로든 모인 멤버들이 일정 기간(1/7~1/27) 동안에 실습이 겸해진 과정(QWIKLABS - Kubernetes in the Google Cloud)을 완수하는 방식이다. 

첫번째 과정으로 “Introduction to Docker” 을 진행 했다. 다음은 과정을 진행하면서 학습할 수 있었던 내용의 요약이다. 

처음 해보는 과정이라서 그런지 익숙해지기 위한 시간이 약간 필요했지만 그다지 어렵지는 않았다.

Docker의 Image와 Container 그리고 Dockerfile의 이해를 위한 몇 가지의 실습이 진행되었다.
실습들을 통해서 Image와 Container를 어떻게 구분지어서 생각해야 하는지를 학습할 수 있었다.

그리고 만들어진 Image는 어떻게 배포하고 관리 되어져야 할 지에 대한 이해를 위해서, 구글에서 서비스하고 있는 플랫폼(Google Cloud Platform)이 제공하는 Registry Server에 gcloud라는 도구을 이용해서 Image를 등록하고 등록된 이미지를 이용하는 실습을 진행되었다.

이렇게 해서 첫번째 과정이 완료 되었다.

간단한 실습과 설명이었지만 Docker 관련 책에서의 분량으로 가늠해 보면 절반 정도의 내용을 압축해 놓은 과정이지 않았나 싶다.
