---
title: "OpenStack Horizon: Create New Plugin"
tags: ["8월"]
---

# Create New Plugin

#### 폴더 구조

![image-20200825224652583](C:\Users\Dongyeop\AppData\Roaming\Typora\typora-user-images\image-20200825224652583.png)

1. enabled 폴더 

   - Plugin을 등록하는 configuration 파일`ex)_31000_myplugin.py`

   - `_31000` 은 load 순서를 정하는 번호

   - `ex)_31000_myplugin.py` 파일

     ```python
     # The name of the panel to be added to HORIZON_CONFIG. Required.
     PANEL = 'mypanel'
     
     # The name of the dashboard the PANEL associated with. Required.
     PANEL_DASHBOARD = 'identity'
     
     # Python panel class of the PANEL to be added.
     ADD_PANEL = 'myplugin.content.mypanel.panel.MyPanel'
     
     # A list of applications to be prepended to INSTALLED_APPS
     ADD_INSTALLED_APPS = ['myplugin']
     
     # A list of AngularJS modules to be loaded when Angular bootstraps.
     ADD_ANGULAR_MODULES = ['horizon.dashboard.identity.myplugin.mypanel']
     
     # Automatically discover static resources in installed apps
     AUTO_DISCOVER_STATIC_FILES = True
     
     # A list of js files to be included in the compressed set of files
     ADD_JS_FILES = []
     
     # A list of scss files to be included in the compressed set of files
     ADD_SCSS_FILES = ['dashboard/identity/myplugin/mypanel/mypanel.scss']
     
     # A list of template-based views to be added to the header
     ADD_HEADER_SECTIONS = ['myplugin.content.mypanel.views.HeaderView',]
     ```

   - [여기](https://docs.openstack.org/horizon/latest/configuration/settings.html#install-settings)에 세팅 설명 참조

2. `my_rest_api.py`

   - Angular 사용시 필요, 순수 Django만 사용할 시 불필요.
   - 추가할 플러그인과 새로운 서비스와 통신하거나 이미 Horizon에서 지원하는 서비스와 interaction시 필요함.
   - 플러그인의 클라이언트 쪽과 Horizon 통신시 새 REST 인터페이스를 정의하는 역할.
   - 일반적으로 이 REST 인터페이스는 `myservice.py`를 호출.

3. `myservice.py`

   - Django나 Angular 사용한 플러그인 생성시 필요.
   - 추가할 플러그인이 지원하는 새로운 서비스와 interaction 하기 위해 편리한 location 역할.

4. `panel.py`

   - 플러그인의 콘텐츠가 상주할 패널을 정의.

   - slug는 패널의 고유 식별자로 URL의 일부로 자주 사용. 

     ```python
     from django.utils.translation import ugettext_lazy as _
     import horizon
     
     class MyPanel(horizon.Panel):
         name = _("My Panel")
         slug = "mypanel"
     ```

5. `views.py`

   - Django 사용했을 때 그대로, 같은 역할.

     ```python
     from django.views import generic
     
     class IndexView(generic.TemplateView):
         template_name = 'identity/mypanel/index.html'
     ```

     > 주로 어떤 방식으로 만드는지 확인 필요.
     >
     > FBV도 사용하는지 확인 필요.

6. `index.html`

   - Django만 사용할 시

     ```html
     {% extends 'base.html' %}
     {% load i18n %}
     {% block title %}{% trans "My plugin" %}{% endblock %}
     
     {% block page_header %}
       {% include "horizon/common/_domain_page_header.html" with title=_("My Panel") %}
     {% endblock page_header %}
     
     {% block main %}
       Hello world!
     {% endblock %}
     ```

   - 새로운 templates 만들어서 사용하면 됨(Django 사용했던 pjt 생각)

7. `MANIFEST.in`

   -  tar에 포함 할 경로

     ```
     include setup.py
     
     recursive-include myplugin *.js *.html *.scss
     ```

8. `setup.py`, `setup.cfg` 확인 필요

![image-20200825231645374](image-20200825231645374.png)



