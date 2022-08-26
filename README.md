# Django project

## Introduction

A community feature application made by django framework.

- Made a virtual environment with virtualenv.
- Used dynamic URL connection between templates with inheritance.
- Did data migration with models and python class.
- Create CRUD feature by using modelForm, request and redirect.
- Add search bar to find room and description with django db model Q.
- Used cookie-based sessions to authenticate user information.
- Imported decorator module to restrict user authority.

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

## CRUD functionality

### Use ModelForm to create forms

- ModelForm: ModelForm is a regular Form which can automatically generate certain fields. The fields that are automatically generated depend on the content of the Meta class and on which fields have already been defined declaratively. Basically, ModelForm will only generate fields that are missing from the form, or in other words, fields that weren’t defined declaratively.

forms.py

```python
from django.forms import ModelForm
from .models import Room

class RoomForm(ModelForm):
  class Meta:
    model = Room
    fields = '__all__'
```

To create a new room and save, we can use `RoomForm(request.POST)`.

view.py

```python
    # If create-room request POST
    if request.method == 'POST':
        form = RoomForm(request.POST)
        # form validation
        if form.is_valid():
            form.save()
            # redirect to the homepage
            return redirect('home')
```

### Update room information

We can make `updateRoom` function to edit room information. If user click `Edit` link on the `home.html`, `url.py` will connect to `update-room/<str:pk>/` and request `updateRoom` function. After edit this room's information, redirected into home page like below.

```python
def updateRoom(request, pk):
    room = Room.objects.get(id=pk)
    form = RoomForm(instance=room)

    if request.method == 'POST':
        form = RoomForm(request.POST, instance=room)
        if form.is_valid():
            form.save()
            return redirect('home')

    context = {'form': form}
    return render(request, 'base/room_form.html', context)
```

### Delete room

Such as `updateRoom` feature, you can add `deleteRoom` function on `views.py`.

```python
def deleteRoom(request, pk):
    room = Room.objects.get(id=pk)
    if request.method == 'POST':
        room.delete()
        return redirect('home')
    return render(request, 'base/delete.html', {'obj':room})
```

## Django authentication

Django uses session to authenticate user on the browser. To understand this, we need to figure out what session is, what is differencies between session and cookies on web browser.

### Differencies between session and cookies

Cookies are client-side files that are stored on a local computer and contain user information. Sessions are server-side files that store user information. Cookies expire after the user specified lifetime. The session ends when the user closes the browser or logs out of the program.

Sessions are more secure than cookies, since they're normally protected by some kind of server-side security.

Django uses a cookie containing a special session id to identify each browser and its associated session with the site.

### Create authentication feature

To authenticate user with session in django framework, you should import several modules and create `loginPage`, `logoutUser` function on `views.py`.

```python
from django.contrib.auth.models import User
from django.contrib.auth import authenticate, login, logout

...

def loginPage(request):

    if request.method == 'POST': # if user input any information
        username = request.POST.get('username')
        password = request.POST.get('password')

        # Check user is exist
        try:
            user = User.objects.get(username=username)
        except:
            messages.error(request, 'User does not exist')

        user = authenticate(request, username=username, password=password)

        # if user is not None, login executed
        if user is not None:
            login(request, user)
            return redirect('home')
        else:
            messages.errir(request, 'Username OR password does not exist')

    context = {}
    return render(request, 'base/login_register.html', context)

def logoutUser(request):
    logout(request)
    return redirect('home')
```

### Restrict user authority

After add user authentication, you are going to restrict user authority to edit or update some information. There bunch of ways to do this, but I will use `decorators` and `HttpResponse` in django.

For doing this, you need to import modules on `views.py`.

```python
from django.http import HttpResponse
from django.contrib.auth.decorators import login_required
```

And add annotaion to each function that need user login required like below.

```python
@login_required(login_url='/login')
def createRoom(request):
    ...
```

We can check current user is equal to host of the room by using conditional statement.

```python
    if request.user != room.host:
        return HttpResponse('You are not allowed here!!')
```

## Reference

- https://youtu.be/PtQiiknWUcI
- https://docs.djangoproject.com/en/4.0/topics/forms/modelforms/
- https://www.tutorialspoint.com/What-is-the-difference-between-session-and-cookies#:~:text=Cookies%20are%20client%2Dside%20files,files%20that%20store%20user%20information.&text=Cookies%20expire%20after%20the%20user,logs%20out%20of%20the%20program.
