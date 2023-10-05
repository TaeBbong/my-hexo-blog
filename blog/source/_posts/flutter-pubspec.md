---
title: "[오늘의 플러터] pubspec.yaml 패키지 버전 관리 방법"
excerpt: "고급 플러터 기술 정리"
thumbnail: https://miro.medium.com/v2/resize:fit:1358/1*sY-WRAIp3kMkaF9qAucIEg.png
date: 2023-10-06 23:23:36
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

## dependencies vs dev_dependencies

플러터의 pubspec.yaml에 들어가면 외부 패키지들을 관리하는 코드가 두개 있다.
dependencies와 dev_dependencies인데, 차이점을 몰랐었다.
간단히 정리하면 dependencies의 패키지들은 릴리즈와 디버그 버전에서 모두 사용되고,
dev_dependencies는 릴리즈 버전 빌드 시 의존성에서 빠지게 된다.
따라서 개발간에만 사용되는 runner 등과 같은 패키지는 dev_dependencies에 넣는게 적절하겠다.

## 패키지 버전 관리(전통 vs 캐럿)

전통적인 방법(traditional method)라 불리는 문법은 다음과 같이 버전을 제약한다.

```yaml
test: '>=0.5.0 <0.12.0'
```

이렇게 이상과 미만으로 버전을 제약한다.

캐럿 방법(caret syntax)는 ^ 기호로 버전을 제약한다.

```yaml
test: ^1.2.3
```

이렇게 작성하면 1.2.3 이후로부터 프로젝트와 충돌이 나지 않는 최대 범위로 설정이 된다.
적용이 안되는 최초 버전을 직접 알아야 하는 전통 방법보다 훨씬 간단하다.
대신 dart sdk 버전은 기본적으로 전통 방법으로 작성되어 있는데, 이는 캐럿으로 작성하지 말라고 플러터에서 안내하고 있다.
이것만 유의하면 될 것 같다.