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

**1. Overview**
* view는 특정 기능을 하고 특정 템플릿을 가진, 장고 어플리케이션에서 웹 페이지의 한 종류이다. 
    - A blog application
        + Blog homepage : displays the latest few entries.
        + Entry 'detail' page : permalink page for a single entry.
        + Year-based archive page : displays all months with entries in the given year.
        + Month-based archive page : displays all days with entries in the given month.
        + Day-based archive page : displays all entries in the given day.
        + Comment action : handles posting comments to a given entry.
    - 작업 중인 poll application
        + Question 'index' page : displays the latest few questions.
        + Question 'detail' page : displays a question text, with no results but with a form to vote.
        + Question 'results' page : displays results for a particular question.
        + Vote action : handles voting for a particular choice in a particular question.
* 장고에서는, view에 의해 웹 페이지와 내용이 전달된다.
* view는 간단한 Python function으로 표현된다. class 기반 view일 경우 method이다.
* 장고는 요청된 URL을 분석하여 view를 선택한다.
* 'URLconf'는 URL pattern과 view를 매핑한다.


**2. Writing more views**
* 해당 view들은 인자를 가진다.
```python
# polls/views.py
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)
    
def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(requeset, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```
* 해당 view들을 다음 `path()` 호출을 추가하여 `polls.urls`에 엮는다.
```python
# polls/urls.py
from django.urls import path

from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path(''<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```
* `/polls/34/` 는 다음과 같은 호출을 만든다.
```
detail(request=<HttpRequest object>, question_id=34)
```


**3. Write views that actually do something**
* view가 하는 일 2가지.
    - 요청한 페이지와 관련된 내용이 담긴 `HttpResponse` 객체 리턴.
    - `Http404` 같은 예외 발생
* DB에서 레코드를 읽어오던, 템플릿을 이용하여 페이지를 출력하던, PDF, XML, ZIP 등 Python 라이브러리를 이용하여 무엇을 만들어서 돌려주던 상관없다.
* Django의 데이터베이스 API를 사용한다.
```python
# polls/views.py
from django.http import HttpResponse

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
    
# Leave the rest of the views (deatil, results, vote) unchanged.
```
* 템플릿을 이용하여 페이지의 모습을 바꾸자.
    - `polls/templates/polls/index.html`로 디렉토리 및 파일을 생성한다.
    - Template namespacing??
```html
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```
```python
# polls/views.py
from django.http import HttpResponse
from django.template import loader

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```
* A shortcut: `render()`
```python
# polls/views.py
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```
**4. Raising a 404 error**
* 요청한 ID의 Question이 없다면 `Http404` 예외를 발생한다.
```python
# polls/views.py
rom django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```
* A shortcut: `get_object_or_404()`
```python
# polls/views.py
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

**5. Use the template system**
* `detail()` view template
```html
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```

**6. Removing hardcoded URLs in templates**
* Before
```html
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```
* After
```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```
* `polls.url` 모듈에 명시된 URL 정의를 찾는다.
```python
# the 'name' value as called by the {% url %} template tag
path('<int:question_id>/', views.detail, name='detail')
```

**7. Namespacing URL names**
* 해당 tutorial 프로젝트는 `polls`라는 앱 하나만 있다. 실제 프로젝트에선 여러 앱이 있을 것이다. 여러 앱이 있을 때 URL 이름들을 구분하기 위해 namespacing을 한다.
* `app_name`을 추가하여 해당 app의 namespace를 설정한다.
```python
# polls/urls.py
rom django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```
* 이를 template에서도 바꿔야 한다.
* Before
```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```
* After
```html
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```

## Part 4
**1. Write a simple form**
* poll detail template(`polls/detail.html`) 개선
```html
<h1>{{ question.question_text }}</h1>

