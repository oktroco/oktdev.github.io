---
layout: post
title: "Serialization(직렬화)"
date: 2019-12-20 23:00:00 +0900
categories: [development, django_rest]
show_sidebar: false
menubar: main_menu
permalink: '/category/django_rest/3/'
published: true
comment: true
---

```yml
이 글은 Django Rest Framework를 설명하면서, 그리고 중간에 나오는 예시코드는 파티와 멤버를 관리하는 서비스로 작성될 것입니다.
본인의 입맛에 맞게 참고하며 학습바랍니다.
또한 본 글을 Django REST framework 공식 도큐먼트(https://www.django-rest-framework.org/)를
기반으로 작성된 것이므로, 가급적이면 공식 도큐먼트로 공부하시기바랍니다.
```

# Serialization(직렬화)

[1. Serialization(직렬화)이 뭘까?](##Serialization(직렬화)이-뭘까?)

[2. Model 작성](##Model-작성)

[3. Serializer 작성](##Serializer-작성)

[4. ModelSerializer](##ModelSerializer)

[5. Shell에서 테스트해보기(Partymanager)](##Shell에서-테스트해보기(Partymanager))


## Serialization(직렬화)이 뭘까?

Serialization(직렬화)에 대한 공부를 디테일하게 하진않아서 간략하게 지식내에서 설명해보겠다. 직렬화에 대해 당장 깊은 설명을 원한다면 다른 문서를 보기바란다.

Serialization(직렬화)은 주로 Python등의 객체지향언어에서 작성된 
객체를 네트워크로 전송하기위해 문자열과 같이 전송에 용이한 형태로 데이터를 변환시켜주는 작업이다.

예를 들자면 내가 만약 int와 String변수들을 여럿다루는 클래스를 만들었다고 하자.
해당 클래스의 정보들을 클래스 자체로 네트워크를 통해 전송하려면, 정보를 받는 쪽에서도 클래스를 처리할만한 환경이 필요할 것이다.
하지만 클래스 정보를 받을만한 모든 곳에 해당 클래스를 처리할 환경이 구축되어있으리란 보장은 당연히 없다.

이 때, 정보를 보내주는 쪽에서 클래스의 정보를 Serialization하여 하나의 문자열, JSON 등의 읽기 쉬운 보편적인 형식으로 정보를 전송해준다면, 이러한 문제를 해결할 수 있다.

REST API에서도 데이터를 일반적으로 JSON형식으로 주고 받기 때문에, 
Django의 모델과 같은 객체정보나 통신코드 등의 정보를 섞어서 JSON형식으로 전송하려면 Serialization을 해주어야한다.

Django Rest Framework에서는 Serialization에 대한 기능이 잘 구축되어있다. 
이제 Django Rest Framework에서 Model을 작성하고 Seiralization을 하고 정보를 송수신하는 방법을 설명하겠다.
Model에 대해 이미 알고 있다면 [3. Serializer 작성](##Serializer-작성)으로 넘어가자.


## Model 작성

직렬화를 하려면 직렬화할 모델이 있어야한다. 그러므로 모델을 먼저 만들어보자.
사실 모델에 대한 부분을 전혀모른다면 Django의 공식 도큐먼트를 보는 것을 추천한다.
여기서는 예시코드를 위해서 파티와 멤버를 관리하는 모델을 작성하겠다.

특정 앱의 모델을 만드려면 특정 앱의 폴더에서 models.py를 만들고 아래의 양식에 맞춰 모델을 작성한다.

```yml
from django.db import models

class <모델명>(models.Model):
    <속성> = models.<필드타입>(<파라미터>)
    <속성> = models.<필드타입>(<파라미터>)
    .
    .
    
class <모델명>(models.Model):
    <속성> = models.<필드타입>(<파라미터>)
    <속성> = models.<필드타입>(<파라미터>)
    .
    .
.
.
```

### 예시코드
파티와 파티에 속한 멤버를 관리하는 앱이라면 아래와 같이 작성할 수 있겠다.

```yml
from django.db import models

# 파티 모델
class Party(models.Model):
    party_id = models.AutoField(primary_key=True)
    party_name = models.CharField(max_length=30, blank=True, null=False)
    create_dt = models.DateTimeField(auto_now_add=True, null=False, blank=False)
    update_dt = models.DateTimeField(auto_now=True, null=False, blank=False)
    delete_dt = models.DateTimeField(null=True, blank=True)

# 멤버 모델
class Member(models.Model):
    member_id = models.AutoField(primary_key=True)
    party = models.ForeignKey(Party, on_delete=models.CASCADE)
    create_dt = models.DateTimeField(auto_now_add=True, null=False, blank=False)
    update_dt = models.DateTimeField(auto_now=True, null=False, blank=False)
    delete_dt = models.DateTimeField(null=True, blank=True)
```

모델을 생성한 뒤에는 아래의 코드를 작성하여 마이그레이션 해준다.
```yml
python3 manage.py makemigrations
python3 manage.py migrate
```
모델에 대해서는 더 이상 디테일한 설명은 하지 않겠다.

## Serializer 작성

이제 Django Rest Framework의 기능인 Serializer를 사용할 때다. 앱의 폴더에 serializers.py를 작성해보자.
serializers.py는 아래와 같은 형식으로 작성한다.
```yml
from rest_framework import serializers

class <Serializer이름>(serializers.Serializer):
    <속성> = serializers.<필드타입>(<파라미터>)
    <속성> = serializers.<필드타입>(<파라미터>)
    .
    .
    .
```

### 예시코드

파티의 정보를 다루는 Serializer를 작성해보면 아래와 같다.
```yml
from rest_framework import serializers
from .models import Party

class PartySerializer(serializers.Serializer):
    party_id = serializers.IntegerField(read_only=True)
    party_name = serializers.CharField(max_length=30, required=False, allow_blank=True)
    create_dt = serializers.DateTimeField()
    update_dt = serializers.DateTimeField(required=False)
    delete_dt = serializers.DateTimeField(required=False)
```

models.py와 상당히 비슷한 형태의 모습을 하고 있다.
Serializer의 필드의 파라미터들에 대해서는 다음에 자세히 설명하도록 하겠다.
위의 코드는 작성된 모델의 속성을 코드 그대로 가져와서 Serializer에 맞춰 다시 작성한 것이다.

뭔가 반복되는 것 같아서 조금 번거로운 느낌이 있다. 이를 위해 모델을 가져오는 Serializer를 위해 ModelSerializer가 있다.

## ModelSerializer

ModelSerializer는 모델을 기준으로 작성한 Serializer를 좀 더 간략히 만들어준다.

ModelSerializer는 아래와 같은 양식으로 작성된다.

```yml
from rest_framework import serializers
from .models import <모델들>...

class <Serializer이름>(serializers.ModelSerializer):
    Meta:
        model = <참조모델>
        fields = ['속성1', '속성2' ...]
```

### 예시코드

위의 예시에서 작성한 긴 PartySerializer를 보면 models.py의 코드를 거의 그대로 가져온 것을 알 수 있다. PartySerializer를 ModelSerializer를 이용하여 아래와 같이 간략히 바꿔보자.
그리고 MemberSerializer도 추가해보자.

```yml
from rest_framework import serializers
from .models import Party

class PartySerializer(serializers.ModelSerializer):
    class Meta:
        model = Party
        fields = ['party_id', 'party_name', 'create_dt', 'update_dt', 'delete_dt']

class MemberSerializer(serializers.ModelSerializer):
    class Meta:
        model = Member
        fields = ['member_id', 'party', 'create_dt', 'update_dt', 'delete_dt']
```

## Shell에서 테스트해보기(Partymanager)

작성한 모델과 Serializer를 쉘에서 테스트해보자.
이 부분은 예시코드로 쓰여오던 partymanager를 기준으로 설명하겠다.
아래 코드를 보자

```yml
from partymanager.models import Party
from partymanager.serializers import PartySerializer
party = Party(partyname="첫번째파티")
party.save()
partyserializer = PartySerializer(party)
print(party)
#Party object (1)
print(partyserializer.data)
#{'party_id': 1, 'party_name': '첫번째 파티', 'create_dt': '2019-12-20T14:01:03.047368Z', 'update_dt': '2019-12-20T14:01:03.047402Z', 'delete_dt': None}

## JSON렌더링
from rest_framework.renderers import JSONRenderer
content = JSONRenderer().render(partyserializer.data)
print(content)
#b'{"party_id":1,"party_name":"\xec\xb2\xab\xeb\xb2\x88\xec\xa7\xb8 \xed\x8c\x8c\xed\x8b\xb0","create_dt":"2019-12-20T14:01:03.047368Z","update_dt":"2019-12-20T14:01:03.047402Z","delete_dt":null}'
```
위와 같이 직렬화된 파티모델을 JSON형식으로 변환시킬 수 있다.
이렇게 변환된 데이터는 네트워크를 이용한 정보송수신에 용이하게 쓰일 수 있다.

View에서 실제로 데이터를 어떻게 쓰는지는 다음 포스팅에서 설명하겠다.
