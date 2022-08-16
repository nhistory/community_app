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
