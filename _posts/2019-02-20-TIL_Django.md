---
layout: post
title: 2019.02.20 Django
subtitle: Django ORM CRUD
tags: django, python, ORM, CRUD
comments: true
---
# 2019.02.20

## CRUD

### Create

```python
## 두줄적기
# a라는 객체 생성
a = Article(title="happy", content="hacking")
a.save()
# 저장

## 한줄적기
Article.objects.create(title="happy",content="hacking")
```

### Read

```python
all_contents = Article.objects.all()
title = Article.objects.first().title
```

### Update

```python
a = Article.objects.get(pk=1)
# a = Article.objects.get(id=1)
a.content = "Thursday"
a.save()
```

### Delete

```python
a = Article.objects.get(id=2)
a.delete()
```



## 실습

### RESTful API

- "URL에 동사를 빼자" & "naming 규칙을 잘 지키자"

url 링크 쓸 때 앞 뒤로 '/' 를 붙여주자 (파일 디렉토리는 아님)

Read

| /articles/ | list |
| --- | --- |
| /articles/1/ | detail |

Create

| /articles/new/   | new   |
| --- | --- |
| /articles/create/ | create |

Update

| /articles/1/edit/ | edit |
| --- | --- |
| /articles/1/update/ | update |

Delete

| /articles/1/delete/ | delete |
| ------------------- | ------ |
|                     |        |



### 대장 문지기(urls.py)의 서브 문지기 만들기

```python
# in global urls.py
from django.contrib import admin
from django.urls import path, include
from articles import views

urlpatterns = [
    path('admin/', admin.site.urls),
    # 반복되는 부분을 분류해서 관리할 수 있음
    path('articles/', include('articles.urls')),
]
```

```python
# in sub urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index),
]
```



### POST 방식

- form action : 경로의 앞 뒤로 '/' 를 잘 써주자

  ```html
  {% raw %}
  <form action = "/articles/create/" method = "POST">
  {% endraw %}
  ```

- CSRF

  - 사이트에 접속중인 사용자인가? 를 확인하는 token을 관리해야함.
  - Django가 토큰을 발급 / 관리.

  ```html
  {% raw %}
  <form action = "/articles/create/" method = "POST">
  	{% csrf_token %}
  </form>
{% endraw %}
  ```

- views.py

  ```python
  def create(request):
      # DB에 저장
      title = request.POST.get('title')
      content = request.POST.get('content')
      articles = Article(title = title, content = content)
      articles.save()
      
      return redirect('/articles/')
  ```



### Django 스럽게 URL을 처리하자

```python
urlpatterns = [
    path('', views.index, name="index"),
    path('new/', views.new, name="new"),
    path('<int:article_id>/', views.detail, name="detail"),
]
```

- hard code 된 url들은 변경할 경우가 많음.

```html
{% raw %}
<a href="{% url 'new' %}">새 글 쓰기</a>
<!-- variable routing : 넣고싶은 정보를 차례대로 입력 -->
<a href="{% url 'detail' article.id %}">{{ article.title }}</a>
{% endraw %}
```

- views.py

```python
def create(request):
    # DB에 저장
    title = request.POST.get('title')
    content = request.POST.get('content')
    article = Article(title = title, content = content)
    article.save()
    
    # return redirect('/articles/')
    return redirect('detail', article.id)
```



- 장고는 모든 templates 폴더를 동일한 레벨로 탐색한다. (INSTALLED_APPS 순서)

  - 많이 공유하는 template 은 프로젝트 폴더 바로 밑에 생성(관례) 

  ```python
  # in settings.py
  # pwd
  TEMPLATES = [
      {
          'BACKEND': 'django.template.backends.django.DjangoTemplates',
          # DIRS 수정
          'DIRS': [os.path.join(BASE_DIR,'board','templates')],
          # os.path.join > os 경로에 맞게 구성해줌(window환경에서도 가능하도록)
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
  ```

- app의 templates은 같은 이름의 하위 폴더를 하나 더 만들어서 거기에 넣어두자