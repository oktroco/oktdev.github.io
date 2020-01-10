---
layout: post
title: "Django REST API 환경 구축"
date: 2019-12-18 16:00:00 +0900
categories: [development, django_rest]
show_sidebar: false
menubar: djangorest_menu
permalink: '/category/django_rest/2/'
published: true
comment: true
---

```yml
이 글은 Django Rest Framework를 설명하면서, 그리고 중간에 나오는 예시코드는 파티와 멤버를 관리하는 서비스로 작성될 것입니다.
본인의 입맛에 맞게 참고하며 학습바랍니다.
또한 본 글을 Django REST framework 공식 도큐먼트(https://www.django-rest-framework.org/)를
기반으로 작성된 것이므로, 가급적이면 공식 도큐먼트로 공부하시기바랍니다.
```

# Django REST API 환경 구축

아래 항목 중, Django시스템이 이미 구축되어있는 사람은 2,3 과정은 생략하길 권장한다.

[1. PIP에서 모듈 설치](#pip에서-모듈-설치)

[2. 프로젝트 및 앱 생성](#프로젝트-및-앱-생성)

[3. DB 모델 연동(PostgreSQL) 후 유저 생성](#db-모델-연동postgresql-후-유저-생성)

[4. settings.py에 앱 추가](#settingspy에-앱-추가)

[5. CORS 설정](#CORS-설정)

## PIP에서 모듈 설치

이미 Django를 설치하고 웹을 구동시켜봤다면, pip의 사용법에 대해서는 알고 있을 것이다.
만약 Django자체를 사용해보지 않았다면, 먼저 Django에 대해 이해를 하고 REST API를 공부하도록 하자.

Django REST API를 구축하려면 djangorestframework이라는 모듈을 설치해야하는데
기본적으로 django모듈을 먼저 설치해야한다.
먼저 django모듈을 설치하자.
그전에 가급적이면 가상환경을 실행시키고 설치하자.
```yml
가상환경 설치 및 활성화

python3 virtualenv <가상환경명> #<가상환경명>이라는 가상환경 생성
source <가상환경명>/bin/activate #가상환경 활성화, 윈도우는 <가상환경명>\Scripts\activate
```
```yml
Django 모듈 설치

pip install django
or 
python3 -m pip install django
```
아래 커맨드를 입력하여 pip에서 djangorestframework 모듈을 설치하자
```yml
Django Rest Framework 모듈 설치

pip install djangorestframework
or
python3 -m pip install djangorestframework
```
간혹, djangorestframework가 아니라 django-restframework와 같이 - 를 넣는 경우가 있는데,
pip에 django-restframework라는 모듈도 따로 있으므로 주의하도록 하자
**djangorestframework**이다! 슬래시는 없다!

공식 도큐먼트에서는 markdown, django-filter라는 모듈도 권장하고 있댜. 일단 참고만 하자.

### 예시 코드
```yml
python3 virtualenv partyenv
source partyenv/bin/activate
pip install django
pip install djangorestframework
```

## 프로젝트 및 앱 생성

이제 모듈을 설치했으니 프로젝트를 만들고 앱을 만들어야한다. 이 부분은 그냥 Django만 설치할 때와 같으므로,
Django의 프로젝트, 앱을 설치하는 방법이 선명하게 기억난다면, 과감하게 [아래 항목](##DB-모델-연동(PostgreSQL)-후-유저-생성)으로 넘어가자.

```yml
프로젝트 생성 후 앱 생성

django-admin startproject <프로젝트명>  #프로젝트 생성
cd <프로젝트명>                         #프로젝트 폴더로 이동
django-admin startapp <앱명>          #프로젝트의 앱 생성
```

그리고 settings.py의 INSTALLED_APPS에 '<앱명>'을 추가하자.

### 예시 코드
파티와 멤버를 관리하는 partyproject의 partymanager라는 앱을 만들어보겠다.

```yml
django-admin startproject partyproject
cd partyproject
django-admin startapp partymanager
```

```yml
settings.py

INSTALLED_APPS = [
    <기존코드>,
    ... ,
    ... ,
    ... ,
    'partymanager',
]
```

## DB 모델 연동(PostgreSQL) 후 유저 생성

Django REST API를 사용할 정도가 되었다면 아마 Django에서 기본 내장하는 SQLite가 아닌,
Oracle, MySQL, PostgreSQL 등의 RDB를 사용하는 게 맞다고 생각한다.
일단 이 글에서는 Django와 주로 연동되는 RDB인 PostgreSQL을 기준으로 설명하겠다.
마찬가지로 DB연동방법이 선명하게 기억나거나 이미 세팅이 되어있다면, 과감하게 [아래 항목](##settings.py에-앱-추가)으로 넘어가자.

PostgreSQL의 DB구축에 대한 설명을 일단 생략하겠다. 
여기서는 DB가 이미 구축되어있다고 가정하겠다.
일단 Django에서 PostgreSQL과 연동할 수 있도록 pip에서 psycopg2-binary 모듈을 설치해준다.
```yml
pip install psycopg2-binary
```
그 다음, settings.py에서 DATABASES 변수를 아래양식에 맞게 바꿔준다.

```yml
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': '<DB이름>',
        'USER': '<유저이름>',
        'PASSWORD': '<유저암호>',
        'HOST': '<호스트(주소)>',
        'PORT': '<포트>',
    }
}
```

### 예시 코드
HOSTNAME=localhost, DBNAME=party_db, USER=postgres, PASSWORD=postgres, POST=5433
이라고 가정하겠다.

```yml
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'party_db',
        'USER': 'postgres',
        'PASSWORD': 'postgres',
        'HOST': 'localhost',
        'PORT': '5433',
    }
}
```

마지막으로 마이그레이션을 해주면 연동이된다.
manage.py 가 있는 경로(최상위경로)로 가서 아래의 커맨드를 입력해주자
```yml
python3 manage.py migrate
```
마이그레이션이 완료되면 DB연동이 된 것이다.
아래의 커맨드를 이용하여 슈퍼유저를 만들어보자
```yml
python3 manage.py createsuperuser
Username (leave blank to use '<username>'): <유저명(ID)>
Email address: <유저이메일>
Password: <암호>
Password (again): <암호>
Superuser created successfully.
```
이렇게 하면 DB에 슈퍼유저가 생성된 것을 확인할 수 있을 것이다.

### 예시코드
```yml
python3 manage.py migrate

python3 manage.py createsuperuser
Username (leave blank to use 'oktroco'): oktroco
Email address: oktroco@gmail.com
Password: 
Password (again): 
Superuser created successfully.
```

## settings.py에 앱 추가

Django에서 직접 작성한 앱이나 외부의 앱을 가져올 시,
일반적으로 <프로젝트명>/settings.py에 있는 INSTALLED_APPS에 문자열 형태로 앱을 추가하게 된다.
rest framework도 마찬가지인데, INSTALLED_APPS에 아래와 같이 'rest_framework'를 추가하자.

```yml
INSTALLED_APPS = [
    <기존의 코드>,
    'rest_framework', # 이름을 통일했으면 좋겠다.
]
```

## CORS 설정

이 부분은 옵션이지만, 일반적으로 설정하는 것이 좋을 거다.
CORS(Cross Origin Resource Sharing)는 도메인 이나 포트가 다른 서버의 자원을 요청하는 매커니즘인데,
이럴 때는 cross origin HTTP로 요청 된다.
하지만 동일 출처 정책(same origin policy)라는 것 때문에 CORS 같은 상황이 발생 하면 외부서버에 요청한 데이터를 브라우저에서는 보안목적으로 차단한다. 이에 대해서는 나중에 자세히 다루겠다.

중요한 것은 react와 rest api를 같이 이용하는 경우에 일반적으로 react와 rest api 시스템이 다른 포트를 사용하기 때문에 CORS가 발생하여 브라우저에서 제대로 정보 조회가 안된다는 것이다.

이때 보편적인 방법으로는 HTTP요청의 모든 헤더에 Access-Control-Allow-Origin옵션을 주는 방법이 있는데, Django측에서는 모듈을 설치하면 CORS에 대해 허용하는 주소를 정할 수 있다. 방법은 아래와 같다.

pip install django-rest-cors 한 뒤
setting의 INSTALLED_APPS 에 'corsheaders' 추가하고 미들웨어 설정을 한다.

```yml
pip install django-rest-cors
```

```yml

INSTALLED_APPS = [
    <기존의 코드>,
    'corsheaders'
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware', #CommonMiddleware보다 위에 배치한다. 
    'django.middleware.common.CommonMiddleware',
    <기존의 코드>,
]
    
```

그리고 CORS_ORIGIN_ALLOW_ALL 이나 CORS_ORIGIN_WHITELIST 변수를 설정한다.
CORS_ORIGIN_ALLOW_ALL를 True 로 설정하면 어느 도메인이든 CORS를 허용하게 되고,
CORS_ORIGIN_ALLOW_ALL를 설정하지 않고, CORS_ORIGIN_WHITELIST에 도메인을 입력하면 해당 도메인의 CORS만을 허용한다.

```yml
CORS_ORIGIN_ALLOW_ALL = True

or

CORS_ORIGIN_WHITELIST = [
    'www.mysite.com',
    'www.mydomain.com'
]
```


여기까지 왔다면 Django REST Framework의 임포트를 완료한 것이다. 이제 모델을 구축하고 시리얼라이저를 생성하고 여러 디테일한 설정들을 해주면 REST API를 사용할 수 있다. 이번 글은 Django 환경 구축과 겹치는 부분이 많았다. 다음 글에서는 모델을 작성하고 시리얼라이저의 개념에 대해 설명하겠다.