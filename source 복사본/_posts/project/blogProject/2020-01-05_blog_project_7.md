---
title: 블로그 프로젝트 7일차 (Python, Django, Sqlite, Bootstrap)
date: 2020-01-05 13:27:50
categories: Project
tags: 
    - Python Project
    - Blog Project
---

## url태그를 사용하여 해당 정보 치환시키기

#### 해당 텍스트에 링크 추가

1. 클릭 시 해당하는 `/post-detail/어딘가/`로 이동시키기

~~~html
<!-- django-blog/templates/post_list.html -->
{% extends 'base.html' %}

{% block content %}
    {% for post in posts %}
    <div class="card mb-3">
        <div class="card-body">
            <h5 class="card-title">
              <!-- 이 부분 경로 설정 -->
                <a href="/post-detail/{{ post.pk }}/">{{ post.title }}</a></h5>
            <p class="card-text">{{ post.text|linebreaksbr|truncatechars:250 }}</p>
            <div class="text-right">
                <span>{{ post.created_date }}</span>
            </div>
        </div>
    </div>
    {% endfor %}
{% endblock %}
~~~

2. `Post List!` 메뉴 클릭시 `post_list.html`로 이동시키기

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
                      <!-- 이 부분 경로 설정 -->
                        <a href="/post-list/" class="nav-link">Post List</a>
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

## url 네임 만들어주기

해당 경로 이름도 커스터마이징 가능

<span style="color: teal">해당 `urlpatterns`에 있는 경로 이름을 변경할 경우 `html` 소스에 있는 경로의 이름도 변경해주어야 하기 때문에 번거로운 작업이 요구된다. 그러므로 해당 `url`이름을 변경하더라도 `html`소스의 경로 이름을 바꿀 필요 없도록 **네임**을 지정해줄 수 있다.</span>

~~~python
# django-blog/config/urls.py
urlpatterns = [
    path('admin/', admin.site.urls),
    path('posts/', post_list, name="post-list"),
    path('post-detail/<int:pk>/', post_detail, name="post-detail"),
]
~~~

~~~html
<!-- django-blog/templates/base.html -->
<ul class="navbar-nav mr-auto">
	<li class="nav-item">
    <!-- 템플릿 문법을 사용하여 path 지정 -->
		<a href="{% url 'post-list' %}" class="nav-link">Post List</a>
	</li>
</ul>
~~~

~~~html
<!-- django-blog/templates/post-list.html -->
<h5 class="card-title">
  <!-- 템플릿 문법을 사용하여 path 지정 -->
	<a href="{% url 'post-detail' %}">{{ post.title }}</a>
</h5>
~~~

<span style="color: red">하지만, `post-detail`에는 `NoReverseMatch` 에러가 발생한다.</span>

![NoReverseMatch Error](https://user-images.githubusercontent.com/57488252/71788531-a3230480-3066-11ea-8039-90e758feee0f.png)

해당 에러는 `Reverse for 'post-detail' with no arguments not found`라는 메시지를 보여준다. 해석하면 `'post-detail'`의 `Reverse`함수에는 인수가 없다는 뜻이다. <span style="color: teal">`Reverse`함수는 `path('posts/', post_list, name="post-list")` 에서 `name="post-list"`을 `posts/`로 번역 시켜준다.</span> <span style="color: red">즉, 해당 에러메시지는 `post-detail/<int:pk>`에서 `<int:pk>`를 번역할 수 없다는 뜻이다.</span>

이를 `shell-plus`에서 실행해보기.

~~~shell
$ (django-blog-env) ➜ django-blog>./manage.py shell_plus
~~~

`Reverse`함수가 있는지 확인해보기.

~~~Shell
In [1]: reverse
Out[1]: <function django.urls.base.reverse(viewname, urlconf=None, args=None, kwargs=None, current_app=None)>
~~~

`Reverse` 함수를 사용해보면 브라우저에서 보여졌던 에러메시지가 shell에서도 똑같이 뜬다. 

~~~shell
In [2]: reverse('post-list')                                                                                            
Out[2]: '/posts/'

In [3]: reverse('post-detail')    
NoReverseMatch: Reverse for 'post-detail' with no arguments not found. 1 pattern(s) tried: ['post\\-detail/(?P<pk>[0-9]+)/$']
~~~

`Reverse` 함수는 기본적으로 `urlconf`, `args`, `kwargs`, `current_app`이라는 매개변수를 가지고 있다. 그리고 `post-detail`의 url을 번역시키기 위해서는 `pk`라는 **키워드 인자**가 필요하다. 

~~~shell
In [4]: reverse('post-detail', kwargs={'pk':1})                                                                         
Out[4]: '/post-detail/1/'
~~~

잘 나오는 것을 테스트해서 확인했으니 해당 소스를 수정해보자.

~~~html
<!-- django-blog/templates/post_list.html -->
<h5 class="card-title">
	<a href="{% url 'post-detail' pk=post.pk %}">{{ post.title }}</a>
</h5>
~~~

