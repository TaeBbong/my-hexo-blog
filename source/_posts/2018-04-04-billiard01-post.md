---
layout: post
title:  "[Node.js] 당구치실 분 01 - 프로젝트 생성하기"
date:   2018-04-04
excerpt: "Node.js와 Mysql을 활용한 프로젝트"
tag: 
- node.js
- mysql
- DB
- 당구치실분
- 프로젝트

comments: true
---

## Remind: 프로젝트 개요
* 데이터베이스를 활용한 Toy Project
* 당구와 같은 취미는 혼자서는 할 수 없는 취미
* 이에 본인과 같이 친구가 적은 사람들이 자신에게 맞는 당구 파티를 짜게 하는 것이 목표
* 웹 사이트에서 방을 만들어서 연락 후 오프라인에서 만나기
* 수익 모델은 사이트 트래픽이 많아지면 광고를 붙일 예정


## Remind: 프로젝트 사용 기술
* 데이터베이스: Mysql
* 언어: Node.js
* 프레임워크: Express Framework
* 서빙: 일단은 로컬, 이후 고민 예정
* 프론트엔드: Html, CSS, ejs

## Step 1: 프로젝트 생성하기
* 우리는 Node.js의 Express Framework를 활용하여 프로젝트를 생성할 것이다.
* Express Framework란 Node.js 상에서 동작하는 웹 개발 Framework이다.
* 이는 다양한 Middleware의 활용을 통해 코드를 간결하고 가볍게 작성할 수 있다는 장점이 있다.
![Node.js Express Framework](https://www.brainvire.com/wp-content/uploads/Express.JS-%E2%80%93-An-Ideal-Node.JS-Framework-to-develop-Enterprise-Web-Applications.jpg)
* 우선 Express을 설치하여야 한다.
```bash
$ npm install express -g
```
* 다음 코드를 통해 프로젝트를 생성한다.
```bash
$ express billiard(My Project Name)
```
* 그러면 다음과 같이 폴더가 생성되며 내부는 다음과 같다.
![Express APP](https://taebbong.github.io/assets/img/billiard01_01.png)

## Step 2: 생성된 프로젝트 파일 구조 이해하기
* 위에서 생성된 프로젝트의 각 구성 요소를 하나하나 이해해보면 다음과 같다.
* app.js
	![app.js](https://taebbong.github.io/assets/img/billiard01_02.png)
	* 프로젝트의 main 파일이라고 생각
	* 모듈 불러오기 등 전반적 코드 대부분이 들어간다.
* public
	![public](https://taebbong.github.io/assets/img/billiard01_03.png)
	* resource 파일들이 들어가있다.
	* 프로젝트에 사용되는 이미지, CSS 스타일 등을 이 곳에 저장하여 사용한다.
* routes
	![routes](https://taebbong.github.io/assets/img/billiard01_04.png)
	* 페이지 라우팅 처리를 관리하는 소스가 저장되어있다.
	* 이 폴더를 통해 라우팅 처리를 모듈화 시켜 관리할 수도 있고, app.js에서 모든 라우팅을 처리해주어도 상관은 없다.
* views
	![views](https://taebbong.github.io/assets/img/billiard01_05.png)
	* 페이지 템플릿이 저장되어있다.
	* 기본 템플릿은 jade 방식으로 우리는 추후 ejs를 통해 관리할 예정이다.