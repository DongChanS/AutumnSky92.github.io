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

# Django에서 User를 관리해보자 2편 : 로그인 / 로그아웃

>참고) 이 포스팅은 Django Tutorial의 일부로 RESTful 구조로 진행하고 있지 않습니다.



### Log in 을 구현해보자

- urls.py 에 URL을 설정합니다.

```python
urlpatterns = [
	path('signup/', views.signup, name="signup"),
	path('login/', views.login, name="login"),
]
```



- views.py 에 로그인 동작을 진행할 함수를 생성해야합니다.
- 그 전에! Django가 제공하는 간편한 User검증 form인 AuthenticationForm을 import 합니다.

```python
from django.contrib.auth.forms import AuthenticationForm, UserCreationForm
# 회원가입 때 사용한 UserCreationForm 과 같은 위치에 존재합니다.
```



  - login 함수를 생성합니다. 이번에도 역시 GET 접근을 우선 고려하여 코드를 작성합니다.

```python
def login(request):
    if request.method == "POST":
        pass
    else:
        # GET 접근이면 User을 확인하는 form 객체를 생성하여 
        login_form = AuthenticationForm()
        
    context = {
        'login_form': login_form
    }
    # 생성된 form을 login.html에 함께 render합니다.
    return render(request, 'accounts/login.html', context)
```



- templates/accounts/login.html
- 로그인 정보를 입력 받을 페이지를 구성합니다.

```html
{% raw %}
<form method="POST">
    {% csrf_token %}
    {{ login_form }}
    <input type="submit" value="Submit"/>
</form>
{% endraw %}
```



여기까지 진행하면 로그인 정보를 입력 받을 준비가 완료 되었습니다!

계속해서 정보를 입력받고 로그인을 시키는 기능을 구현하겠습니다 :)



- views.py에 POST방식으로 로그인 시도가 이루어졌을 때의 동작을 구현합니다.

  - 이번에도 역시 Django가 제공하는 편리한 Login 기능을 사용하기 위해 import를 진행합니다.

```python
# views.py
from django.contrib.auth import login as auth_login
# 우리가 정의한 함수와 이름이 겹치지 않도록 auth_login이라는 이름으로 재정의 합니다.
```



  - 앞서 import 한 함수를 사용하여 로그인을 시키는 로직을 구현합니다.

```python
def login(request):
    if request.method == "POST":
        # POST 접근이면 User 정보를 확인합니다.
        # AuthenticationForm 클래스의 내부 구조에 맞게 인자를 차례로 넣고 객체를 생성합니다.
        # 1: request 자체 , 2: 입력된 데이터
        login_form = AuthenticationForm(request, request.POST)

        # 생성된 객체의 유효성 검사를 실행합니다.
        if form.is_valid():
        # 유효한 유저일 경우 로그인을 진행합니다.
        auth_login(request, form.get_user())
        # 다른 페이지로 render 혹은 redirect 해줍니다.
        return render(request, "accounts/index.html")
    else:
    	login_form = AuthenticationForm()

    context = {
    'login_form': login_form
    }
    return render(request, 'accounts/login.html', context)
```

  



로그인 기능 구현이 완료 되었습니다!

이제 로그아웃 기능을 구현해야하는데, 로그인 보다 훨씬 간단하게 가능합니다 :)

- 로그아웃 URL을 설정합니다.

```python
urlpatterns = [
    path('signup/', views.signup, name="signup"),
    path('login/', views.login, name="login"),
    path('logout/', views.login, name="logout"),
]
```



- 이번에도 역시 Django가 제공하는 편리한 Logout 기능을 사용하기 위해 import를 진행합니다.

```python
# views.py
from django.contrib.auth import logout as auth_logout
# 우리가 정의한 함수와 이름이 겹치지 않도록 auth_logout이라는 이름으로 재정의 합니다.
```



- 그리고 logout함수를 만들어주면 끝!

```python
def logout(request):
    auth_logout(request)
    # 로그아웃되면 로그인 페이지로 redirect합니다.
    return redirect('accounts:login')
```

  

로그인과 로그아웃 기능 구현이 끝났습니다 !