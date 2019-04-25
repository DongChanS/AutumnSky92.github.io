---
layout: post
title: 2019.02.13 Django
subtitle: Django intro
tags: django, framework, python
comments: true
---
> 본 글은 2월 13일에 작성했고,  re-posting을 거치면서 편집했습니다. 

[Django 공식 홈페이지](https://www.djangoproject.com/)

## Django?
Django는 Python 언어 기반의 웹 프레임워크로, 쉽게 생각하면 개발자가 앱의 기능 구현에만 집중할 수 있도록 다양한 기능과 기본 뼈대를 제공하는 코드 묶음이라 할 수 있겠다.

* * *
질문 : 그래서 프레임워크(Framework)가 뭔데?

답변 : 반드시 구성해야할 복잡한 구조가 있다고 생각해보자. 이 구조는 매번 개발을 진행 할 때마다 반복해서 구성하고, 같은 패턴으로 코딩을 해야한다.  사람마다 다를 수 있겠지만, '이걸 매번 반복하기 귀찮다'라는 생각으로 부터 출발한 것이라 이해하면 좋을 것 같다. 후술 하겠지만, 프레임워크의 장점은 단순히 '귀찮음을 해결한다' 뿐만아니라 '기본적인 실수를 프레임워크가 커버한다'는 장점도 존재할 수 있다. 지금은 간단히 이해하고 넘어가자.
* * *
질문 : 어짜피 "개-발-자"라면 다 할 수 있어야하는거 아닌가? 굳이 사용해야하는가?

답변 : 틀린말이 아니다. 개발자라면 스스로 바닥부터 쌓아 올릴 수 있는 지식을 겸비하는 것이 반드시 필요하다. 그러나, 다 할 수 있다고 해서 꼭 해야하는 일은 아니라고 생각한다. 오히려 미리 구성된 코드단에서 자칫 지나칠수 있는 기본 설정이나, 오류같은 것을 프레임워크 단에서 제어 할 수 있기 때문에 일률이 높아지고 실수가 줄어 들 수 있는 것이라 생각한다. 모든 것은 사용하기 나름.
* * *
질문 : Django 말고도 많잖아?

답변 : Python 기반의 프레임워크 중에서는...[할많하않](http://hotframeworks.com/languages/python)
* * *


## Django의 기본 동작 구성

Clients의 요청(Request)을 받으면 응답(Respond)을 하는 것은 웹서비스의 기본이다. 여기서 요청은 사용자의 일정한 행동(Action)에 의해 발생하고, 응답은 그 행동에 대한 출력을 말한다.

Django는 다음의 3개의 주요 개념을 통해 동작이 이루어진다.

- Model : 데이터베이스 관리
- Template : 사용자가 보는 화면
- View : 중간관리자

>  갑자기 뜬금없이 튀어나온 친구들이지만, 서비스 구성에서 가장 중요한 친구들이니 눈인사 정도는 나눠보자



## Template & View

[github.com.djohnkang/install_pyenv.sh](https://gist.github.com/djohnkang/7d7ba4854b505fe42236fccd8ee9788c)

Django 프로젝트에 앞서 pyenv 설치를 권장한다.  (러거시 코드(Legacy Code) 관리를 위함)

>Legacy Code & Semantic Versioning
>
>ex. BREAKING.FEATURE.FIX



#### New  Workspace (Virtual)

```
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
exec "$SHELL"
pyenv --version

git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
exec "$SHELL"

pyenv global 3.6.7
```



#### New Project

```
### 새로운 프로젝트 디렉토리 이름은 대문자로!
mkdir NEWPROJECT
cd NEWPROJECT


### 가상환경 생성
pyenv virtualenv 3.6.7 <새로운 가상환경 이름>-venv 

### 자동으로 가상환경이 로드 되도록 설정
pyenv local newproject-venv

### 장고 설치
pip install django

###  <새로운 프로젝트 이름 : 소문자> . 마지막에 점을 꼭 찍어서 경로를 분명히 해주자
django-admin startproject newproject .
```




- 파일 구성


```
.
├── manage.py 			// 편집할 일 거의 없음
└── practice
    ├── __init__.py 	// 편집할 일 거의 없음
    ├── settings.py
    ├── urls.py
    └── wsgi.py			// 배포할 때 사용
```


- 프로젝트 생성 후에 웹 주소를 설정해준다.

```python
# in settings.py
# 웹 주소 설정
ALLOWED_HOSTS = ["django-practice-ssafycm.c9users.io"]
### https://는 제외
```



#### 서버 구동

```
python manage.py runserver $IP:$PORT
```

internationalization : i18n



#### 앱 생성

- 명령

```
$ python manage.py startapp pages
```

- 구성


```
.
├── db.sqlite3
├── manage.py
├── pages
│   ├── __init__.py				// 거의 안씀
│   ├── admin.py				// 관리자용 페이지에서 활용
│   ├── apps.py					// 거의 안씀
│   ├── migrations
│   │   └── __init__.py			// 거의 안씀
│   ├── models.py
│   ├── tests.py
│   └── views.py
```

- 앱을 만들고 나면 알려줘야함

```python
# in settings.py
INSTALLED_APPS = [
	# .
    # .
    # .
    # 끝나는 요소에 ,를 꼭 붙여야함
    # trailing comma
    'pages',
]
```



### views.py & urls.py

#### Flask와의 차이점
1) route > urls.py 에 views를 import 하고 경로와 이름을 알려줌
2) request > 함수의 인자로 사용됨
3) render > request를 함께 보내줌

> Flask html > jinja
>
> Django html > 거의 비슷

0. index
     ```python
     # in views.py
     def index(request):
         return render(request, 'index.html')
     ```

1. isval

   ```python
   
   ```

2. variable routing

   ```python
   # in views.py
   
   def cube(request,number):
       # 사용자로부터 url로 입력받은 값을 세제곱합니다.
       result = number ** 3
       return render(request, 'cube.html', {'result':result})
       
   def ispal(request,string):
       result = False
       if string == string[::-1]:
           result = True
       data = {'result': result}
       return render(request, 'ispal.html', data)
   ```

   ```python
   # in urls.py
   
   urlpatterns = [
       path('cube/<int:number>', views.cube),
       path('ispal/<str:string>', views.ispal),
   ]
   ```

   

   
##### 환경변수

```
c9 ~/.bashrc
source ~/.bashrc
### 가상환경이 중복으로 생성 > 하나를 꺼주면 됨
source deactivate
```

   



