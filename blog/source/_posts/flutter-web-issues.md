---
title: "[오늘의 플러터] 플러터 웹 관련 이슈와 해결"
excerpt: "고급 플러터 기술 정리"
thumbnail: https://miro.medium.com/v2/resize:fit:1358/1*sY-WRAIp3kMkaF9qAucIEg.png
date: 2024-05-19 23:23:36
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
---

## 1. #(해시) 제거

플러터 웹 URL 라우팅 : 해시 방식(기본) / Path 방식
이를 제거하기 위해 

```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_web_plugins:
    sdk: flutter
```

```dart
void main() {
  // setUrlStrategy(PathUrlStrategy());
  usePathUrlStrategy();
  runApp(const MainApp());
}
```

## 2. 라우팅 이슈(내용 추가 예정)

플러터 웹에서는 라우팅 관련 이슈가 정말 많음

1. 브라우저 뒤로가기 / 앞으로가기 기능에서 오동작 발생
2. 새로고침 시 상태 값 사라짐
3. URL 제대로 동작하지 않음
4. 라우팅 간 이펙트가 앱스러움

2 => 전역 상태 관리 해야할 듯
4 => 라우팅 애니메이션 적용할 수 있도록 커스텀 라우터를 작성하거나, 라이브러리 사용

## 3. 로딩 화면(splash)

https://www.filledstacks.com/post/6-tips-to-make-your-flutter-website-feel-premium/

## 4. cors 에러 (오픈 api 사용 시)

cors 허용은 백엔드 서버에서 설정해야 함
오픈 api의 경우 그것이 불가능하므로, 
1) 프록시 서버를 직접 구성하여 서버 -> 서버로 요청을 처리하도록 하거나 
2) cors-proxy 서비스 활용하는 방법이 있다.

관련 서비스 : [https://corsproxy.io/](https://corsproxy.io/)

## 5. url launch

기존 `url_launcher` 패키지 및 `url_launcher_web` 패키지는 제대로 동작하지 않는다.

```dart
import 'dart:html' as html;

/// ...

html.window.open(url, 'new tab');

/// or

import 'dart:js' as js;

/// ...

js.context.callMethod('open', [url]);
```

## 6. Google Analytics

GA 적용법도 플러터 웹 관련해서는 문서화가 제대로 되어 있지 않았다..

1. 패키지 설치(앱과 동일 과정, 대신 `firebase_analytics_web` 설치)

  ```yaml
  dependencies:
    flutter:
      sdk: flutter
    flutter_web_plugins:
      sdk: flutter
    flutter_animate:
    http:
    firebase_analytics_web:
    firebase_core:
  ```

2. `flutterfire` 활용 `firebase` 기본 설정(앱과 동일 과정)

  ```bash
  $ flutterfire configure
  ```

3. 코드 작성

  ```dart
  // main.dart
  void main() async {
    WidgetsFlutterBinding.ensureInitialized();
    await Firebase.initializeApp(
      options: DefaultFirebaseOptions.currentPlatform,
    );
    usePathUrlStrategy();
    runApp(const MainApp());
  }

  class _MainAppState extends State<MainApp> {
    bool _isDarkMode = false;

    static FirebaseAnalyticsWeb analytics = FirebaseAnalyticsWeb();
    ...
    await analytics.logEvent(name: '[+] Search Event with EnterKey');
  }
  ```