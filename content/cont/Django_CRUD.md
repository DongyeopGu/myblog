---
title: "Django CRUD"
---

### CRUD - Create, Read, Update, Delete

#### 1. Model 사용 (ex: Article이라는 모델을 만들경우)

```python
# 프로젝트 명/App이름/models.py
class Article(models.Model):
    title = models.CharField(max_length=140)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

* `models.Model` 을 상속받은 클래스를 생성한다.
* 속성은 내가 구성하고 싶은 테이블의 컬럼의 이름을 지정하고, 데이터 타입에 맞춰서 필드를 정의한다.
* 위에서 정의된 필드 정보
  * `CharField` :
    * `max_length` : 필수
  * `DateTimeField`
    * `auto_now_add` : (선택) 생성시에만 자동으로 해당 시간 값 설정
    * `auto_now` : (선택) 수정시마다 자동으로 해당 시간 값 설정
  * 이외의 필드는 https://docs.djangoproject.com/ko/2.1/ref/models/fields/#field-types 여기서 확인

#### 2. Migrations

> Migrations are Django’s way of propagating changes you make to your models (adding a field, deleting a model, etc.) into your database schema.
>
> 마이그레이션은 django에서 모델의 변경 사항을 데이터베이스 스키마에 반영하기 위한 방법이다.
>
> [관련 문서](https://docs.djangoproject.com/en/2.1/topics/migrations/)

* 정의된 모델을 데이터베이스에 반영하기 위해서 아래 명령어 실행.

  ```bash
  $ python manage.py makemigrations
  Migrations for 'articles':
    articles/migrations/0001_initial.py
      - Create model Article
  ```

* 마이그레이션 파일은 모델의 변경사항은 기록하며, app 별로 있는 `migrations/` 폴더에 기록된다. 최초에 `0001_initial.py` 라는 파일이 생성되어 있을 것이다.

* 생성된 마이그레이션 파일을 데이터베이스에 반영하기 위해서는 아래의 명령어를 입력한다.

  * 아래와 같이 많아 보이는 것은 django가 기본적으로 활용하고 있는 데이터베이스 마이그레이션 파일까지 반영되었기 때문이다. (프로젝트 생성과 동시에 `python manage.py migrate` )

  ```bash
  $ python manage.py migrate
  Operations to perform:
    Apply all migrations: admin, articles, auth, contenttypes, sessions
  Running migrations:
    Applying contenttypes.0001_initial... OK
    Applying auth.0001_initial... OK
    Applying admin.0001_initial... OK
    Applying admin.0002_logentry_remove_auto_add... OK
    Applying admin.0003_logentry_add_action_flag_choices... OK
    Applying articles.0001_initial... OK
    Applying contenttypes.0002_remove_content_type_name... OK
    Applying auth.0002_alter_permission_name_max_length... OK
    Applying auth.0003_alter_user_email_max_length... OK
    Applying auth.0004_alter_user_username_opts... OK
    Applying auth.0005_alter_user_last_login_null... OK
    Applying auth.0006_require_contenttypes_0002... OK
    Applying auth.0007_alter_validators_add_error_messages... OK
    Applying auth.0008_alter_user_username_max_length... OK
    Applying auth.0009_alter_user_last_name_max_length... OK
    Applying sessions.0001_initial... OK
  ```

* Migrations 관련 명령어

  ```bash
  $ python manage.py makemigrations
  $ python manage.py showmigrations
  $ python manage.py migrate
  $ python manage.py sqlmigrate article 0001
  ```

#### 3. admin 등록하기

```bash
# 프로젝트 명/app 이름/admin.py
from django.contrib import admin

# Register your models here.
from .models import Article
admin.site.register(Article)
```

