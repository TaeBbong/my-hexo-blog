---
title: "[플러터 팁 백과사전] 002. 깔끔한 라우팅"
excerpt: "라우트를 활용해 화면 간 이동을 깔끔하게 하는 방법을 알아봅시다."
thumbnail: https://taebbong.github.io/images/blog/splash1.png
date: 2021-01-17 20:23:36
tags:
category:
  - 개발
  - Flutter
toc: true
widgets:
  - type: toc
    position: left
  - type: category
    position: left
sidebar:
  left:
    sticky: true
head:
  canonical_url: https://taebbong.github.io
  open_graph:
    image: https://taebbong.github.io/images/blog/splash1.png
---

## Flutter의 라우팅

Flutter에서는 페이지 간 이동을 할 때 Navigator를 활용하여 진행합니다. 아주 기본적인 화면 전환 코드는 아래와 같습니다.
(코드)
이렇게 진행하는 것도 문제 없이 잘 되지만, 코드 작성 시 살짝 불편하기도 하고 중복 코드가 너무 많이 생겨난다는 아쉬운 점이 있습니다. 이를 해결하기 위해 Named route라는 방법으로 화면 전환을 구현할 수 있습니다.

## Named route?

SplashScreen에도 종류가 있습니다. 안드로이드/iOS 각 OS의 Native한 SplashScreen과, Flutter App 단의 SplashScreen 이렇게 두개로 나눠지게 됩니다. 만약 기존에 Flutter 단에서 SplashScreen을 잘 만들고 시행했을 때 검은색 혹은 흰색의 기본 화면이 잠시 나오다가 Flutter SplashScreen이 나오는 경험을 하신 적 있다면, 그것이 두 종류의 SplashScreen을 확인한 것입니다.

<img src="/images/blog/splash2.gif" style="border: 1px" width="300">

## Named route 구현 1단계: main.dart 라우팅 선언하기

Flutter level의 SplashScreen을 적용시킬 때에는 특별히 패키지를 가져올 필요는 없습니다. 공개된 패키지가 여럿 있지만, 굳이 없어도 간단히 구현할 수 있습니다. 그냥 로고를 띄워주고 시간이 되거나 데이터를 다 불러왔을 때 메인 화면으로 이동만 시키면 되니까요. 제가 작성한 예시는 아래와 같습니다. 일정 시간이 지나면 메인 화면으로 이동하도록 작성해보았습니다.

```dart
// main.dart
import 'package:flutter/material.dart';
import 'dart:async';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'MY MEMO',
      home: SplashScreen(),
    );
  }
}

class SplashScreen extends StatefulWidget {
  @override
  _SplashScreenState createState() => new _SplashScreenState();
}

class _SplashScreenState extends State<SplashScreen> {
//   Future checkFirstSeen() async {
//     SharedPreferences prefs = await SharedPreferences.getInstance();
//     bool _seen = (prefs.getBool('seen') ?? false);

//     if (_seen) {
//       Navigator.of(context).pushReplacement(
//           MaterialPageRoute(builder: (context) => ChatScreen()));
//     } else {
//       await prefs.setBool('seen', true);
//       Navigator.of(context).pushReplacement(
//           MaterialPageRoute(builder: (context) => OnBoardingPage()));
//     }
//   }

  @override
  void initState() {
    super.initState();
    Timer(Duration(milliseconds: 1500), () {
//       checkFirstSeen();
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        color: Color(0xff28385e),
        child: Center(
          child: Image.asset('images/my_logo.png'),
        ),
      ),
    );
  }
}
```

여기에 실제로 데이터를 가져오는 코드를 넣고 싶다면 주석 부분에 코드를 넣으면 됩니다. 물론 시간이 걸리는 코드는 비동기로 작성되어야 합니다. 제가 작성한 주석 부분은 SharedPreference에서 인트로 화면을 본 적이 있는 사용자인지 체크해서 본 적 있으면 바로 메인으로 넘어가고 본 적이 없으면 인트로 화면을 보여주는 코드입니다.

## Native level SplashScreen 만들기

OS Native SplashScreen은 각 OS 별 폴더 내에 있는 파일들을 수정해야 적용됩니다. 이 작업은 꽤 귀찮고 Native가 낯설다면 조금 거부감이 들 수 있기 때문에, 공식 가이드 링크만 첨부하고 저는 다른 방식으로 Native SplashScreen 만드는 것을 알려드리겠습니다.

flutter_native_splash라는 패키지가 있습니다. 이는 다른 Splash Screen 관련 패키지와 달리 Native 쪽 설정 코드를 생성하여 직접 적용까지 시켜주는 패키지입니다. 이 때문에 일반적인 패키지와 조금 다른 적용법이 필요합니다.

1. 패키지 설치
   ```yaml
   // pubspec.yaml
   dev_dependencies:
     flutter_native_splash:
   ```
2. 로고 등록
   ```yaml
   // flutter_native_splash.yaml
   flutter_native_splash:
     image: images/my_logo.png
     color: ffffff
   ```
3. Splash create
   ```bash
   $ flutter pub pub run flutter_native_splash:create
   ```
4. Run app

실행하면 아래와 같이 나오게 됩니다.

<img src="/images/blog/splash3.gif" style="border: 1px" width="300">

## 마치며

SplashScreen에 대해 깊이 정리해보았습니다. 앱에서 필수적인 요소인만큼 요긴하게 잘 활용하면 좋겠습니다.
