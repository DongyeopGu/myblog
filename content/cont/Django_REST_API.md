---
title: "Django REST Framework"
weight: 24
chapter: true
pre: "<b>8. </b>"
tags: ["django","REST API", "REST Framework"]
---

## Django Rest Framework(DRF)

#### 1. REST API(DRF: Django Rest Framework)

- REST API를 활용하면 백 엔드와 프론트 엔드 분리가 쉽고 코드의 재활용성이 좋다.
- REST API를 사용하기 위해서 먼저 설치가 필요

```shell
$ pip install djangorestframework
```

- settings.py에서 `INSTALLED_APPS`에 `'rest_framework'`추가
- app 폴더에 serializers.py 파일을 만들고 내용 추가

```python
# serializers.py
from rest_framework import serializers
from .models import * #필요한 모델이름 넣어주기

class <모델명>Serializer(serializers.ModelSerializer):
    class Meta:
        model = <모델명>
        fields = ['필요한 필드']
```

- views.py에 필요한 내용 추가

```python
from django.shortcuts import render, get_object_or_404

from rest_framework.response import Response
from rest_framework.decorators import api_view

from .models import *
from .serializers import *

@api_view(['GET']) #method에 따라 다르게 적어주면 됨 (GET, POST 등)
def list(request):	#예를 들어 목록을 보여준다고 할때
    변수명 = 모델명.objects.all()	#queryset
    serializer = 모델명Serializer(변수명, many=True)	#위에서 만든 이름임, many=True는 1:N 경우, queryset을 json으로 변환
    return Response(serializer.data)
@api_view(['GET'])
def detail(request, pk):
    변수명 = get_object_or_404(모델명,pk=pk)
    serializer = 모델명DetailSerializer(변수명)	#serializers.py에서 detail을 위한 모델명DetailSerializer를 만든 후 사용
    return Response(serializer.data)
# Create 경우
@api_view(['POST'])
def create(request):
    serializer = 모델명Serializer(data=request.data)
    if serializer.is_valid():
        serializer.save()
        return Response(serializer.data)
    
# Update, Delete
@api_view(['PUT', 'DELETE'])
def comment_edit(request, pk):
    변수명 = get_object_or_404(모델명, pk=pk)
    if request.method == 'PUT':
        serializer = 모델명Serializer(변수명, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response('성공적으로 수정이 완료 되었습니다.')
        return Response(status=status.HTTP_400_BAD_REQUEST) # 상태까지 나타내기 위함   
    if request.method == 'DELETE':
        comment.delete()
        return Response('성공적으로 삭제 되었습니다.', status=status.HTTP_204_NO_CONTENT)
```

- views.py에서 작성한 이름대로 urls.py에 추가

----

1. [Django 기초](https://dongyeopgu.github.io/cont/django_start.html)
2. [Django CRUD](https://dongyeopgu.github.io/cont/django_crud.html)
3. [Django Paginator](https://dongyeopgu.github.io/cont/django_paginator.html)
4. [Django User(1)](https://dongyeopgu.github.io/cont/django_login.html)
5. [Django User(2)](https://dongyeopgu.github.io/cont/django_change_user.html)
6. [Django Comment](https://dongyeopgu.github.io/cont/django_comment.html)
7. [Django M:N](https://dongyeopgu.github.io/cont/django_like_follow.html)

