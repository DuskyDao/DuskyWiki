#python #django #redirect #HttpResponseRedirect #HttpResponsePermanentRedirect
#### Пример использования функции redirect
Импортируем функцию и прописываем перенаправление, если `year > 2023`
/sitewomen/**women**/views.py
> **302** - страница перемещена временно на другой URL-адрес
```python
from django.shortcuts import render, redirect
from django.http import HttpResponse
  
def archive(request, year):
    if year > 2023:
        return redirect('/')
    return HttpResponse(f"<h1>Архив</h1><p>regular: {year}</p>")
```
> **301** - страница перемещена на другой постоянный URL-адрес
```python
from django.shortcuts import render, redirect
from django.http import HttpResponse
  
def archive(request, year):
    if year > 2023:
        return redirect('/', permanent=True)
    return HttpResponse(f"<h1>Архив</h1><p>regular: {year}</p>")
```
Вместо указания пути `redirect('/', permanent=True)` можно указывать функцию, например `index`
```python
from django.shortcuts import render, redirect
from django.http import HttpResponse
  
def archive(request, year):
    if year > 2023:
        return redirect(index, permanent=True)
        #return redirect(index)
    return HttpResponse(f"<h1>Архив</h1><p>regular: {year}</p>")
```
#### Правильный синтаксис с использованием имен в маршрутах
Для большей читабельности и для универсальности кода, принято в маршрутах прописывать имена/псевдонимы типа `name='cats_id'`
/sitewomen/**women**/urls.py
```python
from django.urls import path, re_path, register_converter #333 1
from . import views
from . import converters # 333 2 создаем converter.py и закидіваем туда класс и регистрируем его
  
register_converter(converters.FourDigitYearConverter, "year4")

urlpatterns = [
    path('', views.index, name='home'),
    path('cats/<int:cat_id>/', views.categories, name='cats_id'),
    path('cats/<slug:cat_slug>/', views.categories_by_slug, name='cats'),
    # re_path(r"^archive/(?P<year>[0-9]{4})/", views.archive, name='archive'),
    path('archive/<year4:year>/', views.archive, name='archive'),  #333
]
```
далее в вьюшке 
/sitewomen/**women**/views.py
```python
from django.shortcuts import render, redirect
from django.http import HttpResponse

def categories_by_slug(request, cat_slug):
	if cat_slug == 'music':
        return redirect('cats', 'limp_bizkit')  # при вводе cats/music => cats/limp_bizkit
    return HttpResponse(f"<h1>Куча категорий</h1><p>slug: {cat_slug}</p>")

def archive(request, year):
    if year > 2023:
        return redirect('home')
    return HttpResponse(f"<h1>Архив</h1><p>regular: {year}</p>")

```
#### Можно использовать так же функцию **reverse** предварительно импортировав её
```python
from django.shortcuts import render, redirect
from django.http import HttpResponse
from django.urls import reverse

def categories_by_slug(request, cat_slug):
    if cat_slug == 'music':
        ury = reverse('cats', args=('limb_bizkit', ))
        return redirect(ury, permanent=True)
    return HttpResponse(f"<h1>Куча категорий</h1><p>slug: {cat_slug}</p>")

def archive(request, year):
    if year > 2023:
        ury = reverse('home')
        return redirect(ury)
    return HttpResponse(f"<h1>Архив</h1><p>regular: {year}</p>")
```
#### Вместо функции **redirect** можно использовать классы **HttpResponseRedirect** *(код 302) *и  **HttpResponsePermanentRedirect** *(код 301)*
```python
from django.shortcuts import render
from django.http import HttpResponse, HttpResponseRedirect, HttpResponsePermanentRedirect
from django.urls import reverse

def categories_by_slug(request, cat_slug):
    if cat_slug == 'music':
        ury = reverse('cats', args=('limb_bizkit', ))
        return HttpResponseRedirect(ury)  # код 302
    return HttpResponse(f"<h1>Куча категорий</h1><p>slug: {cat_slug}</p>")

def archive(request, year):
    if year > 2023:
        ury = reverse('home')
        return HttpResponsePermanentRedirect(ury)  # код 301
        #return HttpResponsePermanentRedirect('home')  # либо без reverse
    return HttpResponse(f"<h1>Архив</h1><p>regular: {year}</p>")
```