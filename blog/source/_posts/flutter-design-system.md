---
title: "[오늘의 플러터] 플러터에서 디자인 시스템을 적용하는 방법(with ThemeData)"
excerpt: "고급 플러터 기술 정리"
thumbnail: https://miro.medium.com/v2/resize:fit:1358/1*sY-WRAIp3kMkaF9qAucIEg.png
date: 2024-05-19 23:45:36
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

## 0. 개요

디자이너와 함께 개발하면 디자인 시스템을 적용하게 됩니다.
색상 팔레트, 타이포그래피 등을 포함한 디자인 시스템은 디자인을 못하는 개발자들에게 참 좋은 길잡이가 되곤 합니다.
당연히 이를 매번 위젯 개발할 때마다 적용하기엔 귀찮고 비효율적이니, 가능한 좋은 방법을 찾아야 할 것입니다.
여태까지 해보면서, 그리고 고민하면서 찾은 방법을 공유해보겠습니다.

## 1. Theme

Theme는 플러터에서 디자인 시스템 적용을 쉽게 하기 위해 제공해주는 기능입니다.
[공식 문서](https://docs.flutter.dev/cookbook/design/themes)에서도 앱 전역에서 color, font style을 공유하기 위해 사용하라고 나와있습니다.

Theme를 활용할 때 주의할 점은 스타일이 적용되는 순서입니다.
사실 이 부분은 조금만 생각해보면 크게 어려울 것은 없는데, 가장 안쪽 범위에서 적용한 스타일이 우선적으로 적용됩니다.
이를테면 앱 전역 Theme와 버튼 위젯 하나에 직접 적용한 style의 경우 직접 적용한 스타일이 적용되는 것입니다.
결국 가장 나중에 적용한 스타일이 적용된다는, 직관적인 순서이니 알아두면 되겠습니다.

## 2. ThemeData

ThemeData는 Theme를 적용하기 위한 클래스입니다.
ThemeData에는 수많은 프로퍼티가 있지만, 이번에 알아볼 프로퍼티는 colorScheme과 textTheme입니다.

디자인 시스템에서 개발자가 주로 적용하는 내용은 색상 팔레트와 타이포그래피일텐데, colorScheme과 textTheme가 각각 그 역할을 수행합니다.
대략 다음과 같은 구조로 적용됩니다.

```dart
MaterialApp(
  title: appName,
  theme: ThemeData(
    useMaterial3: true,

    // Define the default brightness and colors.
    colorScheme: ColorScheme.light(
        background: Colors.white,
        primary: Color(0xff000e27),
        brightness: Brightness.light,
    ),

    // Define the default `TextTheme`. Use this to specify the default
    // text styling for headlines, titles, bodies of text, and more.
    textTheme: TextTheme(
      displayLarge: const TextStyle(
        fontSize: 72,
        fontWeight: FontWeight.bold,
      ),
      titleLarge: GoogleFonts.oswald(
        fontSize: 30,
        fontStyle: FontStyle.italic,
      ),
      bodyMedium: GoogleFonts.merriweather(),
      displaySmall: GoogleFonts.pacifico(),
    ),
  ),
  home: const MyHomePage(
    title: appName,
  ),
);
```

위젯에서는 기본적으로 Material 디자인 시스템이 인식하는 디자인 대로 적용해줍니다.
예를 들어 AppBar의 title의 경우 textTheme의 title 디자인을 적용받습니다.
이 부분은 그래서 따로 스타일 적용을 해주지 않아도 알아서 앞서 작성한 테마 스타일을 적용됩니다.

직접 적용이 필요한 경우에는,

```dart
style: Theme.of(context).textTheme.bodyMedium,
```

와 같이 적용할 수 있습니다.

원래 Theme를 적용하기 전까지는 constants 폴더에 text_styles.dart, colors.dart 파일을 각각 만들고 여기에 const로 각 스타일 및 색상을 정의하며 써왔습니다.
앱 전반 디자인을 한번에 적용할 때에는 Theme를 활용하는 것이 훨씬 효율적으로 보입니다.

## 3. Theme 일부 수정(override)

앱 전반 디자인을 Theme를 통해 적용했다면, 일부 위젯에서 기존 스타일을 수정해야 할 때가 있습니다.
이때 copyWith을 활용합니다.

```dart
style: Theme.of(context).textTheme.bodyMedium!.copyWith(color: Colors.blue),
```

## 4. 프로젝트 구조

제 경우 프로젝트를 진행하면서 디자인 시스템을 적용할 때 다음과 같이 활용하였습니다.

1. constants 폴더에 theme.dart, text_styles.dart, color_scheme.dart 파일 생성
2. text_styles.dart에서는 TextTheme, color_scheme.dart에서는 ColorScheme 객체 정의
3. theme에서는 ThemeData 객체 생성 후 앞서 만든 TextTheme, ColorScheme 적용

이렇게 파일을 굳이 3개로 나눈 이유는 라이트/다크 테마 등 테마를 여러개 작성해야 할 때 좀 더 보기 좋기 때문입니다.

```dart
// text_styles.dart
const TextTheme lightTextTheme = TextTheme(
  titleLarge: TextStyle(fontSize: 22, fontFamily: 'Pretendard'),
  bodyLarge: TextStyle(fontSize: 18, fontFamily: 'Pretendard'),
  bodyMedium: TextStyle(
    fontSize: 16,
    fontFamily: 'Pretendard',
    color: Colors.black54,
  ),
  bodySmall: TextStyle(fontSize: 14, fontFamily: 'Pretendard'),
);

const TextTheme darkTextTheme = TextTheme(
  titleLarge: TextStyle(fontSize: 22, fontFamily: 'Pretendard'),
  bodyLarge: TextStyle(fontSize: 18, fontFamily: 'Pretendard'),
  bodyMedium: TextStyle(
    fontSize: 16,
    fontFamily: 'Pretendard',
    color: Colors.grey,
  ),
  bodySmall: TextStyle(fontSize: 14, fontFamily: 'Pretendard'),
);
```

```dart
// color_scheme.dart
import 'package:flutter/material.dart';

const lightColor = ColorScheme.light(
  background: Colors.white,
  primary: Color(0xff000e27),
  secondary: Color(0xff000e27),
  brightness: Brightness.light,
);

const darkColor = ColorScheme.dark(
  background: Colors.black54,
  primary: Color(0xff000e27),
  secondary: Colors.lightBlue,
  brightness: Brightness.dark,
);
```

```dart
// theme.dart
import '../constants/color_scheme.dart';
import '../constants/text_styles.dart';

final ThemeData lightTheme = ThemeData(
  brightness: Brightness.light,
  colorScheme: lightColor,
  textTheme: lightTextTheme,
);

final ThemeData darkTheme = ThemeData(
  brightness: Brightness.dark,
  colorScheme: darkColor,
  textTheme: darkTextTheme,
);
```

```dart
// main.dart
...
    @override
    Widget build(BuildContext context) {
        return MaterialApp(
            debugShowCheckedModeBanner: false,
            theme: _isDarkMode ? darkTheme : lightTheme,
            ...
        )
    }
...
```

제 프로젝트에서는 다크/라이트 모드 전환 기능이 있어서 위처럼 삼항연산자로 표현했는데,
그렇지 않아도 되는 경우에는 다음과 같이 해도 되겠습니다.

```dart
// main.dart
...
    return MaterialApp(
        title: 'Flutter Demo',
        theme: themeData, // Light theme
        darkTheme: darkThemeData, // Dark theme
        ...
        themeMode: ThemeMode.system, // Use system theme setting by default
    );
...
```