---
title: "[개발] 앱 개발자 키 & 앱 서명에 관하여"
excerpt: "내가 보고 쓰려는 앱 개발자 키 내용 정리"
thumbnail: img/images/blog/flutter-logo-sharing.png
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

[](https://jw910911.tistory.com/53)

안드로이드 디버그 키 해시

keytool -exportcert -alias androiddebugkey -keystore ./key.jks | openssl sha1 -binary | openssl base64


### 1. 안드로이드

[](https://developer.android.com/studio/publish/app-signing?hl=ko)

안드로이드 앱은 구버전 방식과 신버전 방식(2019년 이후)으로 나뉜다. 차이점은 앱 번들(.aab)이 공식 배포 포맷으로 변경되면서 발생한 것으로, APK가 압축파일 형태의 앱이라면 AAB는 기기마다 최적화된 APK 생성을 위한 번들 파일이다. 이 방식으로 바뀌면서 서명 방식도 변경되었다.

#### 구버전

![](https://developer.android.com/studio/img/images/publish/appsigning_selfmanagediagram_2x.png?hl=ko)

앱 서명키 저장소는 서명키(비밀키)와 인증서(공개키)로 구성된다. 처음 키를 생성하면 서명키(비밀키)와 인증서(공개키) 쌍이 생성되는 것이다. 기본적인 서명 알고리즘과 동일하다.

```
1. 개발자는 서명키(비밀키)와 인증서(공개키) 쌍을 생성한다.
2. 그 중 서명키(비밀키)로 앱에 서명한다.
3. 플레이스토어에 인증서(공개키)를 업로드한다.
4. 플레이스토어에 서명된 앱을 업로드한다.
5. 플레이스토어에서는 인증서(공개키)로 서명된 앱을 검증한다.
6. 업데이트가 생겼을 때 개발자는 역시 서명키(비밀키)로 앱에 서명한다.
7. 플레이스토어에서는 예전에 올린 인증서(공개키)로 역시 서명된 앱을 검증한다.
```

이 방식은 개발자가 직접 서명키를 관리하는 방식이다. 키를 분실하면 다시는 해당 ID의 앱을 업데이트할 수 없다.

#### 신버전(앱 서명 키 + 앱 업로드 키)

![](https://developer.android.com/studio/img/images/publish/appsigning_googleplayappsigningdiagram_2x.png?hl=ko)

2021년부터 공식 방법으로 채택되었기 때문에 사실 이것만 알고 있으면 된다. 안드로이드 공식 문서에도 해당 내용을 먼저, 주로 설명하고 있다.
기존 방식에서 앱 업로드 키 개념이 추가되었다.

```
1. 개발자는 서명키(비밀키)와 서명 인증서(공개키) 쌍을 생성한다.
2. 플레이스토어에 서명키(비밀키)와 서명 인증서(공개키)를 업로드한다. => 관리는 이제 플레이스토어가 한다.
3. 개발자는 업로드키(비밀키)와 업로드 인증서(공개키) 쌍을 생성한다.
4. 플레이스토어에 업로드 인증서(공개키)를 업로드한다.
5. 앱 번들에 업로드키(비밀키)로 서명한다.
6. 플레이스토어에 서명된 앱 번들을 올린다.
7. 플레이스토어는 서명키(비밀키)로 앱에 서명하여 유저에게 배포한다.
```

안드로이드 개발자가 생성하는 키는 .jks 또는 .keystore 확장자를 갖는다.
안드로이드 스튜디오로 생성하면 .jks 파일이 생성되며, keytool로 생성하면 .keystore 파일이 생성되는 것이다.
이 파일들은 앱 서명 키가 담긴 파일로, 나중에 배포할 때 이 키 파일을 활용해 앱에 서명을 하면 된다.

#### 안드로이드 스튜디오 활용 키 생성(.jks)

#### keytool 활용 키 생성(.keystore)

=> keystore 비밀번호와 key alias 비밀번호가 같으면 에러 발생

[](https://medium.com/flutter-community/flutter-sign-in-with-google-in-android-without-firebase-a91b977d166f)

[](https://alaveiw.tistory.com/145)

### 2. iOS

#### iOS 키체인 생성

https://eunjin3786.tistory.com/295