---
title: "[프로젝트] 클린더 프로젝트 - 백엔드 01"
excerpt: "클린더 프로젝트 백엔드를 개발하며 공부하는 기록들"
thumbnail: https://www.django-rest-framework.org/img/logo.png
date: 2023-02-01 20:23:36
tags:
category:
  - 개발
  - Django Rest Framework
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

`클린더 Clendar` 프로젝트는 클라이밍장의 세팅데이, 휴무 등을 확인할 수 있고 사용자의 클라이밍 일정을 관리할 수 있는 간단한 앱 서비스입니다.
앱 프론트엔드는 `Flutter`, 백엔드는 `Django`를 활용하여 개발하고 있으며 이를 진행하면서 공부한 내용을 까먹지 않게 기록해보려 합니다.

## 백엔드 기능 정리

무슨 프로젝트를 진행해도 항상 기능 정리를 먼저 해야합니다. 간단한 개인 프로젝트이지만 배포까지 목표로 하고 있으므로 최대한 한번에 잘 설계하려고 했습니다.

- 암장별 세팅데이 및 휴무 추가/조회
- 유저 관심 암장 추가/삭제/조회
- 유저 일정 추가/삭제/수정/조회
- 암장별 색 자동 부여

## 모델 설계

`Django` 프로젝트를 진행할 때는 `모델`만 잘 신경쓰면 됩니다. 나머지는 `ViewSet` 덕분에 크게 구현할 거리가 없지요.
다음과 같이 모델을 설계 했습니다.

### blank=True vs null=True

```python
# 암장(Spot) 모델
class Spot:
    name: str
    location: str
    thumbnail: str
    color: str, blank=True
```

여기서 `blank=True`와 `null=True`가 조금 헷갈렸었는데, `blank=True`는 입력 폼에서 비워질 수 있다는 것이며 이때 기본값은 `""`인 빈 문자열로 들어갑니다. 
`null=True`는 DB에 `null` 값이 들어가도 된다는 것이며 폼에서 입력은 하되 키만 있고 값을 입력하지 않은 경우 `null`로 들어가는 것입니다.

### ManyToManyField, related_name 활용 다대다 관계 표현

```python
# 유저(MyUser) 모델
class MyUser:
    uid: str, primary_key=True
    spots: ManyToManyField(Spot, related_name="follower")
```

유저의 경우 `Django` 기본 유저와 헷갈릴 수 있으므로 `MyUser`로 이름을 작성했습니다.
기본 pk 대신 uid를 자동생성한 다음 이를 primary_key로 사용하기로 했으며, 별도의 회원가입 없이 기기 고유 번호로 생성된 uid를 사용할 것이므로 이렇게 처리했습니다.

`암장(Spot)`과 `유저(MyUser)`는 `다대다(ManyToMany)` 관계입니다. 유저는 여러 암장을 관심 암장으로 등록할 수 있고, 암장은 여러 유저에게 관심을 받을 수 있겠죠. `Django Model`에서 다대다 관계를 표현할때는 `ManyToManyField`를 한쪽 모델에서 정의합니다. 위 예시에서는 MyUser에 `spots`라는 `ManyToManyField`를 정의했습니다. 이러면 MyUser에는 여러 Spot을 등록하여 사용할 수 있습니다. 반대로 Spot에서 해당 Spot을 등록한 유저들을 가져올 때는 앞서 MyUser에서 등록한 `related_name`인 `follower`를 활용하여 조회할 수 있습니다.

```python
# User가 등록한 모든 암장 조회
user = MyUser.objects.get(pk=1)
return user.spots.all()
```

```python
# Spot을 등록한 모든 유저 조회
spot = Spot.objects.get(pk=1)
return spot.follower.all()
```

복잡해보이는 다대다 관계도 한쪽에서 `ManyToManyField`를 정의해주고, `related_name`을 함께 등록해주면 반대쪽에서도 이를 활용하여 똑같이 조회할 수 있다는 간단하고 쉬운 개념이었습니다.
물론 해당 서비스에서는 암장별로 사용자들을 조회할 일은 없으므로 related_name을 쓸 일은 없어보입니다.

### OneToMany 일대다 관계는 ForeignKey, on_delete 알아보기

```python
class SpotSchedule:
    title: str
    date: date
    spot: ForeignKey(Spot, related_name="schedules", on_delete=models.CASCADE)
```

먼저 암장의 휴무, 세팅데이를 표현하는 `SpotSchedule`은 암장 하나와 일정 여러개로 연결되는 `일대다` 관계를 갖습니다.
`Django Model`에서 일대다 관계를 표현할때는 `ForeignKey`를 사용하며, `related_name`을 활용에 앞서 다대다 관계에서 봤던 것처럼 반대쪽인 Spot 객체에서 전체 일정을 조회할 수 있습니다.

```python
class UserSchedule:
    date: date
    user: ForeignKey(MyUser, related_name="schedules", on_delete=models.CASCADE)
    spot: ForeignKey(Spot, on_delete=models.SET_NULL, null=True)
```

사용자의 암장 방문 일정을 담은 `UserSchedule`은 사용자, 암장과 일대다로 연결됩니다.
사용자는 모든 스케쥴을 조회할 필요가 있으므로 `related_name`을 `schedule`로 정의하였습니다.
암장 입장에서는 그날 어떤 사용자들이 방문할지 사용자들의 일정을 알 필요 없으므로 related_name을 굳이 정의하지 않았습니다.

이때 `ForeignKey`는 말그대로 외래 모델을 참조하는 것이므로 일대다에서 일이 삭제 됐을때 어떻게 조치할지 정의합니다. 이를 `on_delete`로 정의하는데, 대부분 `models.CASCADE`를 사용해 자동으로 함께 삭제해주는 방향으로 정의합니다.
하지만 암장을 삭제했다고 해서 사용자가 운동한 일정이 함께 삭제되면 슬프기 때문에 `models.SET_NULL`을 활용해 방문 일정은 그대로 남지만 방문했던 암장만 비워지는 형태로 설계했습니다.

이외에도 `on_delete` 옵션이 다양하게 있었습니다. 필요에 따라 사용할 수 있을 것입니다.

여기까지 모델 설계를 했고, 이제 API를 구현하면 되겠습니다.