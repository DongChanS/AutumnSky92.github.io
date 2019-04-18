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
  <form action = "/articles/create/" method = "POST">
  ```

- CSRF

  - 사이트에 접속중인 사용자인가? 를 확인하는 token을 관리해야함.
  - Django가 토큰을 발급 / 관리.

  ```html
  <form action = "/articles/create/" method = "POST">
  	{% csrf_token %}
  </form>
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
<a href="{% url 'new' %}">새 글 쓰기</a>
<!-- variable routing : 넣고싶은 정보를 차례대로 입력 -->
<a href="{% url 'detail' article.id %}">{{ article.title }}</a>
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



# 2019.02.21

## 데이터 베이스 관계

### 1:1 관계

- 혼인
- 개인-주민번호

### 1:N 관계

- 1 to many
- has_many - belongs_to
- 학급 - 학생
- 게시글 - 댓글
- 유저 - 게시글
- 영화 - 리뷰

### M:N 관계

- many to many
- 수강신청 (수업 - 학생) (1:N - N:1)

### 관계없음





## 1:N 실습

- **N** 쪽에서 Foreign Key(**1** 의 PK)를 사용하여 **1** 에 접근가능

```python
# in models.py

class Comment(models.Model):
    content = models.TextField()
    # ForeignKey(어디에 속한지, 속한 데이터가 삭제되면 어떤 액션을 취할지)
    # CASCADE : 함께 삭제
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
```

```python
article = Article.objects.first()

comment = Comment(content="첫번째 글의 첫번째 댓글입니다.",article=article_id)
# id 요소에 객체 자체를 집어 넣어도 ㄱㅊ
comment = Comment(content="첫번째 글의 첫번째 댓글입니다.",article=article)


# Django ORM의 강력함

# id도 알아서 만들어주고!
Comment.objects.first().article_id

# 연결된 자료의 탐색도 간편!
Comment.objects.first().article
# 다음과 같은 문장
# Article.objects.get(pk=Comment.objecs.first().article_id)

Comment.objects.first().article.title
Comment.objects.first().article.content
Comment.objects.filter(article_id=1).all()

# 1 쪽에서도 N 쪽으로 탐색 가능
Article.objects.first().comment_set.all()
# >>> 직접 filter을 통해 N의 자료에 접근하는 것보다 훨씬 간편한 경우도 있기 때문에 HTML 문서에서도 쉽게 활용 가능!
# Django Template Language 에서는 all() > all 이렇게 쓰던데.. 이유가?
```



- 추가

```python
article = models.ForeignKey(Article, on_delete=models.CASCADE, related_name="comments")
```

​	- 1 > N 접근시 : <name>_set 이 아닌 지정한 이름으로 사용가능

- Django Template Language

```html
{% for a in article.comments.all reversed %}
{{ a.content }}
{% endfor %}
```





# 2019.03.13

## Django Project in Local (Pycharm)

- New Progject - Django
- `python manage.py startapp <appname>`

### 환경설정

- PyCharm pro install
  - Customize
    - BashSupport (install)
    - plugin - gitignore / material theme

- .gitignore

  ```
  # Virtual ENV
  venv/
  
  # Pycharm Auto Generated DIR
  .idea/
  
  # Python Auto Generated DIR
  __pycache__/
  
  # DB
  *.sqlite3
  ```

  

- 유효성 검사

- git
  - `mkdir -p` : 하위 디렉토리 생성
  - `cd -` : 뒤로가기



```python
from IPython import embed

embed()
# 일시정지 디버깅 느낌
```

- pycharm 단축키
  - shift + Enter : 다음 행으로 Enter
  - Alt + Enter : 수정 항목 확인
  - ctrl + alt + R : manage.py Mode
  - ctrl + D : 행 복사&붙여넣기
  - ctrl + G : 행 이동
  - shift + shift : 파일 열기(이동)
  - html ( for + tab / if + tab / block + tab) : HTML 자동완성
  - ctrl + alt + L : 자동 줄 맞춤
  - ctrl+ Shift + 방향키(up, down) : 행 복붙 이동
  - Alt + F12 : 터미널창



HTTP 상태 코드 처리하기

```
article = get_object_or_404(Article, id=article_id)
# TODO : 
# FIXME : 
```

DB에 영향이 있으면 POST로 만든다 생각하자

### RESTful API

- "URL에 동사를 빼자"
- "복수형 명사"





# 2019.03.14

> ### thin controller fat model

## SNS app

- settings .py (global)

