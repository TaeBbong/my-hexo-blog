---
layout: post
title:  "05 정기적으로 작동하게 만들기: Scheduling 기능 추가"
date:   2019-02-13
excerpt: "나만의 블랙보드 알림 서비스 만들기 with Selenium/Requests, BS4"
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


## 게시글 링크
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

---


## Scheduling 모듈 사용하기
* 이제는 주기적으로 내 코드가 반복되게끔 작성하면 됩니다.
* 반복의 경우 Scheduling을 사용하면 쉽게 해결할 수 있습니다.
* 우선 반복할 함수가 필요하므로 여태까지의 코드를 모두 함수로 묶습니다.
* 이후 다음 코드를 입력하여 Scheduling을 구현합니다.

***.../BlackBoard-Tutorial/crawl-complete.py***
~~~python
from apscheduler.schedulers.background import BlockingScheduler

print('Scheduler start...')
sched = BlockingScheduler()
sched.add_job(main_func(), 'interval', hours=12)
sched.start()
~~~

* 전체 코드는 [crawl-complete.py](https://github.com/TaeBbong/BlackBoard-Tutorial) 에서 확인하실 수 있어요!


## 마치며
* 드디어 모든 기능 작성이 완료되었습니다. 개발 완료!!

![img](https://taebbong.github.io/assets/img/bap/bap5-1.png)

* 이제 잠시 쉬었다가, 실제 서비스를 개발 해볼까 해요.
* 앞서 아주 간단하게 말하긴 했지만, 우리가 여태까지 만든 프로젝트는 실제 서비스가 되기엔 많이 부족합니다.
* Selenium 자체적인 리소스 문제와 드라이버의 호환 등 아쉬운 점들이 있습니다.
* 그래서 우리는 이걸 Requests / Session 기반으로 조금 고쳐서 작성해야 합니다. 
* 이는 천천히 수정해볼 예정이에요.
* 실제 서비스는 크게 두가지로 생각하고 있는데, 안드로이드 어플리케이션 혹은 챗봇 쪽으로 생각중입니다.
* 끝까지 읽어주셔서 감사합니다. 그럼 다들 즐거운 개발! 언제든 피드백 남겨주세요:)