{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
{% endfor %}
<input type="submit" value="Vote">
</form>
```
* 개선사항
    - question의 각 choice 마다 radio button을 출력한다. 각 radio button의 name은 `choice`다. 이는 form을 전송할 때 `choice=#`(#은 선택한 choice의 ID)라는 POST data를 같이 보내기 위해서이다. 이는 HTML forms의 기본 개념이다.
    - form의 action을 `{% url 'polls:vote' question.id %}`로, `method="post"`로 설정했다. `method="get"`과는 달리 `"post"`는 서버 측 데이터를 사용 및 변조하기 때문이다. 이는 웹 개발 관례이다.
    - `forloop.counter`는 `for` 태그가 몇 번 반복했는지 가리킨다.
    - 데이터를 수정하는 영향을 끼치는 POST form을 만들기 때문에, CSRF(Cross Site Request Forgery)를 걱정해야 한다. Django에는 이를 사용하기 쉽게 만들었다. 결국, 내부 URL들을 대상으로 하는 모든 POST form들은 반드시 {% csrf_token %} 템플릿 태그를 사용해야 한다.
* 전송된 데이터를 다루는 view를 만들자.
```python
# polls/urls.py
path('<int:question_id>/vote/', views.vote, name='vote')
```
* 이전에 `vote()` 함수를 더미로만 구현했었다. 이번에 실제로 구현해보자. 다음 코드를 추가한다.
```python
from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse

from .models import Choice, Question
# ...
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # Redisplay the question voting form.
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # Always return an HttpResponseRedirect after successfully dealing
        # with POST data. This prevents data from being posted twice if a
        # user hits the Back button.
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```
* 여기엔 아직 설명하지 못한 부분들이 있다.
    - `request.POST`는 키 이름으로 전송한 데이터를 접근할 수 있도록 해주는 연상배열 같은 객체다. 여기서는, `request.POST['choice']`는 선택한 choice의 ID를 문자열로 돌려준다. `request.POST`의 값들은 항상 문자열이다. 물론 `request.GET`을 통해 GET 데이터도 접근이 가능하지만, 오직 POST call을 통해 데이터가 변경된다는 것을 확실하게 해주기 위해 `request.POST`만 사용한다.
    - `request.POST['choice']`는 `choice`가 POST 데이터에 없다면 `KeyError`을 일으킬 것이다. 위 코드는 `KeyError`를 확인하고 `choice`가 주어지지 않았다면 에러 메시지와 함꼐 question form을 다시 보여준다. 
    - choice 횟수를 증가한 후, 코드는 일반적인 `HttpResponse` 보단 `HttpResponseRedirect`를 리턴한다. `HttpResponseRedirect`는 유저가 redirect 될 URL을 인자로 받는다. 
    - 위 코드에서 주석을 달았듯이, POST data를 성공적으로 다룬 이후에는 `HttpResponseRedirect`를 항상 리턴해야 하낟. 이는 웹 개발 관례이다.
    - 여기서는 `HttpResponseRedirect`의 생성자에서 `reverse()` 함수를 사용하고 있다. 이 함수는 view 함수에서 URL를 직접 하드코딩하는 것을 방지한다.
* 누군가 question에서 투표를 한 후에, `vote()` view는 그 질문의 결과 페이지에 리다이렉트해준다. 이를 view로 작성한다.
```python
# polls/views.py
from django.shortcuts import get_object_or_404, render


def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```
* `polls/results.html` 템플릿도 작성한다.
```html
<h1>{{ question.question_text }}</h1>

<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>

<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```
* `vote()` view에 작은 문제가 있다. 데이터베이스에서 `selected_choice` 객체를 얻어오고, `votes`의 새로운 값을 계산한 다음, 다시 데이터베이스에 저장한다. 만약 두 명의 유저가 동시에 투표를 한다면, 원래 42표에서 44표가 될 것이 43표로 저장될 수도 있다. 이는 race condition이라고 부른다. 이는 [Avoiding race conditions using F()](https://docs.djangoproject.com/en/2.1/ref/models/expressions/#avoiding-race-conditions-using-f)를 참조하면 도움이 된다.

**2. Use generic views: Less code is better**
* 지금 코드는 매우 간단하지만 양이 좀 많다. 
* Django는 "generic views" 라는 지름길을 제공한다.
* 다음과 같은 단계를 거쳐 generic views system을 사용한다.
    1. Convert the URLconf.
    2. Delete some of the old, unneeded views.
    3. Introduce new views based on Django's generic views.
* 먼저 generic views system 사용 안했어?
    - 계산기 쓰기 전에 가감승제는 알아야지.
* Amend URLconf
```python
# polls/urls.py
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```
* Amend views
```python
# polls/views.py
from django.http import HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from django.views import generic

from .models import Choice, Question


class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]


class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'


class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'


def vote(request, question_id):
    ... # same as above, no changes needed.
```
* 두 개의 generic view를 사용한다.
    - `ListView` : 객체들의 목록 표시
    - `DetailView` : 특정 타입의 객체의 상세 내용 페이지를 표시
* `model` 속성을 지정해줘야 한다.
* `DetailView`는 URL에서 잡히는 주요 키값을 `pk`라고 한다. 그래서 generic view에서는 `question_id`를 `pk`로 바꿨다.


## Part 5
**1. Introducing automated testing**
* Automated tests? Without having to perform time consming manual testing.
* 시간 절약, 문제 방지, 디자인 준수, 팀워크 향상

**2. Basic testing strategies**
* TDD, Test-Driven Development
* 코딩 전에 테스트를 작성한다.