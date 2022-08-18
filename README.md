# Django project

## Introduction

A community feature application made by django framework.

- Made a virtual environment with virtualenv.
- Used dynamic URL connection between templates with inheritance.
- Did data migration with models and python class.

## Project version

- pytjon: 3.9.12
- pip: 22.2.2
- Django: 4.1

## Virtual environment

Install: `pip install virtualenv`
Create: `virtualenv env`
Activte: `source env/bin/activate`
Deactivate: `deactivate`

## Initiate Django project

Install: `pip install django`
Start project: `django-admin startproject <project_name>`
Run server: `python3 manage.py runserver` (on the project folder)

And you can see initial page on the browser with `http://127.0.0.1:8000`

## Startapp base

Add `base.apps.BaseConfig` on the `INSTALLED_APPS` list of `settings.py`. And now, django can know this base app is installed.

## Basic response function

urls.py

```python
from django.contrib import admin
from django.urls import path
from django.http import HttpResponse

def home(request):
   return HttpResponse('Home page')

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', home),
]
```

Import `HttpResponse` from `django.http` and make a `home` function with simple `HttpResponse` return.

## URL setup

There are huge of urls and methods on this project, so we need to seperate `main url` and url based on the app.

By using `include` module, import `base.url` on the `urlpatterns`.

root.url.py

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('base.urls'))
]
```

## Connect templates

Add templates destination on `settings.py`.

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            BASE_DIR / 'templates'
        ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

Create return value with `render` module.

```python
def home(request):
    return render(request, 'home.html')

def room(request):
    return render(request, 'room.html')
```

## Inherit templates

We can handle templates inside of html by using `main.html` and `block content`.

main.html

```html
<body>
  {% include 'navbar.html' %} {% block content %} {% endblock %}
</body>
```

Now, you can wrap other templates by usint `{% extends 'main.html' %}`.

home.html

```html
{% extends 'main.html' %} {% block content%}
<h1>Home Template</h1>

{% endblock content %}
```

## Using dynamic data on templates

We can make values on `view.py` and send data by using `context`.

```python
rooms = [
    {'id':1, 'name':'Lets learn python!'},
    {'id':2, 'name':'Design with me'},
    {'id':3, 'name':'Frontend developers'},
]

def home(request):
    context = {'rooms': rooms}
    return render(request, 'home.html', context)
```

If you want to iterate with for loop, you can do like below.

```html
<div>
  <div>
    {% for room in rooms %}
    <div>
      <h5>{{room.id}} -- {{room.name}}</h5>
    </div>
    {% endfor %}
  </div>
</div>
```

## Dynamic URL

Definitely, you are thinking about how can I use dynamic URL in django project. For doing this, fix urlpatterns of `url.py` and modify `room` function inside of `view.py`

```python
def room(request, pk):
    room = None
    for i in rooms:
        if i['id'] == int(pk):
            room = i
    context = {'room': room}
    return render(request, 'base/room.html', context)
```

By doing this, we can check whether room's id is equal to `pk` value on the requested url. If it is correct, django will reponse specific template on the browser.

## Data migration

First data table for this project should be made inside of `base/models.py`.

```python
# Class for data table - attributes are column field
class Room(models.Model):
  # host =
  # topic =
  name = models.CharField(max_length=200)
  description = models.TextField(null=True, blank=True)
  # participants =
  updated = models.DateTimeField(auto_now=True)
  created = models.DateTimeField(auto_now_add=True)

  def __str__(self):
    return self.name
```

After this, use `makemigrations` command and do migrate data.

`python3 manage.py makemigrations`

`0001_initial.py` is created on `migrations` folder.

`python3 manage.py migrate`

In order to check database, you need to make admin user.

`python3 manage.py createsuperuser`

After enter user information, we can use django administration page. But migrated room data is not seen now. Something should be added on `admin.py`.

```python
from .models import Room

admin.site.register(Room)
```

Now you can see `Rooms` section on the admin page.

## Connect with schema

After we create and migrate `Rooms` data, it can be used as an `objects` and many kinds of methods like below. (all(), get(), filter())

views.py

```python
def home(request):
    rooms = Room.objects.all()
    context = {'rooms': rooms}
    return render(request, 'base/home.html', context)

def room(request, pk):
    room = Room.objects.get(id=pk)
    context = {'room': room}
    return render(request, 'base/room.html', context)
```
