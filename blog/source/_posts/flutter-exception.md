---
title: "[오늘의 플러터] 플러터 assert, try, catch, throw, finally로 예외 처리하기"
excerpt: "고급 플러터 기술 정리"
thumbnail: https://miro.medium.com/v2/resize:fit:1358/1*sY-WRAIp3kMkaF9qAucIEg.png
date: 2023-10-05 23:23:36
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

## 플러터의 예외 처리

문득 아주 예전에 대선배님이 작성하셨던 플러터 프로젝트 코드가 기억나면서, assert라는 예외처리 구문이 생각났다.
이후 개발 프로젝트에서도 적절히 활용할 수 있을 것 같아 다시 공부하면서 예외처리 기법들을 정리해보고자 한다.

## assert

예외처리 방법 중 하나로, 디버깅 중에서만 적용되는 예외처리 기술이다.
디버깅 중에서만 적용된다는 것은 말그대로 릴리즈 버전에서는 동작하지 않는 예외처리 구문이라는 것이다.
어차피 우리는 개발 중에 디버깅을 활용하니, 적절히 잘 사용할 수 있겠다.

assert는 다음과 같은 문법으로 작성한다.

```dart
assert(condition, optionalMessage);
```

조건문인 condition의 결과가 거짓이면 콘솔에는 optionalMessage를 출력하고 그 즉시 실행이 중단된다.
이후 나오는 기본 예외처리 기법인 try-catch보다 간단하게 조건 처리를 할 수 있어 개발 간 자주 활용될 것 같다.

## try catch finally

이는 이미 너무 다양한 언어에서 사용되고 있어 특별히 다룰 건 없지만, 코드만 보면

```dart
try {
    something();
} on SpecialException { // 특정 Exception만 catch
    doSomething();
} on Exception catch (e) { // Exception 타입의 에러를 잡고 이를 e라는 객체로 컨트롤
    doOtherthing();
} catch (e) { // 그 외 모든 예외를 e라는 객체로 컨트롤
    doOtherStuff();
} finally { // 에러 발생 유무와 관계 없이 수행
    done();
}
```

위와 같이 정리할 수 있겠다.