---
title: 블로그 프로젝트 5일차 (Python, Django, Sqlite, Bootstrap)
date: 2019-12-30 13:27:50
categories: Project
tags: 
    - Python Project
    - Blog Project
---

[django-blog Repo](https://github.com/jin9173/django-blog)

## Static URL

##### URL Resolver가 하는 일

: Request를 분석하여 어떤 view function으로 연결해줌

##### 장고의 static URL

: Django가 개발단계에서 편의를 위해 제공

~~~python
# django-blog/config/settings.py

# url이름 설정
STATIC_URL = '/static/'

# 정적파일을 찾는 경로 추가
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]
~~~

`settings.STATIC_URL/<파일경로>` 를 찾으려면 장고는 `정적파일을 찾는 경로/<파일경로>`에 해당하는 파일을 응답으로 돌려줌

<span style="color: blue; font-weight: bold">예)</span> `~/jin9173/django-blog/static/bootstrap/bootstrap.css` 에서 `~/jin9173/django-blog/static/`은 정적파일을 찾는 절대경로이고, `bootstrap/bootstrap.css`는 파일경로를 말한다. 

즉, `settings.py`에 정적파일을 찾는 경로를 추가하여 장고에서 이 파일을 찾을 수 있도록 path들을 지정해준다. 

~~~python
# url이름과 정적파일 경로가 헷갈릴 수 있으니 이름을 바꿔보고 개발서버에 실행해보면,
STATIC_URL = '/hello/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]
~~~

![](https://user-images.githubusercontent.com/57488252/71613095-bad23700-2be7-11ea-8c03-f364eff0ea71.png)

개발서버 url은 `http://127.0.0.1:8000/hello/bootstrap/bootstrap.css` (`hello`에 주목)이고, 해당 파일 경로를 잘 찾았다. 

