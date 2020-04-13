---
title: "Django User"
weight: 20
chapter: true
pre: "<b>4. </b>"
tags : ["django", "sign in", "sign out", "sign up"]
---

### User 관리(sign in, out, up)

---

> 로그인 기능을 넣는 방법
>
> model을 만들 필요 없이 django에서 제공하는 user 모델을 사용함
>
> bootstrap 사용을 기반으로 작성함

#### 1. accounts라는 새로운 app을 작성

#### 2. 기본 제공되는 User 모델을 사용하기 때문에 forms.py만 작성 

```python
from django.forms import ModelForm
from django.contrib.auth.models import User     # django에서 기본 제공하는 model이 있음.
from django import forms


class SignupForm(ModelForm): #회원가입을 제공하는 class이다.
    username = forms.CharField(
            max_length=20,
            min_length=6,
            widget=forms.TextInput(
                    attrs={
                        'class': 'form-control'
                    }
                )
        )
    password = forms.CharField(
            max_length=16,
            min_length=8,
            widget=forms.PasswordInput(
                    attrs={
                        'class': 'form-control',
                        'placeholder': '8글자 이상, 16글자 이하로 작성해주세요.'
                    }
                )
        )
    password_confirm = forms.CharField(
            max_length=16,
            min_length=8,
            widget=forms.PasswordInput(
                    attrs={
                        'class': 'form-control',
                        'placeholder': '8글자 이상, 16글자 이하로 작성해주세요.'
                    }
                )
        )
    last_name = forms.CharField(
            widget=forms.TextInput(
                    attrs={
                        'class': 'form-control',
                    }
                )
        )
    first_name = forms.CharField(
            widget=forms.TextInput(
                    attrs={
                        'class': 'form-control',
                    }
                )
        )
    email = forms.EmailField(
            widget=forms.EmailInput(
                    attrs={
                        'class': 'form-control',
                    }
                )

        )

    field_order=['username','password','password_confirm','last_name','first_name','email']
    #field_order는 만들어지는 입력양식의 순서, password 바로 밑에 password_check 양식을 추가하기 위해 사용

    class Meta:
        model=User
        widgets = {'password':forms.PasswordInput}
        fields = ['username','password','last_name','first_name','email']
#User model에 정의된 username, passwordm last_name, first_name, email을 입력양식으로

class SigninForm(ModelForm):
    username = forms.CharField(
            max_length=20,
            min_length=6,
            widget=forms.TextInput(
                    attrs={
                        'class': 'form-control'
                    }
                )
        )
    password = forms.CharField(
            max_length=16,
            min_length=8,
            widget=forms.PasswordInput(
                    attrs={
                        'class': 'form-control',
                    }
                )
        )

    class Meta:
        model = User
        widgets = {'password':forms.PasswordInput}
        fields = ['username','password']

```

#### 3. urls.py와 views.py 작성

