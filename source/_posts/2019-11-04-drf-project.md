---
layout: post
title: "[Django Rest Framework로 게시판 처음부터 끝까지 만들기]"
date: 2019-11-04
excerpt: "Django Rest Framework 마스터"
project: true
tag:
  - development
  - python
  - web
  - django
  - api
  - drf
  - 파이썬
  - 장고

comments: true
---

## 프론트엔드와 백엔드

요즘 웹 개발의 흐름은 `react.js`, `vue.js`의 JS 기반 프론트엔드 프레임워크가 지배하고 있다고 느껴집니다. 웹 개발을 할 때 `react.js`나 `vue.js` 없이 개발하는 사례가 거의 없는거 같아요.

<img src="https://res.cloudinary.com/practicaldev/image/fetch/s--TmOso3iG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AkgvWmTl-lX4G4pA-HoX_cQ.jpeg" width="60%" style="display: block; margin: 0px auto;">

(프론트를 거의 안해봐서 잘 모르겠지만) 워낙 쉽게 모든 걸 할 수 있고, 기존 `jquery`, `js`로 구현하기 어려웠거나 불편했던 것의 해결, 유지 보수 등이 좋아 인기가 많은 것 같습니다.

이러다 보니 웹을 만드는 프로젝트를 할 때(비즈니스 레벨 혹은 학교 팀 프로젝트 레벨이더라도) 프론트엔드 프레임워크를 담당하는 사람(들)이 꼭 있습니다. 짧은 식견으로는 라우팅이나 기존 새로고침을 통해 제공해야 했던 데이터의 변경을 프론트엔드에게 넘겨주고, 백엔드에서는 그저 DB에 접근해 요청에 맞는 데이터를 제공하는 `API 서버`를 개발하도록 된 것으로 생각됩니다.

<img src="https://blog.back4app.com/wp-content/uploads/2019/07/make-app-backend-frontend.png" width="80%" style="display: block; margin: 0px auto;">

이렇게 개발하게 되면 얻을 수 있는 장점은 프론트엔드 개발자와 백엔드 개발자 간 `협업`이 상당히 쉬워진다는 것입니다. 프론트엔드 개발자는 UI나 로직을 구현하면 되고, 필요한 데이터가 발생한다면 백엔드 개발자가 만들어놓은 API를 가져다 쓰면 됩니다. 혼자 개발을 한다면 이 둘을 굳이 분리할 필요가 없겠지만, 개발자 둘이 한 몸이 되지 않는 이상 코드 공유부터 수정, 테스트까지 정말 불편한 일들이 많을 것입니다.

특히 백엔드의 `API 서버`는 단지 웹에만 적용되지 않고, 앱이나 여러 http 프로토콜로 통신하는 프로젝트에 그대로 적용될 수 있어 활용성이 정말 좋습니다. `REST API`가 API 개발에서 제일 대표적인 방법이며, 이에 대한 설명은 다른 분들이 더 잘해주셔서 이를 첨부하겠습니다.

- [Redhat - API란?](https://www.redhat.com/ko/topics/api/what-are-application-programming-interfaces)
- [REST API 제대로 사용하기](https://meetup.toast.com/posts/92)

## 왜 Django Rest Framework??

`REST API`를 개발하기 위한 프레임워크는 정말 다양합니다. 최근 정말 인기 있는 `node.js`의 `express`, `python`의 `flask`, `java`의 `spring boot`까지.

<img src="https://vuejsdevelopers.com/images/posts/backends.jpg" width="70%" style="display: block; margin: 0px auto;">

정말 다양한 언어로 개발된 프레임워크들이 있고 모든 분야가 그렇듯 다 경험 해보고 본인이 쓰기 편한 것으로 개발하시면 됩니다. 저 또한 `express`, `flask`, 그리고 `Django Rest Framework` 이렇게 세 종류를 경험해보았고 이 중에서 `Django Rest Framework`를 주 활용 스택으로 선택한 이유는 다음과 같습니다.

1. Django!

- Django의 가장 큰 장점은 `"Django스럽다"` 라고 말할 수 있습니다. 이게 무슨 뜻이냐 하면, 개발해야하는 여러 대표적인 기능들이 거의 모두 이미 개발되었으니 그냥 가져다 쓰면 된다는 것입니다. 처음엔 오히려 이것저것 복잡해서 낯설고 친해지는데 오래 걸릴 수 있지만, 나중에는 이미 구현된 이 기능 하나하나에 정말 큰 고마움을 느낄 것입니다.

2. Django의 강력한 ORM

- Django를 사용하다보면 내가 데이터베이스를 쓰고 있던가 싶을 정도로 개발자가 쓰기 좋게 잘 추상화 되어있습니다. 상당히 `python` 스럽고 직관적인 방법으로 데이터에 접근, 필터링할 수 있으며, 다른 프레임워크로는 얻을 수 없는 경험이라고 생각됩니다.

## 프로젝트 소개

본 프로젝트에서는 `Django Rest Framework`를 활용하여 게시판을 만들어보겠습니다. 게시판을 만들면서 당연히 `CRUD`를 구현하게 될 것이고, 이 과정을 시작하면서 최대한 다양한 방법을 제시하고 그 중에서 한 가지 방법으로 통일하여 쭉 작업할 예정입니다.

또한 항상 프로젝트의 시작이 되면서 은근 귀찮고 어려운 `회원 가입 / 로그인 인증`도 개발할 예정입니다. 당연히 코드도 깃헙을 통해 공유드립니다. 기왕이면 해당 프로젝트 하나만 쭉 따라하면서 공부해도 `Django Rest Framework`를 여러분들이 마스터 할 수 있기를 바랍니다:)

## 포스트 링크

- [01 Django Rest Framework 개발 환경 세팅](https://taebbong.github.io/drf-1-post)
- [02 Django Rest Framework, Serializer, View 개념 익히기](https://taebbong.github.io/drf-2-post)
- [03 DRF로 회원 가입 / 인증 구현하기, 유저 모델 확장하기](https://taebbong.github.io/drf-3-post)