```python
INSTALLED_APPS = [
    .
    .
    sns,
]
.
.
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
# MEDIA : 장고 권장 사용자 컨벤션
```

- urls.py (global)

```python
​```
.
.
​```
from django.conf.urls.static import static
from django.conf import settings

.
.
# Dev level 에서는 꼭 사용해야함. (DEBUG=True)
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_URL)
# DEBUG=False 일 경우 static은 빈 문자열 반환
```

>git add . && git commit -m 'typo'
>
>git add . ; git commit -m 'typo'
>
>mkdir -p sns/templates/sns
>
>touch base.html list.html detail.html
>
>pycharm 단축키 : https://lalwr.blogspot.com/2018/04/intellij.html

- models.py

```python
from django.db import models


class Posting(models.Model):
    content = models.TextField(default='')
    icon = models.CharField(max_length=20, default='')
    # MEDIA/posting/2019/03/14 : 차라리 이렇게 경로를 구체화하는 편이 성능 괜찮
    image = models.ImageField(blank=True, upload_to='postings/%Y%m%d')
    # Python null & blank
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return f'{self.id}: {self.content[:20]}'
```

> pip install pillow : image file 용?

- admin.py

```python
from django.contrib import admin
from .models import Posting


# admin page 에서 직접 수정할 필요가 없기 때문에 따로 명시를 해야 보임
class PostingModelAdmin(admin.ModelAdmin):
    # 개별 화면에서 확인만 가능
    readonly_fields = ('created_at', 'updated_at')
    # 리스트에서 보여질 Column 목록 설정
    list_display = ('id', 'content', 'created_at', 'updated_at')
    # 클릭 가능 Column 목록 설정
    list_display_links = ('id', 'content')


admin.site.register(Posting, PostingModelAdmin)
```



#### image resizing

- `$ pip install django-imagekit`
- settings.py

```pyth
INSTALLED_APPS = [
    'imagekit',
    .
    .
]
```

>  git commit -m "finish Posting Modeling"
>
> git rm -r --cached media/
>
> 폴더를 git 관리에서만 지움

#### Thumbnail

- models.py

```python
class Posting(models.Model):
    image_thumbnail = ImageSpecField(
        source='image',
        processors=[
            ResizeToFit(width=320, upscale=False)
        ],
        format='JPEG',
        options={
            'quality': 60
        },
    )
```

- ob.image_thumbnail.url 을 해야 CACHE 생성 (migrate 불필요 > 호출이 됐을 때에만 생성)



- Create 방법 3가지

  ```python
  #1
  posting = Posting()
  posting.content = request.POST.get('content')
  ...
  posting.save()
  
  #2
  posting = Posting(
      content=request.POST.get('content')
      ...
  )
  posting.save()
  
  #3 : save()가 필요없음
  posting = Posting.objects.create(
      content=request.POST.get('content'),
      icon=request.POST.get('icon'),
      image=request.FILES.get('image'),
  )
  ```

- migrate sns zero : migrate 한거 다 날릴수 있음.

