---
title: "[오늘의 플러터] 플러터와 다트에서의 static 영역"
excerpt: "고급 플러터 기술 정리"
thumbnail: https://miro.medium.com/v2/resize:fit:1358/1*sY-WRAIp3kMkaF9qAucIEg.png
date: 2023-10-07 23:23:36
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

## 메모리에서 static 영역

C언어나 어셈블리, 컴퓨터 구조 등을 배우면 메모리 구조를 알 수 있는데, 대부분 data, stack, heap, static 등의 영역을 알 수 있다.
이때 전역변수로 선언하는 값들은 static 영역에 들어가서 말그대로 코드 전체에서 참조할 수 있게 된다.
플러터와 다트에서도 마찬가지로 사용된다.

## 다트에서의 static

마찬가지로 동일하게 static은 메모리의 static 영역에 올라오는 데이터를 선언해주기 위해 사용한다.
클래스 필드를 static으로 선언시 객체 생성 없이 바로 모든 코드에서 그 값에 접근할 수 있게 된다.
말 그대로 전역변수와 동일하게 사용 가능하다.

```dart
class Config {
    static const bool isDarkMode = true;
}

...

if (Config.isDarkMode) {
    ...
}
```

이를 통해 일부 데이터를 전역에서 공유할 때 사용할 수 있겠다.
물론 이와 같은 코드를 남발할 시에 의도치 않은 의존성이 올라가고(결합도 증가) 퍼포먼스가 떨어지므로 다른 의존성 주입 방법으로 데이터 공유를 하는게 낫겠다.

또한 위처럼 사용할 때 혹시 실수로 객체 생성을 할 수 있으니, 아예 객체 생성을 못하도록 private 생성자를 작성하기도 한다.

```dart
class StringUtils {
    StringUtils._(); // private 생성자
}
```

일부 유틸 등을 구현할 때 static은 자주 사용될 것 같다.

## static, final, const?

앞서 freezed, immutable을 공부하면서 final과 const가 immutable을 구현하기 위한 방법이라고 했다.
static은 헷갈릴 수 있지만 이들과는 아예 다른 키워드로, 전역변수로 사용하기 위해 쓴다고 이해하면 되겠다.
