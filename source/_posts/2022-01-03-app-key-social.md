---
title: "[개발] 앱 개발자 키 & 앱 서명에 관하여"
excerpt: "내가 보고 쓰려는 앱 개발자 키 내용 정리"
thumbnail: https://taebbong.github.io/images/blog/flutter-logo-sharing.png
date: 2022-01-03 20:23:36
tags:
category:
  - 개발
  - 개발 Tip
toc: true
widgets:
  - type: toc
    position: left
  - type: category
    position: left
sidebar:
  left:
    sticky: true
---

## 앱 서명키 & Signing

어떤 개발자가 개발한 앱인지 인증하기 위한 방법으로, 개발자가 만들어놓은 앱 서명키를 활용해 앱에다가 서명을 하는 것이다.
앱에 서명을 하여, 나중에 해당 앱에 대한 업데이트를 진행할 때 원본 앱 개발자의 업데이트가 맞는지 확인하기 위해 필수적이다.


### 1. 안드로이드

안드로이드 앱은 구버전 방식과 신버전 방식(2019년 이후)으로 나뉜다. 차이점은 앱 번들(.aab)이 공식 배포 포맷으로 변경되면서 발생한 것으로, APK가 압축파일 형태의 앱이라면 AAB는 기기마다 최적화된 APK 생성을 위한 번들 파일이다. 이 방식으로 바뀌면서 서명 방식도 변경되었다.

#### 구버전

![]()

#### 신버전(앱 업데이트 키)

안드로이드 개발자가 생성하는 키는 .jks 또는 .keystore 확장자를 갖는다.
안드로이드 스튜디오로 생성하면 .jks 파일이 생성되며, keytool로 생성하면 .keystore 파일이 생성되는 것이다.
이 파일들은 앱 서명 키가 담긴 파일로, 나중에 배포할 때 이 키 파일을 활용해 앱에 서명을 하면 된다.

#### 안드로이드 스튜디오 활용 키 생성(.jks)

#### keytool 활용 키 생성(.keystore)

### 2. iOS

#### iOS 키체인 생성
