---
title: "Django User (2)"
date: 2020-04-14T16:15:29+09:00
weight: 21
chapter: true
pre: "<b>5. </b>"
tags : ["django", "sign in", "sign out", "sign up"]
---

### User 관리(change, customizing)

---

> customizing 방법은 여러가지가 있지만, 앞서 사용했던 방법과 다르게 여기서는 AbstractUser를 상속 받아서 User를 customizing후 사용함
>
> UserChangeForm도 마찬가지로 ChangeForm을 상속받아 customizing 해서 사용함
>
> bootstrap을 사용하기 위해 widget에 attrs도 추가

#### 1. models.py에서 AbstractUser를 상속

```python
from django.db import models
from django.contrib.auth.models import AbstractUser # AbstractUser를 가져옴
# Create your models here.

class User(AbstractUser): # User 모델을 AbstractUser 상속받음
    # 추가할 내용을 작성
    Korean_name = models.CharField(max_length=20, blank=True)
    address = models.CharField(max_length=100, blank=True)
    phone_number = models.CharField(max_length=11, blank=True)
```

#### 2. settings.py 에서 AUTH_USER_MODEL 추가(공식문서 참조)

```python
# settings.py 에 AUTH_USER_MODEL = 'app 이름.User'
AUTH_USER_MODEL = 'accounts.User'
```

#### 3. bootstrap을 위한 class:  form-control 추가를 위하여 forms.py 작성

```python
from django.contrib.auth.forms import UserCreationForm # 기본 제공하는 modelForm을 사용하기 위해
from django import forms
from .models import User		# 커스터마이징한 User 모델을 불러옴
from django.contrib.auth.forms import UserChangeForm  # User 정보를 수정하기 위해
from django.contrib.auth import get_user_model		# User 정보를 수정할 때 정보를 가져오기 위해

class SignupForm(UserCreationForm):		# 상속받아 사용할 클래스 선언
    def __init__(self, *args, **kwargs):	# init을 통해 존재하던 필드의 정보를 수정
        super().__init__(*args, **kwargs)
        class_update_fields = ['password1', 'password2']
        for field_name in class_update_fields:
            self.fields[field_name].widget.attrs.update({
                'class': 'form-control'
            })

    class Meta:					 # Meta 클래스를 통해 새로 추가한 필드의 attrs에서 form-control 추가
        model = User			 # Bootstrap을 사용하기 위해서 추가했음
        fields = (
            'username',
            'Korean_name',
            'address',
            'phone_number',
        )
        widgets = {
            'username': forms.TextInput(
                attrs={
                    'class': 'form-control',
                }
            ),
            'Korean_name': forms.TextInput(
                attrs={
                    'class': 'form-control',
                }
            ),
            'address': forms.TextInput(
                attrs={
                    'class': 'form-control',
                }
            ),
            'phone_number': forms.TextInput(
                attrs={
                    'class': 'form-control',
                }
            )
        }
class CustomUserChangeForm(UserChangeForm):		# User 정보를 update하기 위하여 
    class Meta:
        model = get_user_model()
        fields = ['username', 'Korean_name', 'address', 'phone_number']

```

> 메타 클래스에서 password widget을 설정해도 전혀 변화가 없음. 공식 문서에 따르면 ModelForm 클래스에서 명시적으로 선언된 필드는 메타 클래스에서 설정된 속성의 영향을 받지 않는다고 함.

#### 4. Migration 실행

- model의 변경사항이 있기 때문에 migration을 진행한다

  ```shell
  $ python manage.py makemigrations
  $ python manage.py migrate
  ```

#### 5. views.py와 urls.py에 Update를 추가

```python
#### views.py
from django.shortcuts import render, redirect
from .forms import CustomUserChangeForm   # customizing 한 changeform을 불러옴

def update(request, pk):   # 각각의 유저 정보를 얻고 수정하기 위해 pk값을 받아옴
    if request.method == 'POST':
        form = CustomUserChangeForm(request.POST, instance=request.user)   # instance를 가져와야 기존에 있던 정보를 토대로 수정할 수 있음, 없을경우 빈칸으로 시작됨
        if form.is_valid():
            form.save()
            return redirect('accounts:index')   # 수정을 완료하면 저장 후 인덱스 페이지로 redirect
    else:
        form = CustomUserChangeForm(instance=request.user)
    context = {
        'form': form
    }
    return render(request, 'accounts/update.html', context)

```

1. [Django 기초](https://dongyeopgu.github.io/cont/django_start.html)
2. [Django CRUD](https://dongyeopgu.github.io/cont/django_crud.html)
3. [Django Paginator](https://dongyeopgu.github.io/cont/django_paginator.html)
4. [Django User(1)](https://dongyeopgu.github.io/cont/django_login.html)
5. [Django Comment](https://dongyeopgu.github.io/cont/django_comment.html)

