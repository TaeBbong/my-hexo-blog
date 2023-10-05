---
title: "[오늘의 플러터] factory로 배우는 싱글톤 패턴"
excerpt: "고급 플러터 기술 정리"
thumbnail: https://miro.medium.com/v2/resize:fit:1358/1*sY-WRAIp3kMkaF9qAucIEg.png
date: 2023-10-04 23:23:36
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

## freezed에서 이어지는 factory

앞선 포스트인 freezed 관련 내용에서 Model.fromJson()와 같은 메소드를 볼 수 있었다.
이를 직접 구현시 대부분 다음과 같은 코드로 구현된다.

```dart
class User {
    ...
    factory User.fromJson(Map<String, dynamic> json) {
        return User({
            ...
        });
    }
}
```

여기서 메소드로 보이는 것 앞에 factory라는 키워드가 붙어있다.
이 키워드를 이해하려면 싱글톤 패턴이라는, 객체지향에서 아주 오래된 패턴을 하나 이해해야 한다.
결론만 먼저 얘기하자면 factory 키워드는 생성자를 만들때 사용하며, 해당 생성자가 새로운 인스턴스가 아닌 기존 인스턴스를 반환하도록 만드는 기술이다.

## 싱글톤 패턴(Singleton)

싱글톤은 생성자로 객체 생성 후, 또 생성자를 호출할 때 새로운 객체를 생성하는 것이 아닌 이미 메모리 상에 있는 같은 객체를 반환하도록 하여 객체의 반복 생성을 막는 패턴이다.
당연히 메모리를 아낀다는 장점이 있을 것이다.
어떻게 보면 앞서 freezed와 공부했던 immutable과 상충되는 개념일 수 있다.
아무튼 이런 패턴을 플러터와 다트에서 구현할 때 factory라는 키워드로 생성자를 구현한다.

## factory란

factory는 다트 언어에서 새로운 인스턴스를 생성하고 싶지 않을 때 사용하는 생성자 키워드이다.
생성자이므로 클래스와 같은 타입의 인스턴스를 리턴하도록 작성하여 사용한다.
그 예시가 앞서 봤던 fromJson()과 같은 경우일 것이다.
사실 factory를 사용하는 99%의 예제가 fromJson, fromSnapshot 등일 것이다.

또한 서브클래스의 인스턴스 반환시에도 사용할 수 있는데,

```dart
class Book {
    factory Book.comic() {
        return ComicBook();
    }
}

class ComicBook extends Book {
    ComicBook() : super();
}
```

와 같이 사용될 수 있을 것이다.
아직 객체지향에 대해 완벽히 이해하고 사용하지 못해서 서브클래스를 저런 식으로 사용하는 예제가 잘 떠오르지는 않지만,
좀 더 객체지향을 이해하고 기회가 되면 사용해볼 수 있을 것 같다.