- [github 코드](https://github.com/eduyu)

- [font awesome](https://fontawesome.com/)



> Duck-typing : "사용 방식이 비슷하면 그걸로 됐다. 그냥 사용하자" 개념

Pycharm에는 DB import 기능이 있음(sqlite3 명령어 불필요)

> c + tab : comment





# 2019.03.20

>나중에 AWS 서버 쓸 것 같음. (레드헷 / 우분투)
>
>1. ssh로 서버 직접 접근해서 작업 (vim)
>
>2. windows 에 vm (vagrant) 깔고 거기서 작업 > github > (ssh로 서버 접근) aws server 에서 pull

tag\. DevOps Wunderlist Trello

![IMG_0001](..\..\TIL\REFERENCE\imgs\IMG_0001.PNG)



#### Create a new workspace > Rails Tutorial 

- RAM이 좀 더 잡혀있음

- shell (zzu.li/install_pyenv)

```
$ sudo apt-get update

$ sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev
```

> sudo : 관리자 권한
> apt-get : 우분투의 쵸코 package management tool 

<http://zzu.li/install-pyenv>

```
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
exec "$SHELL"

##### pyenv --version:pyenv 1.2.9-19-g7d02b246


git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
exec "$SHELL"

##### cat ~/.bashrc
##### eval : 스크립트 동작시키는 함수

pyenv install 3.6.7
pyenv global 3.6.7

$ mkdir NEWPROJECT
$ cd NEWPROJECT


$ pyenv virtualenv 3.6.7 todo-venv 
$ pyenv local todo-venv
$ pip install django ipython django-extensions
$ django-admin startproject todoapp .

$ python manage.py startapp todos
$ python manage.py migrate
$ python manage.py createsuperuser
```

>  mr-auto > ml-auto : 왼쪽에 붙이기 > 오른쪽에 붙이기
>
> markup : ovenapp. balsamiq

auth 인증, 권한부여 : 본질적인 코어 기능은 장고가 이미 가지고 있음







### 로그인 관련 app을 따로 만들어보자

#### users 앱 생성

```
$ python manage.py startapp users
```

> no module... : install을 알려주면 해결
>
> python manage.py startapp users
>
> 기본 규약은 프레임 워크가 알아서 해줌(비밀번호 암호화 DB저장)
>
> Secure Hash Alogorithm (made by NSA)
>
> Cryptographic + Hash function



#### app 구성

- login
- logout
- profile
- register



#### Hash Function

> python hashlib

![IMG_0002](..\..\TIL\REFERENCE\imgs\IMG_0002.png)



#### 로그인 & 로그아웃 함수

- django 가 제공하는 login() 함수 & logout() 함수

```python
# users/views.py
from django.contrib.auth import authenticate, login, logout
```

- 제공되는 것들을 사용하는 이유 > 기본적인 보안 문제 or 오류 발생을 막는 처리가 되어 있음 + 간편함

- 자세한 내용은 users/views.py

 

> HTTP = Stateless
>
> - 상태가 없음





#### 메세지 출력

```python
# settings.py
MESSAGE_STORAGE = 'django.contrib.messages.storage.session.SessionStorage'
```

```python
# views.py
messages.success(request, "로그인에 실패했습니다.")
# request에 message를 담아서 보내는 형식
return redirect('users:login')
```

```html
<!-- base.html -->
<!-- 메세지를 보여줌 -->
{% if messages %}
<div class="alert alert-warning alert-dismissible fade show" role="alert">
    {% for message in messages %}
    {{ message }}
    {% endfor%}
    <button type="button" class="close" data-dismiss="alert" aria-label="Close">
        <span aria-hidden="true">&times;</span>
    </button>
</div>
{% endif %}
```



- 내일은? : 유저와 to do list를 1:N으로 연결하는 과정





# 2019.03.21

- model form & form class
- 시간이 남으면 aws 가입까지



## todos

### DB 설계

- content / completed(Flaging) / user_id(ForeignKey)

- User는 auth가 만들어주고, 관리해주는데 눈에는 안보임(패키징되어있음)
- Todos 와 User와 연결
- 1:N (1 > N_set 으로 한번에 뽑기, N > 1 접근하기 하는 방식이 가능하다는 걸 다시 한번 고려하자)



## shouts

- django-forms

  - forms.py 생성
    - models.py 에서 정의한 DB의 정보를 가져다 사용하기 때문에 여기서 알려주기만 하면 됨
  - Form Class 혹은 ModelForm Class를 사용하는 이유는 어제 로그인관련 함수들을 쓴 이유와 같음
    - 간편 + 유효성 검사

  ```python
  # shouts/forms.py
  # Form Class?
  from django import forms
  from .models import Shout
  
  
  # model이름 + Form : 관례
  # model에 기반하여, 장고가만들어주는 form
  class ShoutForm(forms.Form):
      title = forms.CharField()
      content = forms.CharField()
      
      
  # ModelForm Class?
  # - Form Class 보다 간편하게 사용가능
  # - views.py에서 코드를 더 짧게 가능
  # - 속성값을 주는 것도 가능
  class ShoutModelForm(forms.ModelForm):
      class Meta:
          model = Shout
          # fields 값을 알려주기
          fields = ['title', 'content']
          # 속성값 넣기
          widgets = {
              'title': forms.TextInput(
                  attrs = {
                      'class': 'form-control',
                      'placeholder': '제목을 입력해주세요.',
                  }
              ),
              'content': forms.Textarea(
                  attrs = {
                      'class': 'form-control',
                      'placeholder': '내용을 입력해주세요.',
                  }
              ),
          }
  ```

  ```html
  <!-- form 구성이 자동으로 됨 -->
  {% block body %}
  <form method="POST" action="{% url 'shouts:update' form.instance.id %}">
      {% csrf_token %}
      {{ form.as_p }}
      <input type="submit"/>
  </form>
  {% endblock %}
  ```

  

> 꿀팁
>
> python `in` operator는 set으로 바꾸고 쓰면 빠름
>
> html에서 {% debug %}로 다양한 값을 확인 가능





# 2019.04.08

><https://taegon.kim/>
>
><https://www.gitignore.io/>

### Instagram

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
pip install Django==2.1.7

django-admin startproject instagram .
```

git commit 의 순간/내용

- commit history만으로 작업 현황이 보이도록 작성
- 하나의 작업 논리로 쪼개서 작성
- fetch & merge == pull





## 새로운 서비스 만들기(BDD 계획 > TDD 개발)

> 에자일한 단계에서의 구상

1. 무엇을 하는 서비스 인가?

   ex) 사용자가 사진을 업로드하고, 관리하는 서비스

2. 사용자가 어떻게 서비스를 활용하는가?

   ex) templates에 대한 mock-up

3. 데이터 모델링 ( 어떤 정보들을 관리할 것 인가, 어떻게 관리할 것 인가 등등)

#### UML







# 2019.04.11

>  이제 Monolithic 구성은 잘 안함

Amazon Web Service

- Django 코드 > EC2 or EB

- DB > RDS

- Static(정적 파일 : 사진) > S3

- CloudFront : CDN



#### git log

![캡처](..\..\TIL\REFERENCE\imgs\캡처.PNG)

(origin/master) > 가장 최근에 push 했던 커밋 로그 > 이 로그로부터 현재 로그를 비교하여 커밋 상태를 알려줌







#### python image control

$ pip install pillow

settings.py

```python
# 미디어 파일들이 불릴 url
MEDIA_URL = '/media/'


# os.path.join(BASE_DIR, 'media')
# os에 맞게 BASE_DIR (기준 root)로 부터 media 경로를 반환해줌

# MEDIA_ROOT : 실제 저장장소
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```



urls.py

```python
from django.conf.urls.static import static
# from . import settings : 이렇게 하면 장고스럽지 않음 ㅎ
from django.conf import settings
# django.conf -> 장고에 관련된 모든 설정이 저장된 위치 (데이터 은닉의 원칙)
# settings.py의 내용뿐만 아니라 기본 장고의 모든 설정이 저장되어 있음

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
# static(통과시키고자 하는 url, 실제 저장 장소)
```



views.py

```python
form = PostForm(request.POST, request.FILES, instance=post)
```





#### log in  세션 관리

posts.models.py

```python
from django.db import models
#from django.contrib.auth.models import User
# 직접 접근하는 방식
from django.conf import settings
# django 스러운 방식

class Post(models.Model):
    content = models.CharField(max_length=150)
    image = models.ImageField(blank=True)
    # user = models.ForeignKey(User, on_delete=models.CASCADE)
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    
    def __str__(self):
        return self.content
        
```



![캡처2](C:\Users\student\TIL\REFERENCE\imgs\캡처2.PNG)

```
(insta-venv) ssafycm:~/workspace/INSTAGRAM (master) $ python manage.py makemigrations
You are trying to add a non-nullable field 'user' to post without a default; we can't do that (the database needs something to populate existing rows).
Please select a fix:
 1) Provide a one-off default now (will be set on all existing rows with a null value for this column)
 2) Quit, and let me add a default in models.py
