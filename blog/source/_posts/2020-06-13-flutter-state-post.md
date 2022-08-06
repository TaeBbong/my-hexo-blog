---
title: "[오늘의 플러터] 플러터 상태관리 바이블 1편 - setState()"
date: 2020-06-13 15:28:12
tags:
category:
  - 개발
  - Flutter
tag:
  - Flutter
  - State
  - setState
  - Provider
  - BloC
  - 상태관리
---

플러터는 대세 프론트엔드 프레임워크입니다. 예전보다 플러터에 관심 갖는 분들이 많아짐을 느끼고 있는데, 요즘 프론트엔드 프레임워크와 뗄 수 없는 내용이 바로 `상태관리`라고 생각합니다. 상태와 상태관리는 플러터 프론트엔드 개발에서 핵심적인 부분이며, 이를 위한 다양한 도구가 있습니다. 본 포스트에서는 아주 기본적인 개념과 가장 쉬운 예시를 통해 플러터의 대표적인 상태관리 기법들(`setState()`, `Provider`, `BloC`)에 대해 정리해보겠습니다.

<!-- more -->

## 상태

상태를 아주 간단히 이해해봅시다. 상태란 (언제든) 바뀔 수 있는 데이터입니다! 뭔가 거창한 것 같은 이름인데 별게 없습니다ㅎㅎ.. 그림으로 좀 더 자세히 확인해보겠습니다.

<img src="https://taebbong.github.io/images/blog/flutterstate1.png" style="border: 1px">

해당 이미지는 플러터 공식 홈페이지에 있는 상태에 대한 예시 이미지입니다. 별 모양 버튼으로 찜하기를 할 수 있는 것을 확인할 수 있습니다.

<img src="https://taebbong.github.io/images/blog/flutterstate2.png" style="border: 1px">

위 그림처럼 별 모양을 누르면 별이 색칠되면서 카운트가 증가하고, 별 모양을 다시 누르면 별이 흰 색이 되며 카운트가 감소합니다.

그렇다면 위 기능을 구현하기 위해서는 무엇이 필요할까요? 서버와의 통신이고 데이터 저장이고 그런 복잡한 것은 빼고 생각해보면, 찜하기가 눌렸는지 안눌렸는지에 대한 정보가 필요할 것입니다. 그래야 앱이 현재 찜하기가 눌린 상태라면 색칠된 별 모양과 증가된 카운트 수를 보여줄 것이며, 찜하기가 눌리지 않은 상태라면 흰 별 모양과 감소된 카운트 수를 보여주겠죠.

이러한 찜하기 기능은 언제든 눌릴 수 있기 때문에 눌릴 때마다 페이지가 새로고침 된다던지 그러면 불편하겠죠? 즉 눌려졌는지를 잘 기록하고 있고, 그 값의 변화에 따라서 즉각적으로 새로운 모양을 보여줄 수 있도록 하는 것이 상태에 대한 기본적인 내용입니다.

## setState() 사용 예시

위와 같은 예시를 플러터로 작성하려면 `StatefulWidget` 을 활용해야 합니다. 플러터에서는 기본적으로 `StatelessWidget` 과 `StatefulWidget` 을 상속받아 여러 종류의 위젯 클래스를 만들 수 있는데, 변화하는 상태가 있는 위젯은 `StatefulWidget`, 그렇지 않은, 한번 로드되면 변하지 않는 정적인 위젯은 `StatelessWidget` 로 만들게 됩니다.

위의 UI 예시를 코드로 나타내면 다음과 같겠습니다.

```dart
class HomeScreen extends StatefulWidget {
  @override
  createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('setState example'),
      ),
      body: Column(
        children: [
          Padding(padding: EdgeInsets.all(10)),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: [
              Container(
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Container(
                      padding: const EdgeInsets.only(bottom: 8),
                      child: Text(
                        'Oeschinen Lake Campground',
                        style: TextStyle(
                          fontWeight: FontWeight.bold,
                        ),
                      ),
                    ),
                    Text(
                      'Kandersteg, Switzerland',
                      style: TextStyle(
                        color: Colors.grey[500],
                      ),
                    ),
                  ],
                ),
              ),
              Row(
                children: [
                  Icon(
                    Icons.star,
                    color: Colors.red[500],
                  ),
                  Text('41'),
                ],
              ),
            ],
          ),
        ],
      ),
    );
  }
}
```

이제 코드를 실행하면 위에서 봤던 이미지의 형태처럼 텍스트와 함께 빨간색 별이 있는 것을 확인할 수 있습니다. 상태에 초점을 맞추기 위해 불필요한 부분의 코드는 생략했습니다.

코드를 조금 자세히 살펴보겠습니다. 우선 `StatefulWidget` 클래스를 하나 만들었네요. `HomeScreen` 이라는 이름에서 알 수 있듯 홈화면을 구성하는 위젯이고, `MyApp` 클래스의 `home` 부분에 들어가는 위젯입니다. 플러터 프로젝트를 처음 시작한다면 나오는 `MyHomePage` 부분을 전부 지우고 위의 내용으로 채우면 문제 없이 실행될 것입니다.

```dart
class HomeScreen extends StatefulWidget {
  @override
  createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  ...
}
```

그리고 하단에는 `_HomeScreenState` 라는 클래스를 하나 만들었습니다. 이는 `State`를 상속받고 있는데, 이처럼 상태를 다루는 위젯을 구성할 때에는 `StatefulWidget`을 만들고, `State`를 하나 만든 다음에 `StatefulWidget`에서 `createState()`와 같은 함수를 활용하여 해당 클래스에서 사용할 상태 클래스를 가져온다는 개념으로 이해하시면 되겠습니다.

