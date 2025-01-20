### Slug links, также get_absolute_url
Отображение ссылок по слагам, так же функция
#### Создаем в таблице новую ячейку для слагов
```python
from django.db import models
from django.urls import reverse

# Create your models here.
class Women(models.Model):
    title = models.CharField(max_length=255)  # название статьи
    slug = models.SlugField(
        max_length=255,
        unique=True,
        db_index=True,
    )  # слаги, для начала unique=True отключаем и добавляем default='' пустые строки, которые заполним в запросе ниже
    content = models.TextField(
        blank=True
    )  # текстовое поле, blank=True - можно не заполнять при создании
    time_create = models.DateTimeField(auto_now_add=True)  # время создания
    time_update = models.DateTimeField(auto_now=True)  # время обновления
    is_published = models.BooleanField(default=True)  # по умолчанию - опубликовано
  
    def __str__(self):
        return self.title
  
    class Meta:
        ordering = ["-time_create"]  # указываем обратную сортировку по времени создания
        indexes = [models.Index(fields=["-time_create"])]

    def get_absolute_url(self):  #  функция для абсолютных ссылок 
        return reverse("post", kwargs={"post_slug": self.slug})
```
#### Меняем в шаблоне ссылку на функцию 
```django
{% extends 'base.html' %}
{% block content %}
  <ul class="list-articles">
    {% for p in posts %}
      {% if p.is_published %}
        <li>
          <h2>{{ p.title }}</h2>
          {% autoescape off %}
            {{ p.content|linebreaks|truncatewords:40 }}
          {% endautoescape %}
          <div class="clear"></div>
          <p class="link-read-post">
            <a href="{{ p.get_absolute_url }}">Читать пост</a>
          </p>
        </li>
      {% endif %}
    {% endfor %}
  </ul>
{% endblock %}
```
#### Меняем в `urls.py` ссылку на посты
```python
from django.urls import path, re_path, register_converter
from . import views
from . import converters

register_converter(converters.FourDigitYearConverter, "year4")

urlpatterns = [
    path("", views.index, name="home"),  # http://127.0.0.1:8000
    path("about/", views.about, name="about"),
    path("addpage/", views.addpage, name="add_page"),
    path("contact/", views.contact, name="contact"),
    path("login/", views.login, name="login"),
    path("post/<slug:post_slug>/", views.show_post, name="post"),
    path("category/<int:cat_id>/", views.show_category, name="category"),
]
```
#### Меняем функцию представления `show_post`
```python
from django.http import (
    HttpResponse,
    HttpResponseNotFound,
    Http404,
    HttpResponseRedirect,
    HttpResponsePermanentRedirect,
)
from django.shortcuts import get_object_or_404, render, redirect
from django.urls import reverse
from django.template.loader import render_to_string
from django.template.defaultfilters import slugify

from .models import Women

menu = [
    {"title": "О сайте", "url_name": "about"},
    {"title": "Добавить статью", "url_name": "add_page"},
    {"title": "Обратная связь", "url_name": "contact"},
    {"title": "Войти", "url_name": "login"},
]

data_db = [
    {
        "id": 1,
        "title": "Анджелина Джоли",
        "content": """<h1>Анджелина Джоли</h1> (англ. Angelina Jolie[7], при рождении Войт (англ. Voight), ранее Джоли Питт (англ. Jolie Pitt); род. 4 июня 1975, Лос-Анджелес, Калифорния, США) — американская актриса кино, телевидения и озвучивания, кинорежиссёр, сценаристка, продюсер, фотомодель, посол доброй воли ООН.
    Обладательница премии «Оскар», трёх премий «Золотой глобус» (первая актриса в истории, три года подряд выигравшая премию) и двух «Премий Гильдии киноактёров США».""",
        "is_published": True,
    },
    {
        "id": 2,
        "title": "Марго Робби",
        "content": "Биография Марго Робби",
        "is_published": False,
    },
    {
        "id": 3,
        "title": "Джулия Робертс",
        "content": "Биография Джулия Робертс",
        "is_published": True,
    },
]

cats_db = [
    {"id": 1, "name": "Актрисы"},
    {"id": 2, "name": "Певицы"},
    {"id": 3, "name": "Спортсменки"},
    {"id": 4, "name": "Модели"},
]

def index(request):
    posts = Women.objects.filter(is_published=1)
    data = {
        "title": "Главная страница",
        "menu": menu,
        "posts": posts,
        "cat_selected": 0,
    }
    return render(request, "women/index.html", context=data)

def about(request):
    return render(request, "women/about.html", {"title": "О сайте", "menu": menu})

def show_post(request, post_slug):
    post = get_object_or_404(Women, slug=post_slug)
    data = {
        "title": post.title,
        "menu": menu,
        "post": post,
        "cat_selected": 1,
    }
    return render(request, "women/post.html", data)

def addpage(request):
    return HttpResponse("Добавление статьи")

def contact(request):
    return HttpResponse("Обратная связь")

def login(request):
    return HttpResponse("Авторизация")

def show_category(request, cat_id):
    data = {
        "title": "Отбражение по рубрикам",
        "menu": menu,
        "posts": data_db,
        "cat_selected": cat_id,
    }
    return render(request, "women/index.html", context=data)

def page_not_found(request, exception):
    return HttpResponseNotFound("<h1>Страница не найдена</h1>")
```
####
### Пользовательские модели (например обращение к базе)
Опишем в `models.py` новый менеджер для нашей модели `Women`. Создадим новый клас **PublishedManager**(название может быть любое), который будет наследоваться от **models.Manager**.
Определим в нем специальный метод `get_queryset` (то что вернет этот метод и будет возращать нам класс `PublishedManager`).
Обращаемся к базовому классу `models.Manager` через метод `super`, в нем вызовем метод `get_queryset`, который вызовет все наши записи и отфильтруем вывод по `is_published=1`, который выведет только опубликованные записи
Далее в нашей таблице(классе Women) определим новый менеджер `published = PublishedManager()`
```python
from django.db import models
from django.urls import reverse

class PublishedManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(is_published=1)

# Create your models here.
class Women(models.Model):
    title = models.CharField(max_length=255)  # название статьи
    slug = models.SlugField(
        max_length=255,
        unique=True,
        db_index=True,
    )
    content = models.TextField(
        blank=True
    )  # текстовое поле, blank=True - можно не заполнять при создании
    time_create = models.DateTimeField(auto_now_add=True)  # время создания
    time_update = models.DateTimeField(auto_now=True)  # время обновления
    is_published = models.BooleanField(default=True)  # по умолчанию - опубликовано

    objects = models.Manager()  # возвращаем менеджер по умолчанию
    published = PublishedManager()  # наш кастомный менеджер

    def __str__(self):
        return self.title

    class Meta:
        ordering = ["-time_create"]  # указываем обратную сортировку по времени создания
        indexes = [models.Index(fields=["-time_create"])]

    def get_absolute_url(self):
        return reverse("post", kwargs={"post_slug": self.slug})
```
> [!danger] Теперь наш сервер будет выдавать ошибку, так как стандарный метод Women.objects станет более не доступен, как только мы переопределяем свой собственный менеджер в таблице.

