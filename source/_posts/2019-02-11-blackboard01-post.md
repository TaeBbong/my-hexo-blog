---
layout: post
title: "01 개발 준비: Selenium / Requests 개발환경 세팅하기"
date: 2019-02-11
excerpt: "나만의 블랙보드 알림 서비스 만들기 with Selenium/Requests, BS4"
category:
  - 개발
  - Web Scraping & Crawling
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

- [01 개발 준비: Selenium / Requests 개발환경 세팅하기](https://TaeBbong.github.io/blackboard01-post)
- [02 블랙보드 게시글 가져와서 출력하기: Selenium 기반 크롤링 엔진 제작](https://TaeBbong.github.io/blackboard02-post)
- [02-1 블랙보드 게시글 가져와서 출력하기: Requests, Session 기반 크롤링 엔진 제작](https://TaeBbong.github.io/blackboard02_1-post)
- [03 블랙보드 신규 글 확인하기: MySQL 데이터베이스 연동](https://TaeBbong.github.io/blackboard03-post)
- [04 블랙보드 신규 글 알림 기능 제공하기: SMTP 메일링 기능 추가](https://TaeBbong.github.io/blackboard04-post)
- [05 정기적으로 작동하게 만들기: Scheduling 기능 추가](https://TaeBbong.github.io/blackboard05-post)
- [여태까지 최종 결과물 소스코드 레포](https://TaeBbong.github.io/blackboardm-post)
- [06 블랙보드 알림 안드로이드 어플리케이션 만들기: 크롤링 엔진을 Django Rest Framework API 서버로 수정, 서버에 Deploy](https://TaeBbong.github.io/blackboard06-post)
- [07 블랙보드 알림 안드로이드 어플리케이션 만들기: Android 앱 개발, API 서버와의 연동](https://TaeBbong.github.io/blackboard07-post)
- [07-1 블랙보드 알림 챗봇 서비스 만들기: 카카오톡 챗봇 개발, API 서버와의 연동](https://TaeBbong.github.io/blackboard07_1-post)
- [블랙보드 알림 안드로이드 어플리케이션 결과물 레포](https://TaeBbong.github.io/blackboardapp-post)
- [블랙보드 알림 챗봇 서비스 결과물 레포](https://TaeBbong.github.io/blackboardchat-post)
- [블랙보드 알림 API 서버 결과물 레포](https://TaeBbong.github.io/blackboardapi-post)

---

## 시작하며

- 안녕하세요, TaeBbong 입니다.
- 오늘은 나만의 블랙보드 알림 서비스 제작 프로젝트의 첫 단계인 개발환경 세팅을 진행하겠습니다.
- 앞서 프로젝트 소개 글에 설명하였듯 시작 단계는 Selenium 기반으로 작성, 이후 Requests를 활용하여 개발하였습니다.
- 이에 각각의 개발환경을 미리 세팅하여 준비하겠습니다.

## 단원 목표

- Selenium 설치, ChromeDriver을 사용할 수 있다.
- Requests 모듈 설치, Session과 함께 Requests를 사용할 수 있다.

## Selenium, 셀레니움이란?

- `Selenium`은 웹 테스트 자동화를 위한 툴입니다.
- 기본적으로는 웹 서비스를 개발하며 테스트를 실제 유저가 하는 것처럼 진행하기 위해 사용합니다.
- 실제 브라우저를 열어 웹 페이지를 탐색하고 버튼을 누르거나 값을 입력하는 등 진짜 유저가 웹을 사용하는 것처럼 만들 수 있어 유용합니다.
- 그러한 특징은 웹 크롤링에도 활용할 수 있어서 프로그램이 실제 유저처럼 웹을 탐색하며 데이터를 수집하도록 제작할 수 있습니다.
- 그로 인해 `requests` 등의 모듈보다 훨씬 쉽고 유동적으로 데이터를 가져올 수 있습니다.
- 우리는 후자의 활용법을 사용해 `Selenium`으로 블랙보드의 데이터를 가져올 수 있도록 할 것입니다.

## 왜 이번 프로젝트에서 Selenium을 사용하나요?

- 물론 실제 브라우저를 작동시킨다는 것에서 알 수 있듯 리소스 사용량이 훨씬 많습니다.
- 그에 따라 속도 저하는 물론 `서버`에 배포하는데 있어 전혀 적합하지 않습니다.
- 게다가 대부분의 서버 OS로 채용되는 `리눅스` 환경에서는 `headless option`(브라우저를 열지 않고 작동하는 모드)을 사용하지 않으면 작동되지 않습니다.
- 그러한 단점들이 분명히 있음에도 `Selenium`을 사용하는 이유는 `로그인` 등의 기능을 간편하게 자동화 할 수 있기 때문입니다.
- `로그인`과 같이 웹을 탐색할 때 제약사항이 되는 부분들은 대부분 `Selenium`으로 극-복 할 수 있습니다.
- 때문에 입문자들은 `Selenium`으로 적절히 웹 크롤링에 대한 개념을 익힐 수 있고, 이후 `session`을 활용한 `requests` 모듈을 사용해 로그인 등의 기능을 직접 개발하여 자동화 할 수 있습니다.

## Selenium 설치하기

- 이제 Selenium을 설치해보겠습니다!

1. Pycharm

- Pycharm 환경에서는 File -> Setting -> Project Intepreter(windows) / Pycharm -> Preference -> Project Intepreter(mac)으로 이동합니다.

![bap1-2](https://taebbong.github.io/assets/img/bap/bap1-2.png)

- 여기서 selenium을 검색, 설치합니다.

![bap1-3](https://taebbong.github.io/assets/img/bap/bap1-3.png)

- 설치가 완료 되었습니다!

2. CLI

- Command Line Interface 환경의 경우 다음 명령어를 터미널에 입력합니다.

```bash
$ pip install selenium
```

- 추가적으로 설치해두면 이후 좋은 라이브러리는 다음과 같습니다.

```
bs4
smtp
```

## ChromeDriver 다운로드

- Selenium이 실제 브라우저를 작동시키기 위해서는 웹 브라우저 드라이버 실행파일이 필요합니다.
- 대표적으로 사용하는 드라이버는 역시 `ChromeDriver`입니다.

![chromedriver](https://taebbong.github.io/assets/img/bap/bap1-8.png)

- 다음 링크에 이동하여 각자의 OS에 맞는 드라이버를 설치합니다.
- [ChromeDriver](http://chromedriver.chromium.org/downloads)
- 다운로드 되었으면, 해당 파일은 본인이 코드에서 접근하기 좋은 주소에 이동시킵니다.
- ex) C:\, /Users/user_name/
- 제가 본 프로젝트에서 드라이버를 저장한 위치는 다음과 같습니다.

```
/Users/TaeBbong/Projects/BlackBoard-Tutorial/
```

## Selenium + ChromeDriver 작동 테스트

- 여기까지 세팅한 내용들이 잘 작동하는지 확인해보겠습니다.
- 프로젝트 폴더에서 Python 파일을 생성합니다.
- 이후 다음과 같은 코드를 작성해봅시다.

**_.../BlackBoard-Tutorial/crawl-demo.py_**

```python
from selenium import webdriver # 모듈 불러오기

driver = webdriver.Chrome('/Users/TaeBbong/Projects/BlackBoard-Tutorial/chromedriver') # 드라이버 설정(Windows는 chromedriver.exe)
driver.implicitly_wait(3) # 3초간 기다려달라(실행 후 준비를 위한 규약적인 시간)
driver.get('https://www.naver.com') # 원하는 URL로 이동하기
```

- 실행하면 다음과 같이 크롬이 열립니다.

![bap1-7](https://taebbong.github.io/assets/img/bap/bap1-7.png)

- 코드는 주석으로 충분한 설명이 되었을거라 믿습니다:)

## 마치며

- 이렇게 이번 시간에는 Selenium을 설치하고 테스트 하는 것까지 하였습니다.
- 다음 포스트부터 본격적으로 프로젝트 개발을 시작하겠습니다:)
