---
title: 블로그 프로젝트 4일차 (Python, Django, Sqlite, Bootstrap)
date: 2019-12-24 13:27:50
categories: Project
tags: 
    - Python Project
    - Blog Project
---

[django-blog Repo](https://github.com/jin9173/django-blog)

#### 쿼리셋 (QuerySets)

: 전달받은 모델의 객체 목록. (데이터베이스로부터 데이터를 읽고 필터를 걸거나 정렬을 할 수 있다) (c.f. [ORM]([https://jin9173.github.io/2019/12/24/DB/ORM(Object%20Relational%20Mapping)/])

#### 장고 extensions와 jupyter notebook 설치

~~~bash
$ (django-blog-env) ➜ django-blog> pip install django-extensions notebook
~~~

~~~python
django-blog/config/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # PYPI 패키지
    # Shell_plus를 쓰기 위한 library
    'django_extensions',
    
    'blog',
]
~~~

~~~bash
# jupyter notebook에서 django shell_plus 실행
$ (django-blog-env) ➜ django-blog> ./manage.py shell_plus --notebook
~~~

## ORM 사용하여 데이터를 QuerySets 객체로 변환
#### 1. 모든 객체 조회

~~~python
# shell_plus (jupyter notebook)
from blog.models import Post
Post.objects.all()
~~~

#### 2. 객체 생성

~~~python
# shell_plus (jupyter notebook)
from blog.models import Post
from django.contrib.auth.models import User

me = User.objects.get(username='sejinkim')
post = Post.objects.create(
	author=me,
  title='Sample title',
  text='Sample text',
)

post # <Post: Sample title>
~~~
![객체 생성 후 admin 서버에 데이터베이스 테이블에 추가된 모습](https://user-images.githubusercontent.com/57488252/71387657-32c4be00-2638-11ea-9033-ac65748d444c.png)

![객체 생성 후 데이터베이스에 추가된 모습](https://user-images.githubusercontent.com/57488252/71387586-e5e0e780-2637-11ea-9ecb-d85ceb6132c0.png)

#### 3. 데이터 필터링 `filter`

- `text__contains=''`: 해당 필드안에 있는 텍스트가 뒤에 있는 단어를 가지고 있는지를 검사

~~~python
from blog.models import Post

Post.objects.filter(text__contains='인터프리터')
# <QuerySet [<Post: 파이썬>]>
Post.objects.filter(text__contains='자바')
# <QuerySet [<Post: 자바스크립트>, <Post: 자바>]>
~~~

~~~python
from django.utils import timezone
from datetime import timedelta

now = timezone.now()
Post.objects.filter(created_date__lte=timezone.now() - timedelta(hours=1))
# <QuerySet [<Post: 자바스크립트>, <Post: 파이썬>, <Post: 자바>, <Post: 데이터베이스>, <Post: HTML>, <Post: CSS>]>
Post.object.filter(created_date__gt=timezone.now() - timedelta(hours=1))
# <QuerySet [<Post: Sample title>]>
~~~

- `timedelta`: 시간의 양

- `timezone.now()`: 현재 시간

- `lte`: less than equal

  : (현재시간 - 한시간)보다 그 전에 만든 post 데이터 필터링

- `gt`: greater than

  : (현재시간 - 한시간)보다 더 나중에 만든 post 데이터 필터링

#### 4. 데이터 정렬

~~~python
from blog.models import Post

# 생성된 순서대로 오름차순으로 정렬
Post.objects.all()
Post.objects.order_by('pk')
# <QuerySet [<Post: 자바스크립트>, <Post: 파이썬>, <Post: 자바>, <Post: 데이터베이스>, <Post: HTML>, <Post: CSS>, <Post: Sample title>]>

# 생성된 순서대로 내림차순으로 정렬
Post.objects.order_by('-pk')
# <QuerySet [<Post: Sample title>, <Post: CSS>, <Post: HTML>, <Post: 데이터베이스>, <Post: 자바>, <Post: 파이썬>, <Post: 자바스크립트>]>
~~~

## 템플릿 동적 데이터

#### `post-list`를 랜더링 시키기

~~~python
# django-blog/blog/views.py
from django.shortcuts import render

from blog.models import Post


def post_list(request):
  posts = Post.objects.all()
  context = {
    'posts': posts,
  }
  return render(request, 'post_list.html', context)
~~~

1. `posts`변수에 전체 Post를 가지는 QuerySet객체를 할당
2. `context`라는 `dict`를 생성하여, `posts`키에 위 `posts`변수를 value로 사용
3. `render`의 3번째 위치인자로 위 `context`변수를 전달

#### `post_list.html` 템플릿을 동적 데이터로 변환

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
    <div>{{ posts }}</div>
</body>
</html>
~~~

![템플릿 동적 데이터로 변환되어 렌더링된 모습](https://user-images.githubusercontent.com/57488252/71390711-b08ec680-2644-11ea-9f41-394c845fc5a7.png)

![실제 렌더링된 소스 내용](https://user-images.githubusercontent.com/57488252/71390786-f3e93500-2644-11ea-8448-67210ef75418.png)

{% raw %}
<code>post_list.html</code>의 <code>{{ posts }}</code>`의 <code>{{ }}</code>는 변수를 의미한다. <code>views.py</code> 소스를 보면, <code>context</code> 딕셔너리 객체에 <code>'posts'</code>라는 키 값의 value로 <code>Post.objects.all()</code>를 할당한 <code>posts</code> 쿼리셋이 담겨있다. 즉, <code>post_list.html</code>의 <code>{{ posts }}</code>는 <code>posts</code> 쿼리셋을 담아 <code>div</code>로 묶어 화면에 그려주는 것이다. 
{% endraw %} 

화면에 출력된 `<QuerySet [<Post: 자바스크립트>, <Post: 파이썬>, <Post: 자바>, <Post: 데이터베이스>, <Post: HTML>, <Post: CSS>, <Post: Sample title>]>`값은 [`repr`](https://pinocc.tistory.com/168)값으로  시스템(python interpreter)이 해당 `posts`객체를 인식할 수 있는 공식적인 문자열로 나타내어진 것이다. 

#### for문으로 `posts.title` 순회

: template문법으로 for문을 순회할 때 {% raw %}<code>{% %}</code>{% endraw %} (c.f. [jinja2](https://jinja.palletsprojects.com/en/2.10.x/))

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
    {% for post in posts %}
        <div>{{ post.title }}</div>
        <div>{{ post.author }}</div>
        <div>{{ post.created_date }}</div>
        <div>{{ post.published_date }}</div>
    {% endfor %}
</body>
</html>
~~~

![template문법으로 post안에 있는 객체들을 for문으로 순회하여 화면에 출력](https://user-images.githubusercontent.com/57488252/71391307-efbe1700-2646-11ea-8201-39ecba972218.png)

![출력된 브라우저 소스코드](https://user-images.githubusercontent.com/57488252/71391308-f2207100-2646-11ea-9f66-73e294e35896.png)

## Bootstrap 적용

#### Bootstrap 세팅

- [Bootstrap 홈페이지](https://getbootstrap.com/docs/4.4/getting-started/download/)에서 Compiled CSS and JS 다운로드 

- 압축해제 후 css폴더 안에 있는 `bootstrap.css`와 `bootstrap.css.map`을 복사

- `django-blog` 프로젝트 파일에 `static/bootstrap/` 정적 디렉토리 생성 후 붙여넣기

  ~~~makefile
  django-blog
  	blog
  	config
  	static
  		bootstrap
  			bootstrap.css
  				bootstrap.css.map
  	templates
  	manage.py
  ~~~

#### `link`태그로 Bootstrap 파일 불러오기

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

    <!-- Bootstrap -->
    <link rel="stylesheet" href="/static/bootstrap/bootstrap.css">
</head>
<body>
    <h1>Post List!</h1>
    {% for post in posts %}
        <div>{{ post.title }}</div>
        <div>{{ post.author}}</div>
        <div>{{ post.created_date }}</div>
        <div>{{ post.published_date }}</div>
    {% endfor %}
</body>
</html>
~~~

개발자모드의 콘솔을 켜보면 `bootstrap.css`를 받지 못한다고 에러 메시지가 뜰 것이다.

![bootstrap.css에 대한 에러 메시지](https://user-images.githubusercontent.com/57488252/71391771-f6e62480-2648-11ea-9c6f-1c0b0b55ad4b.png)

`http://127.0.0.1:8000/static/bootstrap/bootstrap.css`로 접근해보면 해당 경로는 맞지만 페이지를 찾을 수 없다고 뜬다. 

![](https://user-images.githubusercontent.com/57488252/71392128-5690ff80-264a-11ea-9d23-e365ccea5bb1.png)

#### `settings`파일에 Bootstrap 경로 설정해주기

~~~python
# django-blog/config/settings.py

STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]
~~~

![settings.py에 경로 설정 후 화면에 출력된 모습](https://user-images.githubusercontent.com/57488252/71392125-5690ff80-264a-11ea-880a-5670eb7e1ba9.png)

![settings.py에 경로 설정 후 bootstrap.css를 잘 찾는 모습](https://user-images.githubusercontent.com/57488252/71392123-5690ff80-264a-11ea-8e23-60cceefcde43.png)

#### [Bootstrap CSS를 적용](https://getbootstrap.com/docs/4.4/layout/overview/)하여 `post_list.html`스타일 바꾸기

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

    <!-- Bootstrap -->
    <link rel="stylesheet" href="/static/bootstrap/bootstrap.css">
</head>
<body>
    <div id="wrap">
        <nav class="navbar navbar-expand navbar-dark bg-dark">
            <a href="#" class="navbar-brand">DjangoBlog</a>
            <div class="collapse navbar-collapse">
                <ul class="navbar-nav mr-auto">
                    <li class="nav-item">
                        <a href="#" class="nav-link">Post List</a>
                    </li>
                </ul>
            </div>
        </nav>
        <div class="container-fluid pt-2">
            {% for post in posts %}
            <div class="card mb-3">
                <div class="card-body">
                    <h5 class="card-title">{{ post.title }}</h5>
                    <p class="card-text">{{ post.text }}</p>
                    <div class="text-right">
                        <span>{{ post.created_date }}</span>
                    </div>
                </div>
            </div>
            {% endfor %}
        </div>
    </div>
</body>
</html>
~~~

##### 줄바꿈 속성 `linebreaksbr` 필터 적용

![줄바꿈이 안되 있는 것을 확인](https://user-images.githubusercontent.com/57488252/71393142-79bdae00-264e-11ea-9979-87cc7316f66c.png)

분명 줄바꿈을 넣어 두 문단을 작성했는데 화면에는 줄바꿈이 허용되지 않고 한 문단으로 작성되었다. 이 문제를 해결하기 위해서는 `linebreaksbr` 속성을 추가해준다.

~~~html
<p class="card-text">{{ post.text|linebreaksbr }}</p>
~~~

![<br>태그가 추가된 모습](https://user-images.githubusercontent.com/57488252/71393240-26982b00-264f-11ea-866b-13f76153360c.png)

##### 글자수 제한 속성 `truncatechars` 필터 적용

파이썬 post를 보면 내용이 너무 많아 스크롤을 너무 많이 요구한다. 이는 post-list 카테고리에 적절하지 않기 때문에 화면에 출력되는 글자수에 제한을 준다. 

![넘치는 글자수](https://user-images.githubusercontent.com/57488252/71393360-868ed180-264f-11ea-8b35-7fcfef3be6f5.png)

~~~html
<p class="card-text">{{ post.text|linebreaksbr|truncatechars:250 }}</p>
~~~

![글자수 제한한 후](https://user-images.githubusercontent.com/57488252/71393489-1d5b8e00-2650-11ea-9cb2-7a6e72050d5e.png)



