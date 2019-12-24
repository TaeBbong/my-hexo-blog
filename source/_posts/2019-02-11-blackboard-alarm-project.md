---
layout: post
title:  "나만의 블랙보드 알림 서비스 만들기 with Selenium/Requests, BS4"
date:   2019-02-11
excerpt: "Selenium/Requests 활용 크롤링/메일 서비스 개발"
project: true
tag:
- development
- python
- web
- crawling
- selenium
- bs4
- requests
- session

comments: true
---

 
## 프로젝트 개요
* 고려대학교 블랙보드 알림 서비스 만들기
* 블랙보드에 새로운 공지/과제가 올라오면 사용자 ```메일```로 전송해주기
* ```DB```를 통한 신규 글 확인
* 초기에는 ```Selenium``` 기반으로 크롤링, 이후 ```Requests with Session```으로 개선


## 프로젝트 세부사항
* 언어: ```Python```
* 크롤링: ```Selenium```/```Requests```
* 파싱: ```BS4```(html.parser)
* 서빙: 일단은 로컬 서버에서 개인을 위해 작동, 이후 앱, 챗봇 서비스로 확장 예정
* 메일: ```SMTP``` Gmail
* 디비: ```MySQL```

  
## 프로젝트 링크
* [01 개발 준비: Selenium / Requests 개발환경 세팅하기](https://TaeBbong.github.io/blackboard01-post)
* [02 블랙보드 게시글 가져와서 출력하기: Selenium 기반 크롤링 엔진 제작](https://TaeBbong.github.io/blackboard02-post)
* [02-1 블랙보드 게시글 가져와서 출력하기: Requests, Session 기반 크롤링 엔진 제작](https://TaeBbong.github.io/blackboard02_1-post)
* [03 블랙보드 신규 글 확인하기: MySQL 데이터베이스 연동](https://TaeBbong.github.io/blackboard03-post)
* [04 블랙보드 신규 글 알림 기능 제공하기: SMTP 메일링 기능 추가](https://TaeBbong.github.io/blackboard04-post) 
* [05 정기적으로 작동하게 만들기: Scheduling 기능 추가](https://TaeBbong.github.io/blackboard05-post)
* [여태까지 최종 결과물 소스코드 레포](https://github.com/TaeBbong/BlackBoard-Tutorial)
* [06 블랙보드 알림 안드로이드 어플리케이션 만들기: 크롤링 엔진을 Django Rest Framework API 서버로 수정, 서버에 Deploy](https://TaeBbong.github.io/blackboard06-post)
* [07 블랙보드 알림 안드로이드 어플리케이션 만들기: Android 앱 개발, API 서버와의 연동](https://TaeBbong.github.io/blackboard07-post)
* [07-1 블랙보드 알림 챗봇 서비스 만들기: 카카오톡 챗봇 개발, API 서버와의 연동](https://TaeBbong.github.io/blackboard07_1-post)
* [블랙보드 알림 안드로이드 어플리케이션 결과물 레포](https://TaeBbong.github.io/blackboardapp-post)
* [블랙보드 알림 챗봇 서비스 결과물 레포](https://TaeBbong.github.io/blackboardchat-post)
* [블랙보드 알림 API 서버 결과물 레포](https://TaeBbong.github.io/blackboardapi-post)
