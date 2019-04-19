---
layout: post
title: 2019.02.21 Django
subtitle: Django ORM CRUD
tags: django, python, ORM, CRUD
comments: true
---
## 데이터 베이스 관계

### 1:1 관계

- 혼인(부부사이)
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
{% raw %}
{% for a in article.comments.all reversed %}
{{ a.content }}
{% endfor %}
{% endraw %}
```

