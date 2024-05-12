https://docs.djangoproject.com/en/5.0/topics/http/urls/
### Path converters
sitewomen/**sitewomen**/urls.py
```python
from django.contrib import admin
from django.urls import path
from women.views import index  # самый тупой и простой вариант

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', index)  # самый тупой и простой вариант
]    
```
sitewomen/**women**/views.py
```python
from django.shortcuts import render
from django.http import HttpResponse # импортируем HttpResponse

def index(request): #HttpRequest http://127.0.0.1:8000/
    return HttpResponse("Страница приложения women.")
```
##### Для лучшей читабельности и возможности работы с динамическими ссылками, а также переноса приложения создаем **urls.py** в нём.
Меняем ссылки в корне нашего сайта на наше приложение
sitewomen/**sitewomen**/urls.py
```python
from django.contrib import admin
from django.urls import path, include  # импортируем include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('women.urls')),  # если в приложении создали urls.py
]
```
Прописываем пути в самом приложении на которое ссылается urls.py в корне сайта
sitewomen/**women**/urls.py   
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index),
    path('cats/', views.categories),  # категории
]
```
Создаем дополнительную функцию для категорий
sitewomen/**women**/views.py
```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request): #HttpRequest http://127.0.0.1:8000/
    return HttpResponse("Страница приложения women.")

def categories(request):  # http://127.0.0.1:8000/cats
    return HttpResponse(f"<h1>Куча категорий</h1>")
```

### Динамические ссылки
По умолчанию доступны следующие преобразователи путей:
 - `str` - Соответствует любой непустой строке, за исключением разделителя пути `'/'`. Это значение по умолчанию, если преобразователь не включен в выражение. 
 - `int` — соответствует нулю или любому положительному целому числу. Возвращает `int`.
 - `slug` – соответствует любой короткой строке, состоящей из букв или цифр ASCII, а также символов дефиса и подчеркивания. Например, «создайте свой первый сайт на Джанго». 
 - `uuid` — соответствует отформатированному UUID. Чтобы предотвратить сопоставление нескольких URL-адресов с одной и той же страницей, необходимо включить дефисы, а буквы должны быть строчными. Например, `075194d3-6885-417e-a8a8-6c931e272f00`. Возвращает экземпляр [`UUID`](https://docs.python.org/3/library/uuid.html#uuid.UUID "(in Python v3.12)"). 
 - `path` — соответствует любой непустой строке, включая разделитель пути `'/'`. Это позволяет вам сопоставлять полный путь URL-адреса, а не его сегмент, как в случае с `str`.
sitewomen/**sitewomen**/urls.py
```python
from django.contrib import admin
from django.urls import path, include  # импортируем include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('women.urls')),  # если в приложении создали urls.py
]
```
sitewomen/**women**/urls.py
```python
from django.urls import path, re_path
from . import views

urlpatterns = [
    path('', views.index),
    path('cats/<int:cat_id>/', views.categories),
    path('cats/<slug:cat_slug>/', views.categories_by_slug),
    re_path(r"^archive/(?P<year>[0-9]{4})/", views.archive),
]
```
sitewomen/**women**/views.py
```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request): #HttpRequest
    return HttpResponse("Страница приложения women.")

def categories(request, cat_id):
    return HttpResponse(f"<h1>Куча категорий</h1><p>id: {cat_id}</p>")

def categories_by_slug(request, cat_slug):
    return HttpResponse(f"<h1>Куча категорий</h1><p>slug: {cat_slug}</p>")

def archive(request, year):
    return HttpResponse(f"<h1>Архив</h1><p>regular: {year}</p>")
```

#### Динамические ссылки через собственные классы
sitewomen/**sitewomen**/urls.py
```python
from django.contrib import admin
from django.urls import path, include  # импортируем include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('women.urls')),  # если в приложении создали urls.py
]
```
Создаем converter.py и закидываем туда класс и регистрируем его
sitewomen/**women**/converters.py
```python
class FourDigitYearConverter:
    regex = "[0-9]{4}"

    def to_python(self, value):
        return int(value)

    def to_url(self, value):
        return "%04d" % value
```
sitewomen/**women**/urls.py
```python
from django.urls import path, register_converter
from . import views
from . import converters # 333 2 

register_converter(converters.FourDigitYearConverter, "year4")

urlpatterns = [
    path('', views.index),
    path('cats/<int:cat_id>/', views.categories),
    path('cats/<slug:cat_slug>/', views.categories_by_slug),
    path('archive/<year4:year>/', views.archive),  #333  
]
```
sitewomen/**women**/views.py
```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request): #HttpRequest
    return HttpResponse("Страница приложения women.")

def categories(request, cat_id):
    return HttpResponse(f"<h1>Куча категорий</h1><p>id: {cat_id}</p>")

def categories_by_slug(request, cat_slug):
    return HttpResponse(f"<h1>Куча категорий</h1><p>slug: {cat_slug}</p>")

def archive(request, year):
    return HttpResponse(f"<h1>Архив</h1><p>regular: {year}</p>")
```
