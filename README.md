# Django project

## Introduction

A community feature application made by django framework.

- Made a virtual environment with virtualenv.

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
