---
layout: post
title: "04 블랙보드 신규 글 알림 기능 제공하기: SMTP 메일링 기능 추가"
date: 2019-02-12
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
- [여태까지 최종 결과물 소스코드 레포](https://github.com/TaeBbong/BlackBoard-Tutorial)
- [06 블랙보드 알림 안드로이드 어플리케이션 만들기: 크롤링 엔진을 Django Rest Framework API 서버로 수정, 서버에 Deploy](https://TaeBbong.github.io/blackboard06-post)
- [07 블랙보드 알림 안드로이드 어플리케이션 만들기: Android 앱 개발, API 서버와의 연동](https://TaeBbong.github.io/blackboard07-post)
- [07-1 블랙보드 알림 챗봇 서비스 만들기: 카카오톡 챗봇 개발, API 서버와의 연동](https://TaeBbong.github.io/blackboard07_1-post)
- [블랙보드 알림 안드로이드 어플리케이션 결과물 레포](https://TaeBbong.github.io/blackboardapp-post)
- [블랙보드 알림 챗봇 서비스 결과물 레포](https://TaeBbong.github.io/blackboardchat-post)
- [블랙보드 알림 API 서버 결과물 레포](https://TaeBbong.github.io/blackboardapi-post)

---

## SMTP 모듈 사용하기

- 이번에는 앞서 만든 프로그램에 메일 보내기 기능을 추가해보겠습니다.
- 메일은 몇가지 라이브러리를 활용해 보내도록 만들 수 있고, 우리는 여기서 간단한 `SMTP` 라이브러리를 활용하여 진행해보겠습니다.
- `SMTP`는 네트워크 수업 때 배우는 프로토콜의 종류 중 하나인데, 메일 송수신에 관한 규약을 정리한 것입니다.
- 우리는 이 프로토콜을 Python에 맞게 제작한 기본 내장 라이브러리 `smtplib`를 활용할 예정입니다.
- 코드는 다음과 같아요.

**_.../BlackBoard-Tutorial/crawl-smtp.py_**

```python
import smtplib
from email.mime.text import MIMEText

# Init Mail Service
smtp = smtplib.SMTP('smtp.gmail.com', 587)
smtp.ehlo()
smtp.starttls()
smtp.login('GMAIL_ID', 'GMAIL_PW')
```

- 위 코드를 통해 메일을 보내기 위한 세팅이 완료되었습니다.

## 새로운 글에 대한 메일 보내기 코드 작성

- 이제는 새로운 글을 찾을 때마다 메일을 보내면 됩니다!

**_.../BlackBoard-Tutorial/crawl-smtp.py_**

```python
# 공지 가져오기
announce_raw = driver.page_source
soup = bs(announce_raw, 'html.parser')
announcements = soup.select('li.clearfix')[10:]
announcements.reverse()
for ann in announcements:
    if ann.attrs['id'] not in announce_db:
         sql_ann = 'insert into announcement values(\"' + ann.attrs['id'] + '\")'
         curs.execute(sql_ann)
         conn.commit()
         msg = MIMEText(ann.text)
         msg['Subject'] = 'Announcement for ' + i[0].split('&course_id=')[1]
         msg['To'] = get_target_mail()
         smtp.sendmail(get_gmail_id(), get_target_mail(), msg.as_string())
smtp.quit()
```

- Homework도 마찬가지로 해주세요!
- 전체 코드는 [crawl-smtp.py](https://github.com/TaeBbong/BlackBoard-Tutorial) 에서 확인하실 수 있어요!

## 마치며

- 와 드디어 메일까지 보내주네요!
- 거의 모든 기능이 완료되었습니다:)
- 마지막으로 해볼건 Scheduling Code 작성입니다.
- 정기적으로 몇시간에 한번씩 코드가 돌아가도록 코드를 작성해볼게요:)
