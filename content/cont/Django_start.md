---
title: "Django 시작하기"
weight: 5
chapter: true
pre: "<b>1. </b>"
tag : ["django"]
---

### 기초

---

#### 1. django 설치

```
$ pip install django==2.1.15
```

(2.1.15 기준으로 배웠기 때문에)

#### 2. 프로젝트 시작

```
$ django-admin startproject {프로젝트 이름}
그 후에 프로젝트 폴더의 settings.py에서 
ALLOWED_HOST = ['*']로 수정
```

#### 3. App 만들기

```
manage.py 파일이 있는 폴더에서
$ python manage.py startapp {앱 이름}
그 후에 settings.py에서 
INSTALLED_APPS = [
	'{앱 이름}' 추가
]
```

#### 4. urls.py 작성

```
# 프로젝트 이름/urls.py에서 예를 들어 index 페이지를 넣을 경우 
from app이름 import views #추가
urlpatterns =[
	path('index/', views.index),
]
## path의 url은 항상 /로 닫기 ##
```

#### 5. views.py 작성

```
# app 이름/views.py 에서
def index(request):
	return render(request, 'index.html') #기본 형식
# 만약 html파일에 넣고자 하는 내용이 있을 경우에는
def index(request):
	sample = '샘플'
	context = {
		'sample': sample,
	}
	return render(request, 'index.html', context)
# 이렇게 쓸 수 있음. 다른 방법으로,
	context를 적지 않고 바로 
	return render(request, 'index.html', {'sample':sample})로 쓸 수 있음.
```

- 함수 정의에서 첫번째 인자는 항상 request로
- render에서도 첫번째 인자는 항상 request

#### 6. templates 폴더 만들기

- 필요한 html 파일을 app이름 폴더 안에 templates 폴더를 만든후 저장.
- django template language는 https://docs.djangoproject.com/en/3.0/를 이용해서 확인

#### 7. base.html 만들 경우

```python
# 프로젝트명 폴더에 templates 폴더를 만든 후
# settings.py에서
TEMPLATES = [
    {
        ...
        'DIRS': [
                    os.path.join(BASE_DIR, 'templates')
                ],   # 추가
        ...
    }
]
```

- base.html 작성

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    {% block body %}
    {% endblock %}
</body>
</html>
```

- 그리고 app이름 폴더에 있는 templates 안에 있는 html 파일들에 내용 추가

```html
{% extends 'base.html' %}
{% block body %}

<!-- html 작성 -->

{% endblock %}
```



---

1. [Django CRUD](https://dongyeopgu.github.io/cont/django_crud.html)
2. [Django Paginator](https://dongyeopgu.github.io/cont/django_paginator.html)
3. [Django User](https://dongyeopgu.github.io/cont/django_login.html)