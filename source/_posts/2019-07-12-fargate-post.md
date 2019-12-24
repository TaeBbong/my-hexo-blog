---
layout: post
title:  "AWS Fargate 알아보기"
date:   2019-07-15
excerpt: "AWS 백엔드 시스템 스터디 프로젝트"
tag: 
- development
- aws
- backend
- server
- study

comments: true
---


## AWS Fargate란
![AWS Fargate](https://cloud.joinc.co.kr/wp-content/uploads/2019/01/aws-fargate-v2.png){: width="100" height="100"}
Fargate를 이해하기 위해서는 컨테이너 오케이스트레이션에 대해 살짝 알아야 합니다.
컨테이너라는게 명확히 이해하기는 어렵지만, 도커나 쿠버네티스를 생각하면 조금 더 쉽습니다.
일반적으로 웹 서비스를 배포할 때 백엔드 설정을 직접 해주게 됩니다.
이 직접 설정해야하는 부분을 단계별로 자동화할 수 있는데, 얼마만큼 자동화하느냐에 따라 IaaS, PaaS, SaaS 등으로 나뉘어집니다.
