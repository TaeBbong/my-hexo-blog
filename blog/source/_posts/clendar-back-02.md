---
title: "[프로젝트] 클린더 프로젝트 - 백엔드 02"
excerpt: "클린더 프로젝트 백엔드를 개발하며 공부하는 기록들"
thumbnail: https://www.django-rest-framework.org/img/logo.png
date: 2023-02-03 20:23:36
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

## 30분만에 끝내는 기능 구현

Django REST Framework를 쓰다보면 이렇게 쉽게 개발해도 되나라는 생각이 들곤 합니다.
대부분의 기능이 구현된 클래스들이 상당히 친절히 되어 있어서, 실제로 코드를 작성하는건 몇줄되지 않습니다.
특히 단순한 CRUD는 고민할 필요가 없습니다.
물론 처음 공부를 시작할 때는 Serializer, FBV부터 CBV, mixins, ViewSet 순서대로 천천히 구현해보면서 구조를 몸소 느끼는 것이 더 좋습니다.

### HyperlinkedModelSerializer

시리얼라이저 개념은 예전에 정리해놓은 [포스트]()가 있어서 생략합니다.
기존에 사용했던 시리얼라이저는 ModelSerializer였는데, 이는 model과 fields만 정의하면 구현이 끝났기 때문에 편했습니다.
그러던 중 간만에 공식 문서를 보며 구현을 하려는데 HyperlinkedModelSerializer를 사용하고 있는 것을 확인했습니다.

```python
# user/serializers.py
from .models import MyUser
from rest_framework import serializers

class MyUserSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = MyUser
        fields = ['url', 'uid', 'spots',]
```

ModelSerializer와 HyperlinkedModelSerializer의 가장 큰 차이점은 pk를 url로 표현한다는 것입니다.
ModelSerializer로 직렬화된 결과를 확인하면 대부분 다음과 같은 형태입니다.

```json
// GET /myusers/
[
    {
        "uid": "3sdf72fgzs87fv2bef902f",
        "spots": [
            1, // manytomany, 또는 foreignkey로 엮여있는 다른 모델 객체가 pk로 표현됨
            2,
        ]
    }
]
```

이렇게 pk로 표현된 spots를 각각 다시 조회하려면, 요청 url을 다시 만들어서 조회해야 합니다.
이를 HyperlinkedModelSerializer로 바꾸면 다음과 같은 결과가 나옵니다.

```json
[
    {
        "url": "http://127.0.0.1:8000/myusers/3sdf72fgzs87fv2bef902f/",
        "uid": "3sdf72fgzs87fv2bef902f",
        "spots": [
            "http://127.0.0.1:8000/spots/1/",
            "http://127.0.0.1:8000/spots/2/"
        ]
    }
]
```

이제 spot을 조회하기 위해 spots에 나타난 url을 따라가기만 하면 됩니다.
관계된 다른 모델 객체를 조회하기 좀 더 직관적이고 편한 방법입니다.

### ViewSet

ViewSet은 Django REST Framework에서 가장 강력한 도구 중 하나 입니다.
특별히 기록할만한 부분은 없어서 예시 하나만 남기고 넘어가겠습니다.

```python
# user/views.py
from rest_framework import viewsets

from .models import MyUser
from .serializers import MyUserSerializer


class MyUserViewSet(viewsets.ModelViewSet):
    queryset = MyUser.objects.all()
    serializer_class = MyUserSerializer
```

### Router

Router는 ViewSet에서 작성한 view의 endpoint url 작성을 도와주는 도구입니다.
각 앱마다 urls.py를 작성하지 않고 최상위 프로젝트의 urls.py에서 한번에 작성할 수 있습니다.

```python
# clendar/urls.py
from django.contrib import admin
from django.urls import include, path
from rest_framework import routers

from spot.views import SpotViewSet
from spot_schedule.views import SpotScheduleViewSet
from user.views import MyUserViewSet
from user_schedule.views import UserScheduleViewSet

router = routers.DefaultRouter()
router.register(r'spots', SpotViewSet)
router.register(r'spot_schedules', SpotScheduleViewSet)
router.register(r'myusers', MyUserViewSet)
router.register(r'user_schedules', UserScheduleViewSet)

urlpatterns = [
    path('', include(router.urls)),
    path('admin/', admin.site.urls),
]
```

## 마치며

백엔드에서 구현할 코드 작성은 이게 끝입니다. 정말 간단하게 구현이 완료됐습니다.
이제 부가적으로 DB 연결, 배포 등의 과정만 남았습니다.