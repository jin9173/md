---
title: 블로그 프로젝트 3일차 (Python, Django, Sqlite, Bootstrap)
date: 2019-12-20 23:50:50
categories: Project
tags: 
    - Python Project
    - Blog Project
---

[django-blog Repo](https://github.com/jin9173/django-blog)

## 1. 장고 관리자

#### superuser 등록

~~~bash
$ (django-blog-env) ➜ django-blog> ./manage.py createsuperuser
~~~

#### admin 사이트 접속

~~~bash
# 개발서버 실행 후 접속
$ (django-blog-env) ➜ django-blog> ./manage.py runserver
~~~

 `http://127.0.0.1:8000/admin` 으로 접속

![admin서버 로그인 화면](https://user-images.githubusercontent.com/57488252/71222143-bc138200-2312-11ea-8f50-1ee96e036619.png)

![admin 서버 화면](https://user-images.githubusercontent.com/57488252/71222164-cafa3480-2312-11ea-9496-ce0fa4a0eae8.png)

장고를 사용하여 웹사이트를 만들 경우, 매우 아름답게도 기본적인 구조를 갖춘 admin서버를 제공해준다. 하지만 큰 프로젝트일 경우 DB관리가 어려울 수 있기 때문에 비추천한다.

#### 관리자 화면을 한국어로 변경

~~~python
# django-blog/config/settings.py

LANGUAGE_CODE = 'ko'
~~~

#### admin.py 코드 작성

~~~python
# blog/admin.py
from django.contrib import admin

from blog.models import Post


@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    pass
~~~

- `from django.conrib import admin`

  : 장고의 External Libraries 파일에 내장되어 있는 admin파일을 불러온다

- `from blog.models import Post`

  : `blog`파일 안의 `models.py`에 만들었던 `Post` 클래스 객체를 불러온다

##### `@` Decorator
: 하나의 함수를 취해서 또 다른 함수를 반환하는 함수이다. 위의 코드에서는 `admin.site.register(Post)`를 데코레이터로 작성해 놓았다. 실행 순서는 class에서 가장 가까운 순서부터 실행한다. 

![Post객체가 추가된 모습](https://user-images.githubusercontent.com/57488252/71222653-b4ed7380-2314-11ea-83af-5f31d55d58b5.png)

#### post 데이터 추가하기

![](https://user-images.githubusercontent.com/57488252/71223563-b10f2080-2317-11ea-8d12-a0c075652486.png)

### 이전에 정의한 `Post`객체 코드와 비교해보자

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

![DB 브라우저에도 추가한 Post 데이터가 올라간 모습](https://user-images.githubusercontent.com/57488252/71223666-119e5d80-2318-11ea-975a-d90cb81b9be8.png)

장고 관리자 사이트에서 수동으로 Post 데이터를 추가하였다. DB에도 제대로 올라간 모습이 보인다.

기존에 코드를 작성할 때 `published_date` 값은 `null`값을 허용했다(`null=True`). 즉, 해당 값을 입력하지 않아도 데이터베이스는 생성될 수 있다. 하지만 `published_date`를 제외한 나머지 값에는 `null`값을 허용하지 않았기 때문에 값을 입력하지 않으면 데이터베이스에 추가될 수 없고 저장될 수 없다. 

![](https://user-images.githubusercontent.com/57488252/71224528-334c1480-2319-11ea-9d4d-59484dafd092.png)

## 2. 장고 urls

외부에서 서버로부터 요청이 왔을 때 그 요청은 url로 구분된다. 예를 들어, `naver.com`으로 요청이 왔을 때 `index.html`을 보여주거나, `naver.com/search?`로 왔을 때 뒤에 있는 query값을 보여주는 것으로 설명할 수 있다. 

서버 입장에서는 외부에서 요청이 왔을 때 사용자의 요청을 구분할 수 있는 방법은 **url방식**과 **http 요청에 따로 데이터를 담아 보내는 방식**으로 나뉠 수 있다. 장고는 url방식으로 요청을 구분해준다. 

#### 장고 urls 설정

~~~python
# django-blog/config/urls.py

from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
~~~

해당 기본 url설정은 장고에서 제공하는 admin 애플리케이션으로 가는 경로를 정의하고 있다. 

`path('admin/', admin.site.urls)`에서 `urls`를 클릭해서 내장되어 있는 장고의 url코드를 훑어보자. (`command` + click)

##### URLResolver

: URLResolver의 역할은 장고의 `urls.py`에서 정의되며 이는 외부에서 온 요청의 URL을 판단해서 특정 Controller(장고의 View함수)로 연결한다. 

##### 개발단계에서의 request와 response

`request -> runserver -> urls.py -> view function -> response`

1. 외부에서 요청(`request`)이오면 이 `request`는 `runserver`로 전달이 된다. 
2. 전달 받은 이 `request`는 `urls.py`가 분석해준다.
3. 이 때 `urls.py`는 특정 `view함수`로 분석된 request를 넘겨준다.
4. 들어온 요청은 `urls.py`에서 어디로 응답할지 판단한다. 
5. 판단해서 넘겨준 `view function`은 응답을 돌려준다. 
6. 그 응답은 `runserver`를 통해 `request` 자리로 돌아가서 응답된다. 

#### 장고 MTV 디자인 패턴을 따른 필요한 코드 구현 흐름

([장고의 MTV 디자인 패턴 참고 자료](https://jin9173.github.io/2019/12/20/etc/designPattern/))

하나의 페이지를 만들 때 

1. `urls.py`

   : `urls.py`에 어떤 식으로 요청이 왔을 때 어떻게 넘겨줄 것인지에 대한 url을 만들어주어야 한다. 

2. `views.py`

   : 그 요청을 처리할 Controller를 만들어주어야 하는데 장고에서 Controller는 View이다. 

3. `models.py`

   : 그 Controller가 데이터베이스를 가져와야 하기 때문에 `models.py`도 구현이 되어있어야 한다.

4. 그 다음에 Controller는 사용자에게 UI(View)로 html파일로 응답해주어야 한다.

## 3. 사용자가 보는 화면 제어하기

##### 'Post List!'라고 화면에 출력해보기

~~~python
# django-blog/blog/views.py
from django.http import HttpResponse

def post_list(request):
  return HttpResponse('Post List!')
~~~

`views.py`에 `HttpResponse` 함수를 사용하여 문자열 'Post List!'를 UI에 출력한다. 

~~~python
# django-blog/config/urls.py
from django.contrib import admin
from django.urls import path

from blog.views import post_list

urlpatterns = [
    path('admin/', admin.site.urls),
    path('post-list/', post_list)
]
~~~

`urls.py`에 `post-list`라는 URL에 온 요청을 `blog.views.post_list`함수가 처리할 수 있게 코드를 작성한다.

<span style="color: teal; font-size: 13px">`path`의 첫번째 인자인 `'post-list/'`는 url 주소를 의미하고 두번째 인자인 `post_list`는 `views.py`에서 생성한 함수를 의미한다. </span>

![해당 문자열이 출력된 사용자 UI 모습](https://user-images.githubusercontent.com/57488252/71238842-6e197100-2348-11ea-9b9c-3737ebbaf295.png)

#### Template 활용하기

##### 파일구조

HttpResponse`함수 안에 HTML 코드를 넣어 화면에 뿌려줄 수도 있지만 이는 작업하기에 매우 비효율적이고 가독성도 좋지 않을 것이다. 그러므로 MTV의 T인 template을 활용해보자.

~~~makefile
# 파일구조
django-blog
	blog
		migrations
	config
	templates
		post_list.html
~~~

##### HTML 작성

<span style="color: teal">`html:5` + `tab` 을 하면 pycharm에서 HTML 템플릿 자동 생성<br>`lorem30` + `tab`은 더미 문자열 생성</span>

~~~html
<!-- django-blog/templates/post_list.html -->
<!doctype html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>Post List!</h1>
    <div>
        <p>published: 12.11.2019, 14:38</p>
        <h2><a href="">My First Post</a></h2>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Adipisci cum delectus deserunt eaque facere harum illum, in mollitia nam officiis perspiciatis praesentium repellat repudiandae vitae voluptatibus? Dolorem obcaecati soluta veritatis.</p>
    </div>
    <div>
        <p>published: 12.11.2019, 14:39</p>
        <h2><a href="">My Second Post</a></h2>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Adipisci cum delectus deserunt eaque facere harum illum, in mollitia nam officiis perspiciatis praesentium repellat repudiandae vitae voluptatibus? Dolorem obcaecati soluta veritatis.</p>
    </div>
</body>
</html>
~~~

##### 파일 경로 설정

([os.path 메서드 참고 자료](https://devanix.tistory.com/298))

([시스템 메서드 참고자료](https://wikidocs.net/16077))

~~~python
# django-blog/blog/views.py
import os

from django.http import HttpResponse


def post_list(request):
    cur_file_path = os.path.abspath(__file__)
    blog_dir_path = os.path.dirname(cur_file_path)
    root_dir_path = os.path.dirname(blog_dir_path)
    templates_dir_path = os.path.join(root_dir_path, 'templates')
    post_list_html_path = os.path.join(templates_dir_path, 'post_list.html')

    f = open(post_list_html_path, 'rt')
    html = f.read()
    f.close()

    return HttpResponse(html)
~~~

- `os.path.abspath(__file__)`: 현재 위치하고 있는 파일 경로를 보여준다. 현재는 `views.py`에서 작업하고 있기 때문에 현재 위치는 `views.py`이다.
  
  - `cur_file_path`는 ` /Users/kimsejin/jin9173/django-blog/blog/views.py`이다. 
  
- `os.path.dirname()`: 상위로 파일 경로를 올라간다. 
  - `blog_dir_path`는 `cur_file_path`에서 한단계 상위로 올라간 경로인 `/Users/kimsejin/jin9173/django-blog/blog` 를 가리킨다.
  - `root_dir_path`는 `blog_dir_path`에서 한단계 상위로 올라간 경로인 `/Users/kimsejin/jin9173/django-blog`를 가리킨다.

- `os.path.join(기준점, 내려갈 파일이나 폴더이름)`: 하위로 파일 경로를 내려간다.
  - `templates_dir_path`는 `root_dir_path`에서 한단계 하위로 내려간 경로인 `/Users/kimsejin/jin9173/django-blog/templates`를 가리킨다.
  - `post_list_html_path`는 `templates_dir_path`에서 한단계 하위로 내려간 경로인 `/Users/kimsejin/jin9173/django-blog/templates/post_list.html`를 가리킨다. 

- `f = open(post_list_html_path, 'rt')`

  : `f`라는 변수에 시스템 메서드인 `open`을 사용하여 `post_list_html_path` 안에 있는 `post_list.html`파일을 텍스트 형식으로 읽도록 허용한다. 

- `html = f.read()`, `f.close()`

  : `f` 변수 안에 있는 텍스트 형식으로 읽도록 허용한 `post_list.html`을 읽어 `html`이라는 변수에 넣고 해당 상태를 저장(`f.close()`)한다. 

-  `return HttpResponse(html)`

  : `HttpResponse` 함수를 사용하여 `html`을 화면에 뿌린다. 

<span style="color: teal">방금까지 한 방식은 `templates/post_list.html`을 추가하고, 해당 내용 `post_list`를 `view`에서 돌려주도록 한 것이다. </span>


~~~python
# django-blog/config/settings.py
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
print(BASE_DIR)
~~~

기본적으로 경로설정이 되어있는 `BASE_DIR`을 출력해보면(`runserver`를 할 때마다 출력됨), `/Users/kimsejin/jin9173/django-blog` 가 출력된다. 즉, `BASE_DIR`은 최상위 root폴더로 경로설정되어 있다. <span style="color: teal">즉, `BASE_DIR`를 기준으로 아래와 같이 `templates` 디렉토리의 경로설정을 해주어야 한다.</span>

~~~python
# django-blog/config/settings.py
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
TEMPLATES_DIR = os.path.join(BASE_DIR, 'templates')
~~~

그리고 Django에게 기본 설정 외에 `templates`에 들어있는 파일들을 찾을 경로 목록을 지정해준다. (`django-blog/templates` 폴더 추가)

~~~python
# django-blog/config/settings.py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            TEMPLATES_DIR,
        ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
~~~

##### <span style="color: red">장고의 Help함수를 사용해서 간결하게 표현하기</span>

~~~python
# 장고의 shortcut함수인 render함수 사용
from django.shortcuts import render

def post_list(request):
  return render(request, 'post_list.html')
~~~

~~~python
# 장고의 render함수를 사용하지 않고 python에 내장되어 있는 render함수를 사용하여 코딩할 때
from django.http import HttpResponse

def post_list(request):
  template = loader.get_template('post_list.html')
  html = template.render()
  return HttpResponse(html)
~~~

**`render함수`**: 이 함수는 장고의 shorcut함수로써  `Template`을 찾을 경로에서 `post_list.html`을 찾아 그 파일을 text로 만든 후 `HttpResponse`형태로 돌려주어 브라우저에 그려준다. 







