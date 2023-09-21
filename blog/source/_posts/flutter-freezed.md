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

## 사용 예제

## 원리 이해(with immutable)

사실 freezed는 모델 클래스 자동생성을 위한 코드 생성기이기도 하지만, 이 과정에서 많은 기능을 한번에 생성해주기 위해 다양한 개념을 포함한다.
freezed는 기본적으로 JSON Serialize 기능을 가지며, 객체간 비교를 위한 equal, 객체 복사를 위한 deepCopy(copyWith)을 가진다.
이 중 JSON Serialize나 equal은 개념적으로 더 이해할 것이 없으므로, deepCopy와 immutable에 대해 알아보자.

immutable은 말 그대로 불변이라는 뜻이며, 객체가 생성되면 그 값은 더 이상 변할 수 없다는 것이다.
화면 A와 B에서 공통적으로 사용하는 객체가 있다면, 이 객체의 