실제로 위젯의 내용을 구성하는 부분은 `StatefulWidget`이 아닌 `State` 내부에서 진행합니다. `Widget build()` 메소드도 `State` 클래스 안에서 선언하고 있으며, `StatefulWidget`은 그저 `State` 클래스를 연결해놓는 것으로 그 역할을 다 하고 있다고 볼 수 있겠습니다.

나머지 코드들은 UI를 구성하는 부분이기 때문에 따로 보지 않고, 우리가 상태 변화를 적용시킬 아이콘 부분만 보겠습니다. 지금은 그냥 간단하게 UI만 구성이 되어있네요.

```dart
...
Row(
  children: [
    Icon(
      Icons.star,
      color: Colors.red[500],
    ),
    Text('41'),
  ],
),
```

이제 이 부분이 버튼처럼 눌릴 수 있도록 처리를 해보겠습니다. 여러 방법이 있지만 이 경우에는 아이콘을 아이콘버튼으로 변경하는 것만으로 완성할 수 있습니다. 아래와 같이 코드를 수정합니다.

```dart
...
Row(
  children: [
    IconButton(
      icon: Icon(Icons.star),
      color: Colors.red[500],
      onPressed: () {}
    ),
    Text('41'),
  ],
),
```

이제 우리가 구성한 아이콘은 클릭이 가능한 버튼이 되었습니다. 해당 버튼이 눌렸을 때에 발생하는 동작을 `onPressed()`에 함수 형태로 선언할 수 있습니다. 우리의 의도는 버튼이 눌리면 색이 변경되고 텍스트에 들어가있는 숫자가 바뀌는 것이겠죠. 이 부분을 상태로 다루기 위해서는 다음과 같은 `상태`를 저장하는 변수가 필요합니다.

```dart
class _HomeScreenState extends State<HomeScreen> {
  bool _isPressed = false;
  @override
  Widget build(BuildContext context) {
    ...
  }
}
```

`State` 클래스 내부에 선언한 변수인 `_isPressed`는 상태로 사용할 수 있는 변수입니다. 기본 값으로는 `false`를 넣어두었습니다. 상태에 대한 선언은 저것으로 끝입니다! 상태라는게 대단한게 아니라 즉각적으로 변할 수 있는 변수에 불과하기 때문에 간단히 처리할 수 있었습니다.

`_isPressed`라는 상태를 정의했기 때문에, 우리는 UI를 어떻게 하면 눌린 상태와 눌리지 않은 상태에 띠라 구성할 수 있는지 알 수 있습니다. 삼항 연산자를 활용해 코드를 아주 간단하게 만들 수 있습니다.

```dart
Row(
  children: [
    IconButton(
      icon: _isPressed ? Icon(Icons.star) : Icon(Icons.star_border),
      color: _isPressed ? Colors.red[500] : Colors.white,
      onPressed: () {}
    ),
    Text(_isPressed ? '41' : '40'),
  ],
),
```

위의 코드를 통해 `_isPressed`가 `true`일 때, 즉 눌렸을 때는 빨간색으로 채워진 별 모양을 보여주고 숫자도 41로 보여주게 합니다. `false`일 때는 빈 별과 40을 보여주게 되겠습니다.

그러면 이제는 버튼이 눌렸을 때 일어나야 하는 일을 쉽게 정의할 수 있습니다. 버튼이 눌렸을 때에는 그저 `_isPressed` 값을 변경만 해주면 됩니다. 이것만으로 UI가 바뀔까요? 직접 확인해보겠습니다.

```dart
Row(
  children: [
    IconButton(
      icon: _isPressed ? Icon(Icons.star) : Icon(Icons.star_border),
      color: _isPressed ? Colors.red[500] : Colors.white,
      onPressed: () {
        setState(() {
          _isPressed = !_isPressed;
        });
      }
    ),
    Text(_isPressed ? '41' : '40'),
  ],
),
```

이제 앱에서 버튼을 눌러보면 아래와 같은 일이 발생합니다. 아주 매끄럽게 버튼을 누를 때마다 화면의 요소들이 변경되는 것을 확인할 수 있습니다.

<img src="https://taebbong.github.io/images/blog/flutterstate3.gif" style="border: 1px">

살짝의 해설을 덧붙이자면, `setState()`가 실행될 때마다 해당 위젯은 다시 빌드가 됩니다. 앱 전체가 아닌 해당 부분만 다시 빌드되기 때문에 우리는 어떠한 부자연스러움 없이 자연스럽게 동작에 대한 변화를 확인할 수 있습니다. 다시 빌드가 되는 방식이기 때문에 단지 서로 다른 `_isPressed` 값에 대한 UI를 선언해주는 것만으로 화면을 변경시킬 수 있습니다.

> 버튼이 눌리면 => `setState()`가 실행되면서 => `_isPressed` 값이 변경되고 => 화면이 다시 빌드되면서 => 화면을 그려주는 부분에서 변경된 `_isPressed`에 맞춰 화면을 그려준다!

는 흐름으로 위 코드가 동작함을 이해할 수 있습니다.

이런 방식으로 `setState()`를 활용해 아주 간단한 상태에 대한 처리 및 관리를 할 수 있습니다. 그런데 여기서 하나의 문제는, `setState()`만으로는 모든 문제를 해결할 수 없다는 것입니다. 어떤 경우에 `setState()`가 해결할 수 없는 상황이 발생할까요? 이는 다음 게시글의 주제인 `Provider`에서 확인해보겠습니다.
