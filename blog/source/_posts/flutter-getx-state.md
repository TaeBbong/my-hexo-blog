---
title: "[오늘의 플러터] 플러터 GetX 상태관리 정리"
excerpt: "고급 플러터 기술 정리"
thumbnail: https://miro.medium.com/v2/resize:fit:1358/1*sY-WRAIp3kMkaF9qAucIEg.png
date: 2023-10-17 23:23:36
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

## 0. 개요 및 키워드별 요약 정리

플러터의 상태 관리 도구 중 하나인 `GetX`에는 다양한 기능들이 있다.
대부분의 기능들은 아주 편리하게 작성되어 있으며, 활용하기 쉽다.
가장 핵심이 되는 것은 역시나 `상태 관리`와 `의존성`인데, 처음에 이 부분을 공부하면 헷갈리는 내용이 많다.
좋은 기능들을 다양한 방법으로 구현할 수 있도록 넣어주었기에 오히려 더 헷갈리는 것이다.
대부분의 글들에서 이렇게도 할 수 있고, 저렇게도 할 수 있다고 하니 어떤걸로 할지 모르겠는데, 이건 많이 해봐야 어떤게 어떤 상황에 좋은지 알 것 같다.
이번 포스트에서는 `GetX`에서 상태 관리와 의존성 주입 관련된 내용 전체를 정리한다.

키워드를 통해 요약 정리를 먼저하자면,

`단순 상태 관리(Simple State Management)` : `update()`로 직접 값의 변화를 알려줘야 하는 방법, `ChangeNotifier`와 유사, `GetBuilder`와 사용
`반응형 상태 관리(Reactive State Managemenet)` : `Rx`, `.obs` 키워드로 데이터 변화에 따라 알아서 재랜더링되는 상태 관리 방법이며, `Obx()`, `GetX()`를 사용
`GetBuilder()` : 단순 상태 관리에서 사용되는, 상태를 전달받는 위젯 빌더
`GetX()` : 반응형 상태 관리에서 사용되는, 상태를 전달받는 위젯 빌더로, 위젯 선언시 컨트롤러를 init 할 수 있음, `Get.find()`를 포함
`Obx()` : 반응형 상태 관리에서 사용되는, 상태를 전달받는 위젯 빌더로, 위젯 선언시 컨트롤러를 init 할 수 없음, `Get.find()`를 포함
`GetView` : 상속받는 추상 클래스로, `Get.find()`를 포함, update() 및 재랜더링을 할 수 없는 단순한 위젯 구현시 활용
`GetPage` : `GetX`에서 라우팅을 위해 사용하는 페이지 위젯으로, 컨트롤러 주입 등 다양한 기능을 포함하며 상태 관리와는 살짝 무관
`Get.put()` : 컨트롤러 객체를 생성하는(의존성을 주입) 함수, 선언 즉시 생성되어 주입됨
`Get.lazyPut()` : 컨트롤러 객체가 사용되는 순간 생성하는 함수
`Get.find()` : 이미 생성된 컨트롤러 객체를 찾는 함수

## 1. 단순 상태 관리(Simple State Management)

GetX의 [공식 문서](https://github.com/jonataslaw/getx/blob/master/documentation/en_US/state_management.md)에서 상태 관리 내용을 살펴보면, 단순 상태 관리와 반응형 상태 관리 두가지 방법이 있다고 한다.
차이점을 간단히 살펴보면 수동으로 상태 변화를 감지시키는지, 아니면 자동으로 상태 변화가 감지되어 재랜더링되는지의 차이이다.
단순 상태 관리는 ChangeNotifier와 동일한 방식이다.
컨트롤러에서 관리하는 상태는 일반적인 상태 변수와 동일하게 선언하며, 변경시킬때 이를 직접 업데이트 해주어 상태 변화를 감지시켜 위젯을 재랜더링 시킨다.

```dart
class SimpleController extends GetxController {
    String name = "Before Changed";
    void changeName() {
        name = "After Changed";
        update();
    }
}
```

일반적으로 상태 관리를 얘기하면 여기까지만 보는데, 상태 관리와 연결되어 상태를 사용하는 UI 위젯까지 같이 봐야 더 잘 이해된다.

```dart
// SimpleController 객체는 이미 생성(의존성 주입)되었다고 가정
// 의존성 주입 관련 내용은 3장에서
class SimplePage extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
        return GetBuilder<SimpleController>(
            builder: (controller) {
                return Column(
                    children: [
                        Text(controller.name),
                        ElevatedButton(
                            onPressed: controller.changeName,
                        )
                    ]
                );
            }
        )
    }
}
```

위젯에서는 `GetBuilder`를 활용해 구현할 수 있다. `GetBuilder`는 <>를 통해 컨트롤러를 찾아올 수 있다. (`Get.find()`와 동일)
그리고 `builder`에서 `controller`를 인자로 하여 컨트롤러의 상태와 기능 등을 사용할 수 있고, 상태가 변화하면 `update()` 덕분에 재랜더링된다.
단순 상태 관리에서 `update()`와 `GetBuilder`는 필수적이며, 이 둘은 짝지어 꼭 사용하면 된다.
`update()`를 작성하고 `GetBuilder`를 안쓰면 아무리 시도해도 상태 변화를 위젯이 감지하지 못한다. (이 실수를 처음에 많이 했었음)

## 2. 반응형 상태 관리(Reactive State Management)

반응형 상태 관리는 `update()` 없이 사용할 수 있는 상태 관리 방법이다.
상태 변화가 있을때에만 자동으로 재랜더링되는데, 이때 `Rx`, `Observable` 개념이 들어온다.
개념은 더보기에서 아주 자세히 알아보고, 간단히 말하면 지속적으로 관찰할 대상을 지정하는 것이다.

```dart
class ReactiveController extends GetxController {
    RxString name = "Before Changed".obs;
    void changeName() {
        name.value = "After Changed";
    }
}
```

이렇게 변수를 `RxType`으로 지정하고, 초기값에 `.obs`를 붙인다.
그리고 해당 값을 수정, 접근할 때에는 `name.value`와 같이 사용한다.
위젯에서는 `GetX()`를 사용한다.

```dart
class ReactivePage extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
        return GetX<ReactiveController>(
            builder: (controller) {
                return Column(
                    children: [
                        Text(controller.name.value),
                        ElevatedButton(
                            onPressed: controller.changeName,
                        )
                    ]
                );
            }
        )
    }
}
```

기존 단순 상태 관리와 다른 점은 `GetBuilder` 대신 `GetX`를 빌더로 사용했다는 것이다.
`update()`와 `GetBuilder`가 짝인 것과 같이, `Rx.obs`와 `GetX`가 짝이라고 생각하면 된다.
`Rx.obs`를 위젯에서 사용하는 또다른 방법은 `Obx`이다.

```dart
class ObxPage extends StatelessWidget {
    final controller = Get.find<ReactiveController>();
    
    @override
    Widget build(BuildContext context) {
        return Obx(() {
            return Column(
                    children: [
                        Text(controller.name.value),
                        ElevatedButton(
                            onPressed: controller.changeName,
                        )
                    ]
                );
        });
    }
}
```

`Obx`는 `GetX`와 다르게 컨트롤러를 `find`하는 코드, 기능이 없다.
따라서 미리 `Get.find()`로 찾아놓아야 한다.

## 3. 의존성 주입 방법과 추천

## (더보기) GetX가 상태를 관리하는 방법