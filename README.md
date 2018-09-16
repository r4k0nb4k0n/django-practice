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
**1. Database setup**
* 기본적으로 SQLite를 사용한다.
* 다른 것을 사용하고 싶다면 적합한 데이터베이스 모듈을 설치하고, 데이터베이스 연결 설정에 맞추기 위해 `DATABASES 'default'` 안의 다음 키들을 바꾼다.
    - `ENGINE`
        + `django.db.backends.sqlite3`
        + `django.db.backends.postgresql`
        + `django.db.backends.mysql`
        + `django.db.backends.oracle`
    - `NAME`
        + 데이터베이스의 완전한 절대 경로
        + `os.path.join(BASE_DIR, 'db.sqlite3')`
* SQLite 대신 다른 데이터베이스를 사용한다면, `USER`, `PASSWORD`, `HOST`가 추가되어야 한다.
* `django-practice/`에서 `TIME_ZONE`을 `Asia/Seoul`로 값을 변경했다.
* 다음 명령을 통해 `INSTALLED_APPS`가 필요한 데이터베이스 테이블들을 생성한다.
```
$ python3 manage.py migrate
```

**2. Creating models**
* A model contains the essential fields and behaviors of the stored data.
* 만들 것들
    - `Question`
        + A question(질문).
        + A publication date(게시 날짜).
    - `Choice`
        + The text of the choice(선택지).
        + A vote tally(표수).
* 위를 다음과 같이 Python class로 나타낼 수 있다.
```python3
# polls/models.py
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```
* 각 model은 `django.db.models.Model`의 sub-class인 class로 표현할 수 있다.
* 각 model은 class 변수들을 가지는데, 이는 model의 database field를 나타낸다.
* 각 `Choice`는 하나의 `Question`에 연결된다. 이는 `ForeignKey`로 다대일 관계를 나타낸다.
* 다대일, 다대다, 일대일 관계
* 자세히 하려면 DB 공부를 해야겠다.

**3. Activating models**
* **2. Creating models**를 통해 알 수 있는 Django가 할 수 있는 것
    - 특정 앱을 위한 DB 스키마(`CREATE TABLE`) 생성/
    - DB 내 특정 테이블을 접근할 수 있는 Python DB 접근 API 생성.
* 먼저 프로젝트에게 `polls`를 추가했다고 알려주어야 한다.

```python3
# django-practice/settings.py

# ...
INSTALLED_APPS = [
    'polls.apps.Polls.Config',
    '...',
]
# ...
```

* 이후 `polls`의 마이그레이션 작업을 한다.
```
$ python3 manage.py makemigrations polls
Migration for 'polls':
  polls/migrations/0001_initial.py:
    - Create model Choice
    - Create model Question
    - Add field question to choice
$ python3 manage.py migrate
```

* 이를 SQL문으로 볼 수도 있다.
```
$ python# manage.py sqlmigrate polls 0001
```

**4. Playing with the API**
```
$ python3 manage.py shell
```
* Add `__str__()` method.
* SQL을 몰라도 DB작업이 가능하다...?

**5. Introducing the Django Admin**
* model마다 관리자 interface를 만드는 게 지루하기 때문에, 장고는 이를 자동화했다.
* Creating an admin user
```
$ python3 manage.py createsuperuser
```
* Start the development server
```
$ python3 manage.py runserver 0:8000
```
* Make the poll app modifiable in the admin
```python3
# polls/admin.py
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```

## Part 3

## Part 4

## Part 5