Select an option: 1
Please enter the default value now, as valid Python
The datetime and django.utils.timezone modules are available, so you can do e.g. timezone.now
Type 'exit' to exit this prompt
>>> 1    
Migrations for 'posts':
  posts/migrations/0003_post_user.py
    - Add field user to post
```

이전에 없는 관계를 정의 해야하는 순간 (이전의 데이터를 연결해야하는 상황)

1) 옵션 1을 선택 : 1 > 2) Default를 준다 : 1 (admin 유저에게 전부 연결 : 전부 admin 유저가 만들었다고 알려줌)





```
# 분기 작업 해보자

​```
# 작업 시작전에는 항상 Branch를 깨끗하게 정리하자
$ git branch john
$ git branch -D john


# 작업 시작 전에는 항상 원격 저장소의 로그와 맞춰주자
$ git checkout master
$ git pull origin master

### 진실은 언제나 origin / master


# 브랜치를 나누어 작업하자
$ git branch john
$ git checkout -b john


# coding & git add & git commit 작업 후에 내 branch에 push하자
$ git push origin john


# 코드를 확인한 후에
# Github에서 Compare & pull request 를 통해
# 작업한 내용을 merge해서 master로 만든다

​```
```







# 2019.04.12

>데이터베이스 관계 3 (M:N)

> M V T : M이 가장 많은 일을 하고, V는 중간자 역할만 하는게 좋음 : model FFFFFAAAAATTTTT    

![IMG_0025](..\..\TIL\REFERENCE\imgs\IMG_0025.PNG)

- 1 : N

  - 1에서 N으로 간편하게 접근하자 : one.N_set

- M : N

  - 1:M 과 N:1 사이에 새로운 Table이 존재
  - django는 알아서 table을 해줌 (models.py에서 새로운 테이블 만들어 줄 필요가 없음)

  

```
mkdir SCHOOL
cd SCHOOL
pyenv virtualenv 3.6.7 school-venv
pyenv local insta-venv
pip install --upgrade pip
pip install Django==2.1.8
pip install django ipython django-extensions
django-admin startproject school .
```

```python
from django.db import models
from faker import Faker
import random

