---
layout: post
title: "03 블랙보드 신규 글 확인하기: MySQL 데이터베이스 연동"
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

## MySQL 모듈 사용하기

- 이번에는 앞서 만든 프로그램에 데이터베이스를 연결해보겠습니다.
- 데이터베이스를 연결하지 않으면 앞서 가져왔던 데이터 모두가 출력되게 됩니다.
- 따라서 데이터베이스에 모든 글들을 저장해놓고, 가져오는 글이 데이터베이스에 없을 경우에만 출력/메일로 전송하도록 하면 됩니다.
- 그런 후에 데이터베이스에 글을 저장하면 되는거죠!
- Python에는 `MySQL`을 사용할 수 있는 라이브러리가 존재합니다.
- 코드를 작성하기 전에 우선 데이터베이스를 설치, 세팅해야합니다.
- MySQL 설치/세팅/SQL 문법 등은 이 포스트에서 다루지 않겠습니다.
- 설치/세팅이 다 되었다면, 다음과 같이 mysql console을 활성화시킵니다.

```bash
$ where mysql
>>> usr/local/bin/mysql
$ cd usr/local/bin
$ ./mysql -u root -p
>>> Enter password: <type your password>
mysql>
```

## 데이터베이스/테이블 생성

- 이제 SQL 문을 작성해볼게요. 우리는 `bb`라는 데이터베이스를 생성하고, 이 데이터베이스에 `announce` 와 `homework` 라는 테이블을 선언하겠습니다.
- 두 테이블 모두 간단하게 `post_id` 만 저장할건데요, 그래서 SQL 문은 다음과 같이 간단해집니다.

```bash
mysql> CREATE DATABASE bb;
mysql> USE bb;
mysql> CREATE TABLE announce (id varchar(20));
mysql> CREATE TABLE homework (id varchar(20));
```

- 또한 pip / pycharm으로 pymysql을 설치해봅시다.

```bash
$ pip install pymysql
```

- 여기까지 문제 없이 진행되었다면 MySQL에서 다룰 부분은 끝입니다.

## Python - MYSQL Adapter 코드 작성

- 이제는 Python에서 MySQL을 가져다 쓰는 코드를 작성할 차례입니다.
- 코드로 바로 확인해보겠습니다.

**_.../BlackBoard-Tutorial/crawl-mysql.py_**

```python
import pymysql

conn = pymysql.connect(host='localhost', user='root', password='PASSWORD', db='bb', charset='utf8') # MYSQL connect
curs = conn.cursor() # MYSQL 커서(탐색 도구)
```

- 위와 같이 작성하면 이제 Python에서 아까 만든 데이터베이스로의 접근이 자유로워집니다.
- 그럼 이제 bb 데이터베이스에 있는 announce와 homework를 모두 가져와 리스트로 저장해두겠습니다.
- 나중에 가져오는 포스트들이 이 리스트에 저장되어있는지 아닌지를 확인하며 작업할 예정입니다.

**_.../BlackBoard-Tutorial/crawl-mysql.py_**

```python
announce_db = []
homework_db = []

sql = "select * from announcement"
curs.execute(sql)
rows = curs.fetchall()
for a in rows:
    announce_db.append(a[0])

sql = "select * from homework"
curs.execute(sql)
rows = curs.fetchall()
for h in rows:
    homework_db.append(h[0])
```

- 이제 데이터베이스에 있는 모든 `post_id`들이 리스트에 저장되었습니다.
- 그럼 post를 가져올 때마다 이 리스트에 있는지 확인을 해야겠죠?
- **확인해서 이미 있으면 pass, 없으면 출력/메일로 전송 단계를 거치고 데이터베이스로 insert 해야합니다.**

**_.../BlackBoard-Tutorial/crawl-mysql.py_**

```python
for ann in announcements:
    if ann.attrs['id'] not in announce_db:
        sql_ann = 'insert into announcement values(\"' + ann.attrs['id'] + '\")'
        curs.execute(sql_ann)
        conn.commit()

        print(ann.attrs['id'])
        print(ann.text)
        print('---------------')
```

- homework도 마찬가지로 진행하면 돼요!

- 전체 코드는 [crawl-mysql.py](https://github.com/TaeBbong/BlackBoard-Tutorial) 에서 확인하실 수 있어요!

## 마치며

- 이번 시간을 통해 우리는 Python으로 MySQL을 제어하고, 이를 활용해 데이터베이스에 없는 포스트만 출력하도록 코드를 작성하였습니다.
- 데이터베이스에 대해 기본적인 내용만 알고 있으면 크게 어려운 내용은 아니었어요:)
- 이제는 데이터베이스가 연결되어있으니, 새로운 글이 올라올 때마다 print() 대신 내 메일로 보내주는 코드를 작성해야합니다.
- 다음시간엔 `SMTP`를 활용해 메일 보내기 코드를 작성해보겠습니다.
