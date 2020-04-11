---
title: "Django 시작하기"
---

### 기초

---

#### django 설치

```
$ pip install django==2.1.15
```

(2.1.15 기준으로 배웠기 때문에)

#### 프로젝트 시작

```
$ django-admin startproject {프로젝트 이름}
그 후에 프로젝트 폴더의 settings.py에서 
ALLOWED_HOST = ['*']로 수정
```

#### App 만들기

```
manage.py 파일이 있는 폴더에서
$ python manage.py startapp {앱 이름}
그 후에 settings.py에서 
INSTALLED_APPS = [
	'{앱 이름}' 추가
]
```

#### urls.py 작성

```
# 프로젝트 이름/urls.py에서 예를 들어 index 페이지를 넣을 경우 
from app이름 import views #추가
urlpatterns =[
	path('index/', views.index),
]
## path의 url은 항상 /로 닫기 ##
```

#### views.py 작성

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

#### templates 폴더 만들기

- 필요한 html 파일을 app이름 폴더 안에 templates 폴더를 만든후 저장.
- django template language는 https://docs.djangoproject.com/en/3.0/를 이용해서 확인