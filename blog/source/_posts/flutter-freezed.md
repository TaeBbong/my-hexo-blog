---
title: "[오늘의 플러터] immutable & freezed 패키지로 모델 간단히 구현"
excerpt: "고급 플러터 기술 정리"
thumbnail: https://miro.medium.com/v2/resize:fit:1358/1*sY-WRAIp3kMkaF9qAucIEg.png
date: 2023-09-20 23:23:36
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

## Model 작성의 귀찮음

모델 작성시 다음과 같은 절차가 필요함
1. 모델 필드 선언
2. 생성자 선언
3. fromJson, toJson 메소드 선언
4. copyWith 작성
5. (추가) toString override

이게 모델 필드가 많고 모델 수가 많아지면 아주 큰 노가다임.
따라서 이를 자동으로 생성해주는 패키지를 사용 => freezed

## freezed

설치는 공식문서대로

```yaml
dependencies:
  flutter:
    sdk: flutter
  freezed_annotation:
  json_annotation:

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^2.0.0
  build_runner:
  freezed:
  json_serializable:
```

이후 모델 코드는 다음과 같이 작성

```dart
// lib/models/member.dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'member.freezed.dart';
part 'member.g.dart';

@freezed
class Member with _$Member {
  factory Member({
    required int id,
    required String email,
    required String name,
  }) = _Member;

  factory Member.fromJson(Map<String, dynamic> json) => _$MemberFromJson(json);
}
```

이후 터미널에서 다음 명령어를 실행(경로는 프로젝트 디렉토리면 됨)

```bash
$ flutter pub run build_runner build --delete-conflicting-outputs
```

이러면 다음과 같은 파일들이 생성됨

```dart
// lib/models/member.freezed.dart
// coverage:ignore-file
// GENERATED CODE - DO NOT MODIFY BY HAND
// ignore_for_file: type=lint
// ignore_for_file: unused_element, deprecated_member_use, deprecated_member_use_from_same_package, use_function_type_syntax_for_parameters, unnecessary_const, avoid_init_to_null, invalid_override_different_default_values_named, prefer_expression_function_bodies, annotate_overrides, invalid_annotation_target, unnecessary_question_mark

part of 'member.dart';

// **************************************************************************
// FreezedGenerator
// **************************************************************************

T _$identity<T>(T value) => value;

final _privateConstructorUsedError = UnsupportedError(
    'It seems like you constructed your class using `MyClass._()`. This constructor is only meant to be used by freezed and you are not supposed to need it nor use it.\nPlease check the documentation here for more information: https://github.com/rrousselGit/freezed#custom-getters-and-methods');

Member _$MemberFromJson(Map<String, dynamic> json) {
  return _Member.fromJson(json);
}
...
```

```dart
// lib/models/member.g.dart
// GENERATED CODE - DO NOT MODIFY BY HAND

part of 'member.dart';

// **************************************************************************
// JsonSerializableGenerator
// **************************************************************************

_$_Member _$$_MemberFromJson(Map<String, dynamic> json) => _$_Member(
      id: json['id'] as int,
      email: json['email'] as String,
      name: json['name'] as String,
    );

Map<String, dynamic> _$$_MemberToJson(_$_Member instance) => <String, dynamic>{
      'id': instance.id,
      'email': instance.email,
      'name': instance.name,
    };
```

이와 같이 자동으로 필요한 기능들을 전부 구현해줌.
물론 직접 개발한 것보단 코드도 길지만 어차피 자동생성이니 신경쓸 필요 없음.
사용은 동일하게 사용할 수 있으며, 앞선 요구사항이었던 부가기능들(copyWith, toString)도 사용 가능

## 원리 이해(with immutable)

사실 freezed는 모델 클래스 자동생성을 위한 코드 생성기이기도 하지만, 이 과정에서 많은 기능을 한번에 생성해주기 위해 다양한 개념을 포함한다.
freezed는 기본적으로 JSON Serialize 기능을 가지며, 객체간 비교를 위한 equal, 객체 복사를 위한 deepCopy(copyWith)을 가진다.
이 중 JSON Serialize나 equal은 개념적으로 더 이해할 것이 없으므로, deepCopy와 immutable에 대해 알아보자.

