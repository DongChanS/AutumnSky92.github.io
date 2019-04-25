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

# Django에서 User를 관리해보자 1편 : 회원가입

>참고)
>
>1. 이 포스팅은 Django Tutorial의 일부로 RESTful 구조로 진행하고 있지 않습니다.
>
>2. Django에서는 accounts 라는 이름으로 app을 생성해서 User DataBase를 관리하는 것이 관례입니다.  (공식 문서 참고)

### accounts APP

> (settings.py 와 메인 urls.py에 앱 생성을 알리는 것을 제외하면)
>
> 이 파트에서 작업하는 디렉토리는 대부분 accounts/ 에서 이루어집니다.

accounts 앱을 생성합니다.

- `$ python manage.py startapp accounts`

  - Django가 제공하는 다양한 기능들을 간편하게 사용하기 위해서 Convention을 지켜줍니다.

- 앱을 Django에게 알려줍니다.

  ```python
  # settings.py
  INSTALLED_APPS = [
      .
      .
      .
      'accounts',
  ]
  ```

- 등록한 앱의 URL을 메인 URL에서 설정합니다.

  ```python
  # (main) urls.py
  from django.contrib import admin
  from django.urls import path, include
  
  urlpatterns = [
      path('admin/', admin.site.urls),
      path('accounts/', include('accounts.urls')),
  ]
  ```

  

아직까지 관리할 유저가 없기 때문에 회원가입 기능을 먼저 구현해봅시다.

- urls.py 생성: 접근 URL(GET accounts/signup/)

  ```python
  from django.urls import path
  from . import views
  
  app_name = 'accounts'
  
  urlpatterns = [
      path('signup/', views.signup, name="signup"),
  ]
  ```

- views.py

  - 드디어 장고가 제공하는 편리한 기능 중 하나인 `auth.forms`를 사용할 차례입니다. ([auth.forms를 사용하는 이유]('#'))
  - 사용자를 생성하는 Form인 UserCreationForm 을 추가로 import 해줍니다.

  ```python
  from django.contrib.auth.forms import UserCreationForm
  ```

  - 우선은 사용자가 GET 접근을 한다고 가정하고, function을 구성합니다.

  ```python
  def signup(request):
      if request.method == "POST":
          # POST 접근은 조금 뒤에 설명하겠습니다. 일단 pass!
          pass
      else:
          # 사용자가 GET으로 접근했을 때!
          # UserCreationForm 객체를 생성합니다.
          uc_form = UserCreationForm()
          context = {
              'uc_form': 'uc_form'
          }
          # 생성된 객체를 회원가입 페이지로 넘기면서 render해줍니다.
          return render(request, "accounts/signup.html", context)
  ```

- templates/accounts/signup.html

  - GET 접근을 통해 render할 회원가입 페이지를 생성합니다.

  ```html
  {% raw %}
  <form>
      {{ uc_form }}
      <input type="submit" value="Submit"/>
  </form>
  {% endraw %}
  ```




여기까지하면 껍데기는 완성입니다 :) 이제 HTML에서 전달 받은 값을 DB에 적용하는 구성으로 넘어 가겠습니다.

- templates/accounts/signup.html

  - 사용자 정보 입력 form에선 POST로 접근이 이루어져야 하기 때문에 method와 csrf token을 추가합니다.

  ``` html
  {% raw %}
  <form method="POST">
      {% csrf_token %}
      {{ uc_form }}
      <input type="submit" value="Submit"/>
  </form>
  {% endraw %}
  ```



이렇게 하면 HTML에서 POST 방식으로 request를 현재 url(accounts/signup/)에 넘겨주게 됩니다.

이제 전달 받은 request를 통해 사용자 정보를 DB에 적용하겠습니다.

- views.py

  ```python
  def signup(request):
      if request.method == "POST":
          # POST 접근이 이루어지면
          # request에 POST로 담긴 정보를 다시금 UserCreationForm의 인자로 넣어주고 객체를 생성합니다.
          user_form = UserCreationForm(request.POST)
          
          # 생성된 객체의 유효성 검사를 진행하고,
          if user_form.is_valid():
              # 유효한 데이터이면 저장합니다.
              user_form.save()
              
              # 회원가입을 환영하는 페이지를 만들고, render해 줍니다.
              return render(request, "accounts/index.html")
      else:
          context = {
              'uc_form': UserCreationForm()
          }
          return render(request, "accounts/signup.html", context)
  ```

- templates/accounts/signup.html

  ```html
  {% raw %}
  <h1> 환영합니다! </h1>
  {% endraw %}
  ```



회원가입 기능 구현이 끝났습니다 :)