```python
### urls.py ###
from django.urls import path, include
from . import views

app_name='accounts'

urlpatterns = [
    path('signup/', views.signup, name="signup"),
    path('signin/', views.signin, name="signin"),
    path('signout/', views.signout, name="signout"),
    path('community/', include('community.urls')),
]
### views.py ###
from django.shortcuts import render, redirect
from django.urls.base import reverse
from django.contrib.auth.models import User
from django.contrib.auth import login, authenticate, logout
#login은 login처리를 해주며, authenticate는 아이디와 비밀번호가 모두 일치하는 User 객체를 추출
from .forms import SigninForm, SignupForm
# Create your views here.

def signup(request):#역시 GET/POST 방식을 사용하여 구현한다.
    if request.method == "GET":
        form = SignupForm()
        context = {
            'form': form
        }
        return render(request, 'accounts/signup.html', context)


    elif request.method == "POST":
        form = SignupForm(request.POST)
        if form.is_valid():
            if form.cleaned_data['password']  == form.cleaned_data['password_confirm']:
        #cleaned_data는 사용자가 입력한 데이터를 뜻한다.
        #즉 사용자가 입력한 password와 password_check가 맞는지 확인하기위해 작성해주었다.
                new_user = User.objects.create_user(
                    form.cleaned_data['username'],
                    form.cleaned_data['email'],
                    form.cleaned_data['password']
                )
        #User.object.create_user는 사용자가 입력한 name, email, password를 가지고 아이디를 만든다.
        #바로 .save를 안해주는 이유는 User.object.create를 먼저 해주어야 비밀번호가 암호화되어 저장된다.
                new_user.last_name = form.cleaned_data['last_name']
                new_user.first_name = form.cleaned_data['first_name']
        #나머지 입력하지 못한 last_name과, first_name은 따로 지정해준다.
                new_user.save()

                return redirect(reverse('reviews:review_list'))
            else:
                context = {
                    'form':form,
                    'error':'비밀번호와 비밀번호 확인이 다릅니다.'

                }
                return render(request, 'accounts/signup.html',)
                #password와 password_check가 다를 것을 대비하여 error를 지정해준다.
        else: #form.is_valid()가 아닐 경우, 즉 유효한 값이 들어오지 않았을 경우
            context = {
                'form':form
            }
            return render(request, 'accounts/signup.html', context)

def signin(request):
    if request.method == "GET":
        form = SigninForm()
        context = {
            'form': form,
        }
        return render(request, 'accounts/signin.html', context)

    elif request.method == "POST":
        form = SigninForm(request.POST)
        id = request.POST['username']
        pw = request.POST['password']
        user = authenticate(username=id, password=pw) # username과 password를 비교, 같으면 해당객체 반환 아니면 none

        if user: # user 값이 있으면
            login(request, user=user) # user값으로 로그인
            return redirect('reviews:review_list')
        else:
            context = {
                'form' : form,
                'error': '아이디나 비밀번호가 일치하지 않습니다.'
            }
            return render(request, 'accounts/signin.html', context)

def signout(request):
    logout(request)
    return redirect('reviews:review_list')
```

#### 4. 로그인 관련 html을 작성

>bootstrap을 사용하여 네비게이션 바에 로그인 상태에 따라 다르게 표시함

```html
<!-- base.html 일부 -->
<div class="collapse navbar-collapse justify-content-between" id="navbarNav">
            <ul class="navbar-nav">
                {% if request.resolver_match.url_name == 'create' %}
                    <li class="nav-item">
                        <a class="nav-link" href="{% url 'reviews:review_list' %}">List</a>
                    </li>
                    <li class="nav-item active">
                        <a class="nav-link" href="{% url 'reviews:create' %}">New<span class="sr-only">(current)</span></a>
                    </li>
                {% else %}
                    <li class="nav-item active">
                        <a class="nav-link" href="{% url 'reviews:review_list' %}">List<span class="sr-only">(current)</span></a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="{% url 'reviews:create' %}">New</a>
                    </li>
                {% endif %}
            </ul>
            <div class="d-flex flex-row">
            {% if user.is_authenticated %}<!-- login 된 상태일 경우 -->
            <div class="p-2">{{ user.username }}님 환영합니다.</div>
            <ul class="navbar-nav">
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                    Sign out
                    </a>
                    <div class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
                        <a class="dropdown-item" href="{% url 'accounts:signout' %}">Sign Out</a>
                    </div>
            {% else %}
                <ul class="navbar-nav">
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                            Sign In
                        </a>
                        <div class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
                            <a class="dropdown-item" href="{% url 'accounts:signup' %}">Sign Up</a>
                            <a class="dropdown-item" href="{% url 'accounts:signin' %}">Sign In</a>
                        </div>
            {% endif %}
                </li>
            </ul>
            </div>
        </div>
```

#### 5. singup.html 작성

```html
<!-- signup.html -->
{% extends 'base.html' %}
{% load static %}
{% block body %}

{% if error %}
  <h3 class="text-danger">{{ error }}</h3>
{% endif %}
  <form action="" method="POST">
  {% csrf_token %}
  <div class="form-row mb-3 mt-5">
    <div class="col-12">
      <label for="username">Username</label>
      <div>{{ form.username }}</div>
    </div>
  </div>
  <div class="form-row mb-3">
    <div class="col-6">
      <label for="password">Password</label>
      <div>{{ form.password }}</div>
    </div>
    <div class="col-6">
      <label for="password_confirm">Password Confirm</label>
      <div>{{ form.password_confirm }}</div>
    </div>
  </div>
  <div class="form-row mb-3">
    <div class="col-6">
      <label for="last_name">Last name</label>
      <div>{{ form.last_name }}</div>
    </div>
    <div class="col-6">
      <label for="last_name">First name</label>
      <div>{{ form.first_name }}</div>
    </div>
  </div>
  <div class="form-row mb-3">
    <div class="col-12">
      <label for="email">Email</label>
      <div>{{ form.email }}</div>
    </div>
  </div>
  <div class="form-row mb-3">
    <button type="submit" class="btn btn-info col-12">SIGN UP</button>
  </div>
  <div class="form-row mb-3">
    <a href="/community/" class="btn btn-secondary col-12">CANCEL</a>
  </div>
</form>
{% endblock %}
```

