---
layout: post
title: Instagram Clone Coding
subtitle: Django Project
tags: django, python
comments: true
version:
 - Django : 2.1.8
 - python : 3.6.7
---

# Instagram Clone Coding
> 개발은 `cloud9` 환경에서 진행됩니다.

### Start Project!

- 개발 환경 구성
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
mkdir INSTAGRAM
cd INSTAGRAM
pyenv virtualenv 3.6.7 insta-venv 
pyenv local insta-venv
pip install --upgrade pip
pip install Django==2.1.8
pip install ipython django-extensions
django-admin startproject instagram .
```

설치가 완료 되면 ALLOWED_HOSTS를 변경해줍니다.
- settings.py

```python
ALLOWED_HOSTS = ["*"]
```


### Admin User를 생성하여 관리자 페이지를 확인해봅시다.

Initialization 을 위해서 migrate를 먼저 실행합니다.

- `python manage.py migrate`

관리자를 생성합니다.

- `python manage.py createsuperuser` 

서버를 실행하고 관리자 페이지로 이동합니다. (<main_page 주소>/admin)

- `python manage.py runserver $IP:$PORT` 


> Internationalization도 가능합니다.
>
> ```python
> # settings.py
> LANGUAGE_CODE = 'ko-kr'
> 
> TIME_ZONE = 'Asia/Seoul'
> ```

기본 개발 환경 구성이 완료되었습니다!
