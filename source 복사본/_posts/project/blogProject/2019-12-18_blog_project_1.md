---
title: 블로그 프로젝트 1일차 (Python, Django, Sqlite, Bootstrap)
date: 2019-12-18 11:29:50
categories: Project
tags: 
    - Python Project
    - Blog Project
---

- [django-blog Repo](https://github.com/jin9173/django-blog)

#### Django 설치

~~~bash
$ (django-blog-env) ➜ django-blog> pip install django==2.2.8
$ (django-blog-env) ➜ django-blog> pip list
~~~

~~~bash
# Django admin 설치
$ (django-blog-env) ➜ django-blog> django-admin startproject config .
~~~

#### Python Interpreter 설정

: 만든 가상환경을 Interpreter로 설정 

([파이썬 가상환경 설치 가이드](https://jin9173.github.io/2019/12/18/etc/pyenv,virtualenv/))

![](https://user-images.githubusercontent.com/57488252/71049105-cdc71f00-2184-11ea-97c4-adb88330c4e0.png)

#### .gitignore파일을 생성하여 해당 소스 붙여넣기

1. [gitignore 사이트](http://gitignore.io/) 방문

2. gitignore할 파일 검색

   ![](https://user-images.githubusercontent.com/57488252/71050354-54c9c680-2188-11ea-82e0-5081445b46c2.png)

3. 생성 후 .gitignore파일에 복사하여 붙여넣기

   ![](https://user-images.githubusercontent.com/57488252/71050377-61e6b580-2188-11ea-9c9e-591a0d0a05f1.png)

   ![](https://user-images.githubusercontent.com/57488252/71050628-09fc7e80-2189-11ea-93cc-85dfb3ca0cf0.png)