#### 6. signin.html 작성

```html
<!-- signin.html -->
{% extends 'base.html' %}
{% load static %}
{% block body %}
    <form action="" method="POST">
    {% csrf_token %}
    <div class="form-row mb-3 mt-5">
        <div class="col-12">
            {% if error %}
            <div class="d-flex flex-row">
                <label for="username" class="mr-3">Username</label>
                <p class="text-danger p-0 m-0">{{ error }}</p>
            </div>
            {% else %}
            <label for="username">Username</label>
            {% endif %}
            <div>{{ form.username }}</div>
        </div>
    </div>
    <div class="form-row mb-3">
        <div class="col-12">
            <label for="password">Password</label>
            <div>{{ form.password }}</div>
        </div>
    </div>
    <div class="form-row mb-3">
        <button type="submit" class="btn btn-info col-12">SIGN IN</button>
    </div>
    <div class="form-row justify-content-between">
        <a href="/accounts/signup/" class="btn btn-info col-5">SIGN UP</a>
        <a href="/community/" class="btn btn-secondary col-5">CANCEL</a>
    </div>
    </form>

{% endblock %}
```

#### 7. 로그인 페이지를 modal로 변경하기

- base.html에서 include를 사용하여 modal 부분을 적은 html을 불러온다.

```html
<ul class="navbar-nav">
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                            Sign Up / Sign In
                        </a>
                        <div class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
                            <a class="dropdown-item" href="{% url 'accounts:signup' %}">Sign Up</a>
                            <a type="button" class="dropdown-item" data-toggle="modal" data-target="#SIGNIN">
                                Sign In
                            </a>
                        </div>
            {% endif %}
                </li>
            </ul>
            </div>
        </div>

{% include 'modal.html' %}
```

- modal.html을 작성 후 form action을 통해 만들어 놓은 sigin으로 보내어 로그인

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>SIGN IN</title>
</head>
<body>
    <!-- Modal -->
<div class="modal fade" id="SIGNIN" tabindex="-1" role="dialog" aria-labelledby="SIGNINTitle" aria-hidden="true">
  <div class="modal-dialog modal-dialog-centered" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="SIGNINTitle">SIGN IN</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
            <form action="{% url 'accounts:signin' %}" method="POST">
            {% csrf_token %}
            <div class="form-row mb-3 mt-5">
                <div class="col-12">
                    {% if error %}
                    <div class="d-flex flex-row">
                        <label for="username" class="mr-3">Username</label>
                        <p class="text-danger p-0 m-0">{{ error }}</p>
                    </div>
                    {% else %}
                    <label for="username">Username</label>
                    {% endif %}
                    <div><input type='text' id='username' name="username" class="form-control"></div>
                </div>
            </div>
            <div class="form-row mb-6">
                <div class="col-12">
                    <label for="password">Password</label>
                    <div><input type='password' id='password' name="password" class="form-control"></div>
                </div>
            </div>
            <div class="modal-footer mt-5">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary">Sign In</button>
            </div>
            </form>
        </div>
    </div>
  </div>
</div>
</body>
</html>
```



#### 참고

----

- 내가 여기서 사용한 것은 django에서 기본으로 제공하는 user 모델만 사용했음.
- 하지만 django에서는 UserCreationForm 이라는 ModelForm도 제공함. 다만, UserCreationForm은 username과 password1, 2만 입력받음. 

---

1. [Django 기초](https://dongyeopgu.github.io/cont/django_start.html)
2. [Django CRUD](https://dongyeopgu.github.io/cont/django_crud.html)
3. [Django Paginator](https://dongyeopgu.github.io/cont/django_paginator.html)

