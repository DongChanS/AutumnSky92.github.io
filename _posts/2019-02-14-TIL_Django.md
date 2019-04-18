---
layout: post
title: 2019.02.14 Django
subtitle: Django ORM
tags: django, python, ORM
comments: true
---
> Django Template Language


## Model

### Django ORM

DB의 반영 과정 (Migrations)

1. moels.py에서 정의 & 알려주는 과정
2. Migrate : db에 적용



- 정의

```python
# in models.py
from django.db import models

# Create your models here.
class Article(models.Model):
    
# 알아서 table name 생성 ,id도 자동임
```

#### makemigrations

```
$ python manage.py makemigrations
```

#### migrate

```
$ python manage.py migrate
```



#### shell

- runtime 데이터를 관리

```
$ python manage.py shell
------------------------------------------
>>> from articles.models import Article

### Create	
>>> a = Article(title="happy", content="hacking")
## 상태 변경 요청 / 테이블에 추가
>>> a.save()
## 한줄적기
>>> Article.objects.create(title='t2',content='c2')

### Read
>>> Article.objects.all()
# <QuerySet [<Article: Article object (1)>]>
## objects > query (in sqlalchemy)
## all() > SELECT * 느낌 (query set은 list랑 매우 비슷)
>>> Article.objects.first().title
# 'happy'

>>> Article.objects.filter(title="happy").all()
## filter > WHERE 느낌
>>> Article.objects.filter(title="happy").first()
## query set의 첫번째 요소

>>> Article.objects.all().count()
>>> Article.objects.get(pk=1)
>>> Article.objects.get(id=1)
>>> Article.objects.get(title="happy")

### Update
>>> a = Article.objects.get(pk=1)
>>> a.content = "Thursday"
>>> a.save()

### Delete
>>> a = Article.objects.get(id=2)
>>> a.delete()

### order : 간편하게 DB에서 ordering 하자(단, DB환경에 따라 성능이 다름)
>>> a = Article.objects.order_by('id')
>>> a = Article.objects.order_by('-id')
>>> Article.objects.all()[1:2]

>>> exit()
```



#### sqlmigrate

- 출력

```
$ python manage.py sqlmigrate articles 0001
# BEGIN;
~~~~~~~~~~
```



#### createsuperuser

```
$ python manage.py createsuperuser
```