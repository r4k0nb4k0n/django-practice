# django-practice [link](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)
## Environment
goormIDE의 Django 컨테이너에서 Django를 2.1로 업데이트 한 후 실습을 진행했다.
## Part 1
**1. Creating a project**
```
$ django-admin startproject django_practice
```
  * 상위 `django_practice/` 디렉토리는 프로젝트를 담는 것으로 이름은 원하는 대로 바꿔도 된다.
  * `manage.py`는 장고 프로젝트를 다양한 방법으로 건들 수 있는 커맨드라인 기반 유틸리티이다.
  * 하위 `django_practice/` 디렉토리는 해당 프로젝트의 실제 파이썬 패키지이다.
  * `django_practice/__init__.py` 로 파이썬에게 `django_practice/`를 파이썬 패키지로 인식하게 한다. 
  * `django_practice/settings.py`는 장고 프로젝트의 설정 파일이다.
  * `django_practice/urls.py`로 장고 프로젝트에서 URL를 설정할 수 있다.
  * `django_practice/wsgi.py`

**2. The development server**
```
$ python3 manage.py runserver 0:8000
$ python3 manage.py runserver 0.0.0.0:8000
```
![](./tutorial_img/1.png?raw=true)

**3. Creating the Polls app**
* An app is a Web application that does something.
* A project is a collection of configuration and apps for a particular website.
```
$ python3 manage.py startapp polls
```
* `polls/`라는 폴더가 생긴다.

**4. Write your first view**
```python3
# polls/views.py

from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```
```python3
# polls/urls.py

from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

```python3
# mysite/urls.py

from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```
## Part 2

## Part 3

## Part 4

## Part 5