fake = Faker('ko_KR')

class Student(models.Model):
    name = models.CharField(max_length=30)
    student_id = models.IntegerField()
    
    # 객체 생성없이 사용 가능
    @classmethod
    # 더미 데이터를 자동으로 넣어주는 친구
    def dummy(cls, n):
        for _ in range(n):
            cls.objects.create(name=fake.name(), student_id=random.randint(2000, 2020))

    
    
    # 객체 생성 후에, 객체가 사용하는 형식
    def __str__(self):
        return self.name
    
class Lecture(models.Model):
    title = models.CharField(max_length=100)
    
    # def __str__(self):
    #     return self.title
    
class Enrollment(models.Model):
    lecture = models.ForeignKey(Lecture, on_delete=models.CASCADE)
    student = models.ForeignKey(Student, on_delete=models.CASCADE)
    
    
    def __str__(self):
        # self > Enrollment 객체
        # self.student_set
        return f"{self.student.name} 님이 {self.lecture.title} 과목을 수강하였습니다."
```

- join table에 중복 방지 처리가 되어 있지 않음!



### Django ORM이 지원하는 M : N (ManyToManyField)

- 매우 간편하고 중복 방지 처리가 되어있음

- meta data : 데이터(data) 에 대한 데이터(meta data)

  > django model meta options

```python
# 이번엔 Django가 지원하는 M:N 구조로 진행
class Client(models.Model):
    name = models.CharField(max_length=30)
    
    # class Meta:
    #     ordering = ['name', ]
    @classmethod
    def dummy(cls, n):
        for _ in range(n):
            cls.objects.create(name=fake.name())
        
class Resort(models.Model):
    name = models.CharField(max_length=30)
    # models.ManyToManyField(모델 클래스 이름, 온딜릿도 필수 아님)
    clients = models.ManyToManyField(Client)
    # 포함관계는 이해하기 쉬운 쪽에(Client or Resort 상관없음) 쓰자, 단! 순서(선언) 중요 (문법)
    
    @classmethod
    def dummy(cls, n):
        for _ in range(n):
            cls.objects.create(name=fake.company())
```

- sql문을 구경해보자

```
python manage.py makemigrations
python manage.py migrate
python manage.py sqlmigrate sugangs 0002
```

- Shell에서 확인해보자

```python
python manage.py shell_plus
###

Resort.dummy(5)
Client.dummy(20) 
resort.clients
resort.clients.all()
resort.clients.add(man)
resort.clients.all()
resort.clients.remove(Client.objects.last())

# 매우 간편 + 중복 방지도 되어있음!
```





# 2019.04.15

## Like 기능추가

<https://docs.djangoproject.com/en/2.2/topics/auth/default/>





# 2019.04.16

> 데이터 베이스 유효성 검사

## Comment & Follow

### Comment : 댓글 기능 추가

1. (content, ForeignKey 2개)
2. 이전 코드는 참고 금지
3. 공식 문서 & 구글 검색
4. 정리



#### 1. models.py

#### 2. views.py

#### 3. forms.py

#### 4. list.html



## 회원 정보 수정 기능





# 2019.04.17

### 1:1 Relationship

- 1:1 > 1:N 관계에서 uniq 한 접근 / 그냥 column 값으로 추가하는게 일반적
- 다만, 기존 테이블의 유저 정보가 방대한 환경에서 새로운 column을 추가하고 싶은 경우에 사용하면 좋음

- 또한, Django에서 제공해주는 user_model을 사용하고 있기 때문에 직접 수정하기 어려움. > OneToOne으로 접근하는게 간편. *(+ 참조 방식은 1:N과 비슷하지만 ~~~_set 을 만들지 않는다!)*

> python manage.py sqlmigrate accounts 0001





base_user.py

- AbstractBaseUser
- AbstractUser(AbstractBaseUser)
- Django 제공 : User(AbstractUser)
- Custom_ : User(AbstractUser)