immutable은 말 그대로 불변이라는 뜻이며, 데이터가 생성되면 그 값은 더 이상 변할 수 없다는 것이다.
그럼 immutable이라는 개념이 왜 나타났는가 하면, 기본적으로 Dart에서 String과 같은 데이터는 변수에 할당되고 이후 값을 바꿀 때 실제로 바뀌지 않는다.
실제로는 메모리에 새로운 값이 올라오고, 이에 대한 주소값이 해당 변수에 새로 할당되는 개념이다.
결국 변수에는 실제 값이 들어있는게 아닌, 데이터 값이 있는 주소값이 변수에 들어가는 것이다. (물론 우리 눈에는 보이지 않지만)
그럼 기존에 있던, 더이상 참조할 수 없는 데이터는? Dart의 Garbage Collector가 열일하면서 데이터를 메모리 상에서 정리한다.
이런식으로 동작하는 원리는 문서상 나이브하게 적혀있는데, 개발자가 의도하지 않은 방향으로 데이터가 변조되는 것을 막는 역할이라고 한다.
이를테면 사용자가 프로필 수정 페이지에서 닉네임을 고치고, 수정 완료 버튼을 눌렀다 곧바로 취소했다면 어떻게 될까?
만약 immutable이 아닌 방식이라면 다음과 같이 되었을 것이다.

```dart
Person user = Person(nickname: "taehyung");
user.nickname = "taebbong";
modifyNickname(user); // 여기를 취소
```

일반적인 로직으로는 취소에 대한 상황을 고려하지 않는데, 그렇다면 현재 상태에서 user.nickname은 기존 닉네임인 "taehyung"이 아닌 "taebbong"이 된다.
취소를 했기 때문에 서버로 이 내용이 전달되지 않았고, 기기 상태에서도 반영되지 않아야 하는데 말이다.

만약 immutable한 방식으로 새로운 객체를 만들도록 로직을 구현한다면 어떨까.

```dart
Person user = Person(nickname: "taehyung");
Person newUser = user;
newUser.nickname = "taebbong"; // 실제로 이렇게 수정하진 않지만 개념상 보자.
modifyNickname(newUser).then(() {
    user = newUser;
}); // 여기를 취소

```

이러면 newUser가 서버로 전달되든 안되든 user.nickname은 계속 "taehyung"이 된다.
서버로 데이터가 전달된 이후에 user 객체를 갱신하므로 이벤트 취소와 관계 없이 user 상태 값이 온전히 잘 유지된다. 
이 과정에서 newUser라는 새로운 객체를 생성했는데, 왜냐면 immutable 개념에 따라 기존 객체인 user를 수정하지 못하고 새로운 객체를 만들어야 하기 때문이다.

이와 같은 개념을 활용해서 모든 변수들을 생성하고 선언한 이후 데이터가 바뀌지 않도록 하는 것을 immutable이라고 할 수 있겠다.

Flutter와 Dart에서 immutable을 사용하기 위해 아주 익숙한 선언자가 있다. 바로 final과 const이다.
final과 const 모두 데이터를 변경 불가하도록 하는 선언자이나, 약간 다른 점이 있다.
final은 런타임 때 변수에 데이터를 할당하고, const는 컴파일 시에 변수에 데이터를 할당한다.
런타임과 컴파일 타이밍의 차이점은 런타임은 실행 후 데이터를 할당하기 때문에 변수를 선언하자마자 초기값을 넣어주지 않아도 된다.
그래서 later라는 키워드를 통해서 본인이 직접 원하는 타이밍에 데이터를 할당해줄 수 있는 것이다.
반면 컴파일은 말그대로 빌드되는 과정에서 데이터가 할당되므로 초기값을 선언시에 꼭 넣어주어야 한다.
이것이 final과 const의 차이점이며, 이에 따라 사용하는 경우가 일부 다르다.

이를테면 클래스의 필드는 final로 작성하지만, 클래스의 생성자는 const로 작성하기도 한다.

```dart
class Person {
    final String name;
    final String address;
    final int age;

    const Person({required this.name, required this.address, required this.age});
}
```

## copyWith

아무튼 이러다보니 일반적인 방법으로는 객체의 필드 값을 변경할 수 없다.
기본적으로 immutable 방식은 기존 객체의 값을 변경하는 것이 아닌 새로운 객체를 생성하는 방식이므로,
기존 객체를 새로운 객체로 복사하면서, 복사 과정에서 바꾸고 싶은 값만 새롭게 설정하여 새로운 객체를 생성한다.
이와 같은 방식을 deepCopy, copyWith 라고 한다.

freezed로 자동생성된 copyWith 코드는 꽤나 복잡하므로, 직접 구현할 때 어떤 식으로 구현하는지 코드를 간단히 살펴보자.

```dart
class Person {
    ...
    Person copyWith({String? name, String? address, int? age}) {
        return Person(
            name: name ?? this.name,
            address: address ?? this.address,
            age: age ?? this.age,
        );
    }
}
```

이렇게 하면 새로 바꿀 값이 있으면 새로운 값을 넣고, 없으면 기존 값을 넣어 새로운 객체를 생성한다.
이와 같은 방식으로 copyWith을 구현할 수 있고, freezed는 조금 더 복잡한 방식으로 구현되었을 뿐 개념적으론 같은 내용이다.
여기까지 freezed로 모델을 간편하게 생성하는 방법을 공부하면서 자연스럽게 immutable, copyWith, final, const에 대해 알아보았다.