---
layout: post
title: 2019.04.25 Django
subtitle: Django Review
tags: django, python, auth, user
comments: true
version:
 - Django : 2.1.8
 - python : 3.6.7
---



# Django에서 User를 관리해보자

> Django에서는 accounts 라는 이름으로 app을 생성해서 User DataBase를 관리하는 것이 관례이다. 
>
> link 참고 [<https://docs.djangoproject.com/en/2.1/topics/auth/passwords/>]



Admin User 생성 : `$ python manage.py migrate`



`$ python manage.py migrate`
`$ python manage.py startapp accounts`

