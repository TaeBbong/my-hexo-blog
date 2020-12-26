---
title: [플러터 핵심 팁 백과사전] 001. SplashScreen 만들기
date: 2020-12-19 20:23:36
tags:
category:
  - 개발
  - Flutter
---

그럴듯한 앱을 만들기 위한 가장 첫 단계는 SplashScreen 입니다. 앱을 시작할 때 로고와 함께 유저를 반기는 첫 화면인 SplashScreen을 아래와 같이 간단하게 만들어볼 수 있습니다.

## 1. SplashScreen??
SplashScreen은 앱이 실행될 때 처음 나타나게 되는 일종의 소개 화면입니다. 주로 앱 로고 등을 넣어 사용자에게 어떤 앱인지 알려주는 용도로 사용합니다. 또한 사용자에게는 안보이지만, 앱에서 시간을 들여 가져와야 하는 데이터를 가져올 때 SplashScreen이 보여지는 동안 가져오기도 합니다. 

## 2. SplashScreen의 종류
SplashScreen에도 종류가 있습니다. 안드로이드/iOS 각 OS의 Native한 SplashScreen과, Flutter App 단의 SplashScreen 이렇게 두개로 나눠지게 됩니다. 만약 기존에 Flutter 단에서 SplashScreen을 잘 만들고 시행했을 때 검은색 혹은 흰색의 기본 화면이 잠시 나오다가 Flutter SplashScreen이 나오는 경험을 하신 적 있다면, 그것이 두 종류의 SplashScreen을 확인한 것입니다. 

## 3. Flutter level SplashScreen 만들기
Flutter level의 SplashScreen을 적용시킬 때에는 특별히 패키지를 가져올 필요는 없습니다. 공개된 패키지가 여럿 있지만, 굳이 없어도 간단히 구현할 수 있습니다. 그냥 로고를 띄워주고 시간이 되거나 데이터를 다 불러왔을 때 메인 화면으로 이동만 시키면 되니까요. 제가 작성한 예시는 아래와 같습니다. 일정 시간이 지나면 메인 화면으로 이동하도록 작성해보았습니다. 

여기에 실제로 데이터를 가져오는 코드를 넣고 싶다면 아래 부분에 코드를 넣으면 됩니다. 물론 시간이 걸리는 코드는 비동기로 작성되어야 합니다.

## 4. Native level SplashScreen 만들기
OS Native SplashScreen은 각 OS 별 폴더 내에 있는 파일들을 수정해야 적용됩니다. 이 작업은 꽤 귀찮고 Native가 낯설다면 조금 거부감이 들 수 있기 때문에, 공식 가이드 링크만 첨부하고 저는 다른 방식으로 Native SplashScreen 만드는 것을 알려드리겠습니다. 

flutter_native_splash라는 패키지가 있습니다. 이는 다른 Splash Screen 관련 패키지와 달리 Native 쪽 설정 코드를 생성하여 직접 적용까지 시켜주는 패키지입니다. 이 때문에 일반적인 패키지와 조금 다른 적용법이 필요합니다. 

1. 패키지 설치
2. 코드 작성(splash screen)
3. Splash create
4. Run app

## 5. 마치며
SplashScreen에 대해 깊이 정리해보았습니다. 앱에서 필수적인 요소인만큼 요긴하게 잘 활용하면 좋겠습니다.