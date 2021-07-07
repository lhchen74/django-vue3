## backends

### django

0. pip install django
1. django-admin startproject backends
2. cd backends
3. python manage.py startapp lyb
4. backends/settings.py INSTALLED_APPS add lyb

```python
INSTALLED_APPS = [
    ...
    'lyb'
    ...
]
```

5. python manage.py runserver

#### models

1. lyb/models.py

```python
from django.db import models

class Lyb(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)
    content = models.TextField()
    posttime = models.DateTimeField(auto_now_add=True)

    class Meta:
        db_table = "d_lyb"
```

2. python manage.py makemigrations
3. python manage.py migrate

### djangorestframework

[Quickstart - Django REST framework](https://www.django-rest-framework.org/tutorial/quickstart/#quickstart)

1. pip install djangorestframework
2. backends/settings.py INSTALLED_APPS add rest_framework

```python
INSTALLED_APPS = [
    ...
    'rest_framework'
    ...
]
```

#### serializer

lyb/serializers.py

```python
from rest_framework import serializers
from .models import Lyb


class LybSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Lyb
        fields = "__all__"
```

#### viewset

lyb/views.py

```python
from django.shortcuts import render
from rest_framework import viewsets
from .models import Lyb
from .serializers import LybSerializer

class LybViewSet(viewsets.ModelViewSet):
    queryset = Lyb.objects.all().order_by('-posttime')
    serializer_class = LybSerializer
```

### router

backends/urls.py

```python
from django.contrib import admin
from django.urls import path, include
from rest_framework import routers
from lyb.views import LybViewSet

router = routers.DefaultRouter()
router.register(r'lyb', LybViewSet)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include(router.urls))
]
```

#### testing

python manage.py runserver

http://127.0.0.1:8000/api/lyb

![](backends.png)

### django-cors-headers

1. pip install django-cors-headers
2. backends/settings.py INSTALLED_APPS add corsheaders

```python
INSTALLED_APPS = [
    ...
    'corsheaders',
    ...
]
```

2. backends/settings.py MIDDLEWARE add corsheaders.middleware.CorsMiddleware

```python
MIDDLEWARE = [
...
'corsheaders.middleware.CorsMiddleware',
...
]
```

3. backends/settings.py setting allow cors

```python
CORS_ALLOW_ALL_ORIGINS = True
```

## frontends

### vite-app

1. npm init vite-app frontends
2. cd frontends
3. npm install
4. npm run dev
5. index.html link milligram CDN
6. components/Lyb.vue