> [!info] Что бы он продолжал существовать его нужно отдельно прописать в ташей таблице 
> `objects = models.Manager()`

Также поменяем вызов данных с таблици на наш новый метод `published`
```python
from django.http import (
    HttpResponse,
    HttpResponseNotFound,
    Http404,
    HttpResponseRedirect,
    HttpResponsePermanentRedirect,
)
from django.shortcuts import get_object_or_404, render, redirect
from django.urls import reverse
from django.template.loader import render_to_string
from django.template.defaultfilters import slugify
  
from .models import Women

menu = [
    {"title": "О сайте", "url_name": "about"},
    {"title": "Добавить статью", "url_name": "add_page"},
    {"title": "Обратная связь", "url_name": "contact"},
    {"title": "Войти", "url_name": "login"},
]

data_db = [
    {
        "id": 1,
        "title": "Анджелина Джоли",
        "content": """<h1>Анджелина Джоли</h1> (англ. Angelina Jolie[7], при рождении Войт (англ. Voight), ранее Джоли Питт (англ. Jolie Pitt); род. 4 июня 1975, Лос-Анджелес, Калифорния, США) — американская актриса кино, телевидения и озвучивания, кинорежиссёр, сценаристка, продюсер, фотомодель, посол доброй воли ООН.
    Обладательница премии «Оскар», трёх премий «Золотой глобус» (первая актриса в истории, три года подряд выигравшая премию) и двух «Премий Гильдии киноактёров США».""",
        "is_published": True,
    },
    {
        "id": 2,
        "title": "Марго Робби",
        "content": "Биография Марго Робби",
        "is_published": False,
    },
    {
        "id": 3,
        "title": "Джулия Робертс",
        "content": "Биография Джулия Робертс",
        "is_published": True,
    },
]

cats_db = [
    {"id": 1, "name": "Актрисы"},
    {"id": 2, "name": "Певицы"},
    {"id": 3, "name": "Спортсменки"},
    {"id": 4, "name": "Модели"},
]

def index(request):
    posts = Women.published.all()
    data = {
        "title": "Главная страница",
        "menu": menu,
        "posts": posts,
        "cat_selected": 0,
    }
    return render(request, "women/index.html", context=data)

def about(request):
    return render(request, "women/about.html", {"title": "О сайте", "menu": menu})

def show_post(request, post_slug):
    post = get_object_or_404(Women, slug=post_slug)

    data = {
        "title": post.title,
        "menu": menu,
        "post": post,
        "cat_selected": 1,
    }
    return render(request, "women/post.html", data)

def addpage(request):
    return HttpResponse("Добавление статьи")

def contact(request):
    return HttpResponse("Обратная связь")

def login(request):
    return HttpResponse("Авторизация")

def show_category(request, cat_id):
    data = {
        "title": "Отбражение по рубрикам",
        "menu": menu,
        "posts": data_db,
        "cat_selected": cat_id,
    }
    return render(request, "women/index.html", context=data)

def page_not_found(request, exception):
    return HttpResponseNotFound("<h1>Страница не найдена</h1>")
```
#### choices (определяем осмысленные имена) 


###
###