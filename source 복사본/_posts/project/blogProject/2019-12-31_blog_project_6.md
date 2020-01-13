---
title: 블로그 프로젝트 6일차 (Python, Django, Sqlite, Bootstrap)
date: 2019-12-31 13:27:50
categories: Project
tags: 
    - Python Project
    - Blog Project
---

[django-blog Repo](https://github.com/jin9173/django-blog)

## Post detail 페이지 만들기

#### `/post-detail/` url 설정

~~~python
# django-blog/config/urls.py
from django.contrib import admin
from django.urls import path

from blog.views import post_list, post_detail

urlpatterns = [
    path('admin/', admin.site.urls),
    path('post-list/', post_list),
    path('post-detail/', post_detail),
]
~~~

#### `post_detail` 함수 만들기

~~~python
# django-blog/blog/views.py
from django.shortcuts import render

from blog.models import Post

def post_detail(request):
    post = Post.objects.all()[0]
    context = {
        'post': post,
    }
    return render(request, 'post_detail.html', context)
~~~

- `post = Post.objects.all()[0]`: 전체 `Post`목록(Post전체 QuerySet) 중 `[0]`번 index에 해당하는 Post객체 하나를 `post`변수에 할당한다.
- `context = { 'post' : post, }`: `context`라는 이름의 딕셔너리를 만들어 `'post'`라는 Key에 위 `post`변수를 value로 사용한다.
- `return render(request, 'post_detail.html', context)`: 이 `context`변수를 `render`의 3번째 인자로 전달한다. 

#### templates 파일에 `post_detail.html` 생성 후 내용 작성

~~~html
<!-- django-blog/templates/post_detail.html -->
<!doctype html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Post Detail</title>
</head>
<body>
    <h1>Post Detail!</h1>
    <h2>{{ post.title }}</h2>
    <p>{{ post.author }}</p>
    <p>{{ post.text }}</p>
    <p>{{ post.created_date }}</p>
</body>
</html>
~~~

![브라우저에 렌더링 된 post_detail.html](https://user-images.githubusercontent.com/57488252/71614033-edcaf980-2bec-11ea-827d-038dc38ff59c.png)

#### post detail에 `pk`매개변수 추가

화면에 잘 렌더링되는 것을 확인했다면 post detail에 `pk`매개변수를 추가하여 url로 서로 다른 값(인자)을 받을 수 있도록 만들어준다.

`pk`값을 경로에 주었을 때, 장고에서는 `models.AutoField`가 생성된다.

~~~python
id = models.AutoField(primary_key=True)
~~~

`AutoField`는 `IntegerField` 포함하고, `IntegerField`는 실제로 데이터베이스에도 `int`값을 저장할 수 있도록 만들어준다. 데이터베이스를 살펴보자.

![sqlite 데이터베이스](https://user-images.githubusercontent.com/57488252/71614561-bf9ae900-2bef-11ea-8a15-a58632b272c7.png)

`blog_post`의 `id`값은 `integer` 즉, 숫자만 받을 수 있도록 설계되어 있다. 즉, `post-detail`의 `pk` 매개 변수 값에 숫자라는 키워드인 `int`값을 선언해주어야 한다. 

~~~python
# django-blog/config/urls.py
from django.contrib import admin
from django.urls import path

from blog.views import post_list, post_detail

urlpatterns = [
    path('admin/', admin.site.urls),
    path('post-list/', post_list),
    path('post-detail/<int:pk>/', post_detail),
]
~~~

![post-detail 페이지에 pk값을 주면 TypeError가 뜸](https://user-images.githubusercontent.com/57488252/71648545-0a7c4400-2d49-11ea-9200-2347395cfae4.png)

장고의 에러메시지를 보면 `post-detail() got an unexpected keyword argument 'pk'`라는 문구가 뜬다. 이는 `post-detail`이라는 함수가 `pk`라는 예기치못한 인수를 받는다는 뜻이다. `post-detail` 함수를 살펴보자.

~~~python
# django-blog/blog/views.py
def post_detail(request):
    post = Post.objects.all()[0]
    context = {
        'post': post,
    }
    return render(request, 'post_detail.html', context)
~~~

`post-detail` 함수는 `request`라는 **하나**의 매개변수를 받는다. 외부 request를 받아서 처리할 수 있는 view함수는 무조건 첫번째 인자로 `request`라는 인자를 받는다. 어떤 값을 받는지 확인해보자.

~~~python
# django-blog/blog/views.py
# pk값도 추가
def post_detail(request, pk):
    print('post-detail request', request)
    print('post-detail pk', pk)
    post = Post.objects.all()[0]
    context = {
        'post': post,
    }
    return render(request, 'post_detail.html', context)
~~~

~~~
post-detail request <WSGIRequest: GET '/post-detail/999'>
post-detail pk 999
~~~

<span style="color: red; font-weight: bold">`post_detail()`에 전달된 `pk`에 따라 다른 Post를 보여주기</span>

view함수의 매개변수로 전달되는 `pk`를 사용하여 전달받은 `pk`값이 자신의 `pk` DB Column값과 같은 Post를 `post`변수에 지정한다. 이후 `pk`에 따라 `/post-detail/`에 접근했을 때, 다른 Post가 출력되는지 확인한다. 

~~~python
# django-blog/blog/views.py
def post_detail(request, pk):
  post = Post.objects.filter(pk=pk)
  print(post)
  context = {
    'post': post,
  }
  return render(request, 'post_detail.html', context)
~~~
~~~
<QuerySet [<Post: 자바스크립트>]>
~~~

![Post객체 전부가 반환됨](https://user-images.githubusercontent.com/57488252/71649390-bb86dc80-2d51-11ea-9ee6-1788fac9bf44.png)

post = Post.objects.filter(pk=pk)`는 객체와 데이터베이스를 자동으로 매핑해주는 ORM을 사용하여 QuerySet값을 반환한다. QuerySet값은 List와 비슷한 객체로써 위는 이 List 안에 Post객체가 하나 들어있는 것을 의미한다. 즉 Post라는 객체 안의 각각의 post들은 인덱스를 사용하여 꺼내주어야 한다.  

~~~python
def post_detail(request, pk):
    post = Post.objects.filter(pk=pk)[0]
    print(post)
    context = {
        'post': post,
    }
    return render(request, 'post_detail.html', context)
~~~

![0번째 pk값을 request를 보낸 후 출력된 모습](https://user-images.githubusercontent.com/57488252/71649392-bd50a000-2d51-11ea-8bb2-5ab7c3c53cdf.png)

`filter`메서드는 QuerySet객체 안의 모든 값을 가져오기 때문에 인덱스를 사용하여 코딩해야 한다. 하지만 `get`메서드를 사용하면 하나의 값만 가져올 수 있다. 

~~~python
def post_detail(request, pk):
    post = Post.objects.get(pk=pk)
    context = {
        'post': post,
    }
    return render(request, 'post_detail.html', context)
~~~

하지만 이 경우에는 데이터베이스에 들어있는 pk값을 url로 request보내면 잘 출력되지만, 그 이상의 수를 보내면 에러가 뜬다. 

![DoesNotExist](https://user-images.githubusercontent.com/57488252/71649783-c727d280-2d54-11ea-9180-412a573ee0af.png)

#### `try-except` 구문을 사용하여 pk에 해당하는 Post가 없는 경우, `HttpResponse('없음')`을 돌려주도록 함

~~~python
# django-blog/blog/views.py
def post_detail(request, pk):
    try:
        post = Post.objects.get(pk=pk)
    except:
        return HttpResponse('없음')
    context = {
        'post': post,
    }
    return render(request, 'post_detail.html', context)
~~~



![존재하지 않는 pk값을 요청한 경우 '없음'이라는 창 띄우기](https://user-images.githubusercontent.com/57488252/71649895-c5aada00-2d55-11ea-89ad-37431d5616bd.png)



<span style="color: red; font-weight: bold">장고의 Helper함수 사용하기</span>

~~~python
from django.shortcuts import render, get_object_or_404

def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    context = {
        'post': post,
    }
    return render(request, 'post_detail.html', context)
~~~



## `post_detail.html` 에 bootstrap 스타일 입히기

~~~html
<!-- django-blog/templates/post_detail.html -->
<!doctype html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Post Detail</title>

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
            <div class="card mb-3">
                <div class="card-body">
                    <h5 class="card-title">{{ post.title }}</h5>
                    <p class="card-text">{{ post.text|linebreaksbr }}</p>
                    <div class="text-right">
                        <span>{{ post.created_date }}</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</body>
</html>
~~~

![post_detail.html](https://user-images.githubusercontent.com/57488252/71650119-52a26300-2d57-11ea-8ab0-62e3e1e0d651.png)

화면에는 문제없이 스타일이 적용되어있지만 `post_detail.html`과 `post_list.html`의 내용이 대부분 중복되는 것을 볼 수 있다. 

#### 중복을 최소화하기

~~~html
<!-- django-blog/templates/base.html -->
<!doctype html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Post Detail</title>

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
            {% block content %}{% endblock %}
        </div>
    </div>
</body>
</html>
~~~

{% raw %}

<code>{% block content %}{% end block %}</code> 이 부분만 유동적으로 채운다. 

{% endraw %}

~~~html
<!-- django-blog/templates/post_list.html -->
{% extends 'base.html' %}

{% block content %}
    {% for post in posts %}
    <div class="card mb-3">
        <div class="card-body">
            <h5 class="card-title">{{ post.title }}</h5>
            <p class="card-text">{{ post.text|linebreaksbr|truncatechars:250 }}</p>
            <div class="text-right">
                <span>{{ post.created_date }}</span>
            </div>
        </div>
    </div>
    {% endfor %}
{% endblock %}
~~~

~~~html
<!-- django-blog/templates/post_detail.html -->
{% extends 'base.html' %}

{% block content %}
    <div class="card mb-3">
        <div class="card-body">
            <h5 class="card-title">{{ post.title }}</h5>
            <p class="card-text">{{ post.text|linebreaksbr }}</p>
            <div class="text-right">
                <span>{{ post.created_date }}</span>
            </div>
        </div>
    </div>
{% endblock %}
~~~



