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

> Django에서는 accounts 라는 이름으로 app을 생성해서 User DataBase를 관리하는 것이 관례입니다. 
>
> link 참고 [<https://docs.djangoproject.com/en/2.1/topics/auth/passwords/>]

- Basic Script (cloud 9 / workspace)

```
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
exec "$SHELL"
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
exec "$SHELL"
pyenv install 3.6.7
pyenv global 3.6.7
mkdir NEWPROJECT
cd NEWPROJECT
pyenv virtualenv 3.6.7 pj-venv 
pyenv local pj-venv
pip install --upgrade pip
pip install Django==2.1.8
pip install ipython django-extensions
django-admin startproject newproject .
```

- settings.py

```python
ALLOWED_HOSTS = ["*"]
```



#### Admin User를 생성하여 관리자 페이지를 확인해봅시다.

Initialization 을 위해서 migrate를 먼저 실행

- `python manage.py migrate`

관리자 생성

- `python manage.py createsuperuser` 

서버 실행

- `python manage.py runserver $IP:$PORT` 

관리자 페이지로 이동

- <main_page 주소>/admin

> Internationalization도 가능합니다.
>
> ```python
> # settings.py
> LANGUAGE_CODE = 'ko-kr'
> 
> TIME_ZONE = 'Asia/Seoul'
> ```



#### accounts 앱을 생성하여 User Database를 관리해봅시다.

accounts 앱 생성

- `$ python manage.py startapp accounts`

