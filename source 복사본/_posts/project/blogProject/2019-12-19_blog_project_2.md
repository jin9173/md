---
title: 블로그 프로젝트 2일차 (Python, Django, Sqlite, Bootstrap)
date: 2019-12-19 09:29:50
categories: Project
tags: 
    - Python Project
    - Blog Project
---

- [django-blog Repo](https://github.com/jin9173/django-blog)

## 1. blog app 생성

#### django 파일구조

<img src="https://user-images.githubusercontent.com/57488252/71133948-ca449e00-223f-11ea-8b32-3f8f5a8cc8d2.png" style="width: 600px">

~~~makefile
django-blog						# 프로젝트 이름 (최상위)
	config							# 설정패키지 폴더
		__init__.py
		settings.py
		urls.py
		wsgi.py
	.gitignore
	.python-version
	manage.py						# 장고관련 도움을 주는 유틸리티 모듈
~~~

#### 서버 구동하기

~~~bash
$ (django-blog-env) ➜ django-blog> ./manage.py runserver
~~~

#### 서버 구동 후 `localhost: 8000`으로 접속

: 장고를 개발서버에서 구동

![](https://user-images.githubusercontent.com/57488252/71134377-64591600-2241-11ea-8789-f8aee28926eb.png)

**개발서버 존재 이유**: 애플리케이션을 개발했을 때, 실제 request를 보내고 response가 어떻게 돌아오는지 그때그때 알아야 한다. 이 작업을 매번 다른 컴퓨터에 서버를 띄우고 확인하는 것이 굉장히 힘들고 비효율적이다. 즉, 개발서버를 로컬에서 띄워 해당작업을 반복하여 테스트할 수 있다.

**개발서버의 장점**: 소스코드를 바꾸면 서버가 알아서 restart되고 소스코드에 틀린 부분이 있다면 터미널에 디버그 메시지가 나타나기 때문에 개발하기 편하다.

~~~bash
# 개발서버 끄기
ctrl + c
~~~

#### 애플리케이션 만들기

: 프로젝트 내부에 애플리케이션을 만듬

~~~bash
$ (django-blog-env) ➜ django-blog> ./manage.py startapp blog
~~~

![애플리케이션을 추가한 장고 프로젝트의 파일구조](https://user-images.githubusercontent.com/57488252/71136849-7e96f200-2249-11ea-8433-9969ff01299a.png)

해당 애플리케이션에서 장고 기능만 있는 것이 아니라 블로그 기능, 설문조사 기능, 이미지 업로드 기능 등 많은 기능을 가질 수 있다. 이러한 기능들을 의미 단위로 구분하기 위해 `blog`라는 패키지에서 소스코드를 작성한다. `blog`라는 패키지에 <span style="color: red">데이터베이스 모델과 관련이 있는 경우 이를 장고에서는 특별히 애플리케이션이라고 부른다.</span> 물론 `django-blog` 프로젝트에서 데이터베이스를 사용하지 않는 기능을 만들 경우에도 추가 가능하지만 이를 패키지라고 부른다.

**`blog` 패키지를 애플리케이션으로 사용하기 위해서는 `config`의  `settings.py` 파일에 명명해주어야 한다.

~~~python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # 이 패키지는 application취급
    'blog',
]
~~~

## 2. post model 생성 및 migration

#### [models](https://wayhome25.github.io/django/2017/03/20/django-ep5-model/)

- `models.CharField`: 글자 수가 제한된 텍스트를 정의할 때 사용 (제목같이 짧은 문자열 정보를 저장에 적합)
- `models.TextField`: 글자 수에 제한이 없는 긴 텍스트를 위한 속성 (블로그 콘텐츠 저장에 적합)
- `models.DateTimeField`: 날씨와 시간
- `models.ForeignKey`: 다른 모델에 대한 링크

##### 관계형 데이터베이스 - RDB (Relational Database)

: 키(key)와 값(value)으로 이루어진 데이터베이스 테이블끼리 관계를 맺을 수 있다. (Python 딕셔너리 데이터타입과 유사)

<span style="color: teal">속도가 아닌 용량을 중심으로 발달했기 때문에 데이터를 찾을 때 NoSQL보다 시간이 소요된다</span>

##### 관계형 데이터베이스 관리 시스템 - RDBMS (Relational Database Management System)

: 관계형 데이터베이스를 관리하는 시스템 (e.g. Oracle, msSQL, MySQL...)

#### 장고모델 만들기

: 이 모델을 저장하면 그 내용이 데이터베이스에 저장됨

`blog` 애플리케이션 안에 `models.py`에 작성

~~~python
from django.conf import settings
from django.db import models
from django.utils import timezone


class Post(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    title = models.CharField(max_length=200)
    text = models.TextField(blank=True)
    created_date = models.DateTimeField(default=timezone.now)
    published_date = models.DateTimeField(blank=True, null=True)

    def __str__(self):
        return self.title

    def publish(self):
        self.published_date = timezone.now()
        self.save()
~~~
##### 설명
- `class Post(models.Model)`
- `Post` 객체모델을 정의
  
- `models`: `Post`가 장고모델임을 의미하고 `Post`에 데이터베이스에 저장되어야 한다고 알림
  
- 속성 정의

  - `author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)`

    - `author`객체는 장고 모델인 `models`에 외부참조키(`ForeignKey`)를 참조 

    - 해당 `ForeignKey`는 `config`의  `settings`파일에 정의된 `AUTH_USER_MODEL`객체에서 가져옴

    - `author` 객체의 어떤 레코드가 삭제되면 (`on_delete`), 이와 관련있는 `Post`객체의 레코드들도 연차적으로 삭제시킴. (여기서 `author`와 연관있는 객체는 `AUTH_USER_MODEL`의 레코드)

      <span style="font-size: 12px">만약, `on_delete`가 설정되어 있지 않으면, 모델 `author`의 레코드를 참조하고 있는 다른 모델의 레코드가 있기 때문에 삭제할 수 없음</span>

      <span style="font-size: 13px; color: teal">**`CASCADE`**: 어떤 행위를 직접적으로 지시를 받지 않았으나, 연관된 어떤 모델이 받은 행위에 같은 영향(행위)을 받게 함</span>

  - `title = models.CharField(max_length=200)`

    : `title`객체는 장고모델인 `models`의 `CharField`속성을 사용하여 최대 글자 수를 `200`으로 제한한다.

  - `text = models.TextField(blank=True)`

    : `text`객체는 장고모델인 `models`의 `TextField`속성을 사용하여 긴 텍스트를 허용하고 공백(`blank=True`)을 허용한다.

  - `created_date = models.DateTimeField(default=timezone.now)`

    : `created_date`객체는 장고모델인 `models`의 `DateTimeField`속성을 사용하여 시간의 기본값을 현재 시간으로 지정(`default=timezone.now`)한다.

  - `published_date = models.DateTimeField(blank=True, null=True)`

    : `published_date`객체는 장고모델인 `models`의 `DateTimeField`속성을 사용하여 공백도 허용(`blank=True`)하고 아무 값도 입력되지 않는 것(`null=True`)도 허용한다.

- 함수 정의

  ~~~python
  # __str__ 문자열화 해주는 함수 선언
  def __str__(self):
    return self.title
  ~~~

  `__str__`은 인스턴스 자체를 출력할 때 형식을 지정하는 함수로써 인스턴스가 문자열로 어떻게 표현될지 결정해주는 역할을 한다. 

  <span style="color: teal">즉, `__str__`을 호출하면 `Post`모델의 제목은 문자열로 변환되어 얻게된다.</span>

  ~~~python
  def publish(self):
    self.published_date = timezone.now()
    self.save()
  ~~~

  `publish`함수가 호출되면 `Post`객체의 `published_date`의 `timezone.now()` 속성이 호출되어 현재 포스트를 발행한 시간을 기록하고 데이터베이스에 저장(`self.save()`)한다. 

#### 데이터베이스 툴 설치

: 장고모델을 데이터베이스에서 확인하기 위해 **Sqlite** 설치

~~~bash
$ brew cask install db-browser-for-sqlite
~~~

#### Sqlite 실행

##### `manage.py`에 저장된 데이터베이스를 Sqlite에 적용시키기

~~~bash
$ (django-blog-env) ➜ django-blog> ./manage.py migrate
~~~

![데이터베이스 열기](https://user-images.githubusercontent.com/57488252/71141492-a42af800-2257-11ea-8d58-47283b26fca8.png)

![migrate된 데이터베이스](https://user-images.githubusercontent.com/57488252/71141423-6f1ea580-2257-11ea-9340-af0911ce538b.png)

- `auth_....`: 이 파일들은 인증을 위해 만들어진 기본적인 데이터베이스 파일(자동으로 생성)
- `django_admin...`: 장고에서 관리자페이지를 만들 때 자동으로 생성됨

#### 현재 소스코드에서 생성한 내가 만든 `Post` 테이블 추가

~~~bash
$ (django-blog-env) ➜ django-blog> ./manage.py makemigrations
~~~

![](https://user-images.githubusercontent.com/57488252/71142983-51077400-225c-11ea-9db6-d39ce0d47ef5.png)

`makemigrations`를 한 후 `Post`테이블을 데이터베이스에 추가하면 위 그림과 같이`models.py`에서 생성한 `Post` 테이블 객체가  `blog_post`라는 테이블로 생성된다. 

해당 `blog_post`테이블에는 6개의 컬럼이 존재하며 `id`컬럼은 기본적으로 `PrimaryKey`값으로 자동 생성된다.

