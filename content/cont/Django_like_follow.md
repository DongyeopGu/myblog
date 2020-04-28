---
title: "Django M:N model"
date: 2020-04-28T22:54:07+09:00
weight: 23
chapter: true
pre: "<b>7. </b>"
tags: ["django","like", "follow", "M:N"]
---

## Django M:N model

> Django에서 좋아요 기능이나 팔로우 기능처럼 M:N 관계 만들기
>
> ManyToManyField 이용

- 좋아요 기능을 위한 model 추가(게시글 모델)

```python
 # 좋아요 기능을 위한 article_like_users 테이블 생성
class 좋아요 기능을 추가할 클래스 명:
    like_users = models.ManyToManyField(settings.AUTH_USER_MODEL, related_name='like_articles')
 # 팔로우 기능을 위한 
```

- 팔로우 기능을 위한 model 추가(User 모델을 Customizing)

```python
class User(AbstractUser):
    followers = models.ManyToManyField(settings.AUTH_USER_MODEL, related_name='followings')
```

- 게시글 app의 views.py에서 좋아요 기능 설정

```python
def like(request, pk):
    article = get_object_or_404(Article, pk=pk)
    # 좋아요를 누르면, >> DB에 존재할 경우
    if article.like_users.filter(id=request.user.pk).exists():
        article.like_users.remove(request.user) # 좋아요 취소
    else:		#좋아요 누르지 않았다면
        article.like_users.add(request.user)
    return redirect('articles:detail', article.pk)
```

- 계정 관련 app의 views.py에서 팔로우 기능 설정

```python
def follow(request, username):
    User = get_user_model()
    user = get_object_or_404(User, username=username)
    if user != request.user:
        if user.followers.filter(username=request.user.username).exists():
            user.followers.remove(request.user)
        else:
            user.followers.add(request.user)
    return redirect('accounts:profile', user.username)
```

- html 작성

> 좋아요를 위한 html

```html
...
  {% for article in articles %}
    <ul>
      <li>{{ article.title }}</li>
    </ul>
	<!-- 좋아요를 눌렀을 경우 -->
    {% if request.user in article.like_users.all %}
    <a href="{% url 'articles:like' article.pk %}">
		<!-- 좋아요 표시를 위한 아이콘이나 사진을 넣음 -->
	</a>
	<!-- 좋아요를 다시 눌러 취소할 경우 -->
    {% else %}
    <a href="{% url 'articles:like' article.pk %}">
		<!-- 좋아요 표시를 위한 아이콘이나 사진을 넣음 -->
    </a>
    {% endif %}
	<!-- 좋아요를 누른 사람을 카운트하기 위해 -->
    <p style="display: inline;">{{ article.like_users.count }}명이 좋아합니다.</p>
    <hr>
  {% endfor %}
...
```

> 팔로우 기능을 위한 html

```html
...
<!-- 자기 자신을 팔로우 하지 않기 위해 request 유저가 팔로우할 user가 아닐 경우-->
{% if request.user != user %} 
    {% if request.user in user.followers.all %}<!--팔로우 상태일 경우 -->
      <a href="{% url 'accounts:follow' user.username %}">팔로우 취소</a>
    {% else %}
      <a href="{% url 'accounts:follow' user.username %}">팔로우</a>
    {% endif %}
  {% endif %}
  <p>팔로워: {{ user.followers.all.count }}명</p>
  <p>팔로잉: {{ user.followings.all.count }}명</p>
  <hr>
  <h3>작성한 글 목록</h3>
  {% for article in user.article_set.all %}
  	<p>{{ article.title }}</p>
  {% endfor %}
  <h3>좋아요한 글 목록</h3>
  {% for article in user.like_articles.all %}
    <p>{{ article.title }}</p>
  {% endfor %}
...
```

> 이 html에서 `template tag`인 `with`를 사용하면 `user.followers.all`을 조금더 간단하게 이름을 변경해 사용 가능하다

----

1. [Django 기초](https://dongyeopgu.github.io/cont/django_start.html)
2. [Django CRUD](https://dongyeopgu.github.io/cont/django_crud.html)
3. [Django Paginator](https://dongyeopgu.github.io/cont/django_paginator.html)
4. [Django User(1)](https://dongyeopgu.github.io/cont/django_login.html)
5. [Django User(2)](https://dongyeopgu.github.io/cont/django_change_user.html)
6. [Django Comment](https://dongyeopgu.github.io/cont/django_comment.html)

