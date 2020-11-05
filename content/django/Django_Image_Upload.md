---
title: "Django Image Upload"
weight: 25
chapter: true
pre: "<b>9. </b>"
tags: ["django","Image", "Upload"]
---

## Django에서 Image Upload

> POST요청을 통해 Vue.js에서 Django로 이미지 전송.

### 기본 세팅

- settings.py에서 아래 추가

```python
MEDIA_URL = '/api/' # 기본 api url을 통일 시켜 번거로움을 줄이기 위해 /api/로 설정했음
MEDIA_ROOT = os.path.join(BASE_DIR, 'media') # 이미지 파일이 저장되는 기본 위치를 media폴더로 설정
```

> 이미지 url도 다른 application과 같이 api로 시작하게 만들기 위해 MEDIA_URL을 /api/로 설정

- urls.py

```python
from django.contrib import admin
from django.urls import path, include
from django.conf.urls.static import static
from django.conf import settings

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/accounts/', include('accounts.urls')),
    path('api/articles/', include('articles.urls')),
    path('api/images/', include('images.urls')),
]
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 
```

> `urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) `을 추가함

#### 1. Image를 Base64로 받아서 저장하는 방법

- 순서
  1. Vue 에서 Image를 base64로 인코딩
  2. 인코딩한 정보를 django로 보냄
  3. django에서 디코딩하여 저장
- views.py

```python
import base64
import os
from datetime import datetime

@api_view(['POST'])
def upload_image(request):
    img_string = request.data['img_base64'] # POST요청을 통해 받은 base64정보
    imgdata = base64.b64decode(img_string) # 디코딩
    now = datetime.now()
    now = datetime.timestamp(now)
    filename = f'temp_image_{request.user}.jpg' # DB에 저장하지 않고 사용한다음 지우기 위해
    with open(filename, 'wb') as f:
        f.write(imgdata)				# 디코딩한 이미지를 사용하기 위해 잠시 저장
 	#### 이미지를 사용하는 코드 #####
    os.remove(filename)    				# 이미지를 사용한 후 삭제
    return Response({'result' : result})

@api_view(['POST'])
@permission_classes([IsAuthenticated])
def save_image(request):
    img_string = request.data['img_base64']
    img_result = request.data['result']
    imgdata = base64.b64decode(img_string)
    now = datetime.now()
    now = datetime.timestamp(now)
    filename = f'{img_result}_{request.user}_{now}.jpg'
    dir_list = os.listdir(settings.MEDIA_ROOT+'/users/')     
    if str(request.user) not in dir_list:
        os.makedirs(settings.MEDIA_ROOT+'/users/'+f'{request.user}/')
    media_root = settings.MEDIA_ROOT+'/users/'+f'{request.user}/'+filename  # 각각 user이름의 폴더에 이미지 저장한 후 db에 저장하기 위해
    with open(media_root, 'wb') as f:
        f.write(imgdata)
    animal = get_object_or_404(Animal, english_name=img_result)
    animal_image = AnimalImage.objects.create(
        upload_image=f'users/{request.user}/{filename}',
        animal=animal,
        upload_user=request.user
    )
```

> 이미지 경로를 설정하지 않은 경우 BASE_DIR에 저장됨



#### 2. Image를 파일로 받아서 저장하는 방법

- 순서
  1. vue에서 formData로 보낼 파일을 넣어줌
  2. django에서 파일정보를 받아 저장
- vue(vuetify 사용했음)

```html
<v-file-input v-model="files" multiple name="files" ></v-file-input> <!-- 여러파일 보낼경우 multiple 사용 -->

<script>
import axios from 'axios'

export default {
  data() {
    return {
      files: null
    }
  },
  methods: {
    sendImages() {
      let info = new formData()
      info.append('files', this.files)
      if (this.files===null) {             // 파일을 보내지 않을 경우
        info.append('files', [])
      } else {
        for (let i = 0; i < this.files.length; i++) {   // 파일이 하나 이상인 경우
          info.append('files', this.files[i]);
        }
      }
      const token = sessionStorage.getItem("token") // token을 세션에 저장시켜 사용했기 때문에
      let config = {
        headers: {
          'Content-Type': 'multipart/form-data', // Content-Type을 변경해야 파일이 전송됨
          'Authorization': `token ${token}`
          }
      }
      axios.post(
        `${process.env.VUE_APP_BACKEND_URL}요청 보낼 URL`, 
        info, config)
      .then((res) => {
        console.log(res) // 필요한 것 넣어서 쓰면됨
      })
    }
  }
}
</script>
```

- models.py(위에서 한 방법과 다르게 바로 models.py에서 사용자별 폴더를 생성해 저장하는 방법)

```python
def user_directory_path(instance, filename):
    return 'users/{0}/{1}'.format(instance.upload_user.username, filename)  # user를 upload_user로 저장했기 때문에 instance.upload_user로 사용함
def user_path(instance):
    return 'users/{0}'.format(instance.upload_user.username)

class HairImage(models.Model):
    upload_date = models.DateTimeField(auto_now_add=True) # 업로드 시간
    upload_user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE) # 업로드 유저
    upload_image = models.ImageField(upload_to=user_directory_path, blank=True) # 이미지 경로
    def delete(self, *args, **kargs):   # DB 를 삭제하면 저장한 이미지도 삭제되도록 하기 위해
        if self.upload_image:
            img_path = f'{settings.MEDIA_ROOT}/{user_path}'
            os.remove(os.path.join(img_path, self.upload_image.path))
        super(HairImage, self).delete(*args, **kargs)
```

- views.py

```python
@api_view(['POST'])
@permission_classes([IsAuthenticated])
def analyze_image(request):
    src = request.FILES['files'] # 파일이 하나일 경우
    #for img in request.FILES.getlist('files'):   # 파일이 여러개일 경우
    #    upload_img = UploadImage.objects.create(image=img, .....)
    ########
    ########
    uploaded_image = HairImage.objects.create(upload_image=src, upload_user=request.user)
	########
    ########
    return Response('response할 내용')
```



----

1. [Django 기초](https://dongyeopgu.github.io/cont/django_start.html)
2. [Django CRUD](https://dongyeopgu.github.io/cont/django_crud.html)
3. [Django Paginator](https://dongyeopgu.github.io/cont/django_paginator.html)
4. [Django User(1)](https://dongyeopgu.github.io/cont/django_login.html)
5. [Django User(2)](https://dongyeopgu.github.io/cont/django_change_user.html)
6. [Django Comment](https://dongyeopgu.github.io/cont/django_comment.html)
7. [Django M:N](https://dongyeopgu.github.io/cont/django_like_follow.html)

