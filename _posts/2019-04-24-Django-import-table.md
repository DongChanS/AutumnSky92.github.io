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



# Django User DataBase Import Cheat Table

```
django
	.shortcuts
		.render (import)
		.redirect (import)
		.get_object_or_404 (import)
	.views
		.decorators
			.http
				.require_POST (import) : POST접근을 요구하도록하는 데코레이터
	.db
		.models (import) : models.py 를 구성할 경우
			.Q (import) : Query 조건문을 여러개 사용할 경우
	.forms
		.ModelForm (import) : forms.py 를 구성할 경우
	.conf
		.settings
			.AUTH_USER_MODEL (import) : 유저 모델을 가져와야할 경우
	.contrib
		.auth
			.forms
				.AuthenticationForm (import) : 로그인 정보를 받을 폼
				.UserCreationForm (import)	: 회원가입 정보를 받을 폼
				.UserChangeForm (import)	: 회원 정보 수정 내용을 받을 폼
				.PasswordChangeForm (import)	: 비밀 번호 변경 내용을 받을 폼
			.login (import)		: 로그인 시키는 함수
			.logout (import)	: 로그아웃 시키는 함수
			.decorators
				.login_required (import)	: 로그인을 요구하도록하는 데코레이터
			.get_user_model (import) : 유저 모델을 가져와야할 경우
			.update_session_auth_hash (import) : 사용자 해쉬 정보를 업데이트 해줘야할 경우
```







