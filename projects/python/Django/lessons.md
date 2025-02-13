#python #django #posts #tags
## 27. Отображение постов по рубрикам
### прописываем маршрут в urls.py
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
    path("category/<slug:cat_slug>/", views.show_category, name="category"), # тут
]
```
### меняем функцию в views.py
В функции `show_category`  передаем `cat_slug`, пропишем переменную category, которая будет делать выборку с таблици **Category**, по переданному  `slug` и в случае не имения подобной категории возвращать ошибку. с помощью метода `get_object_or_404()`
Так же  пропишем перемунную `post`, которая будет выводить статьи из таблицы по  **Women** по связаных категорий `cat_id` из таблици `Category` (cat_slug) с помощью кастомного менеджера `published` из manage.py

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
  
from .models import Women, Category

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

def show_category(request, cat_slug):
    category = get_object_or_404(Category, slug=cat_slug)
    post = Women.published.filter(cat_id=category.pk)

    data = {
        "title": f"Рубрика {category.name}",
        "menu": menu,
        "posts": post,
        "cat_selected": category.pk,
    }
    return render(request, "women/index.html", context=data)

def page_not_found(request, exception):
    return HttpResponseNotFound("<h1>Страница не найдена</h1>")
```
> [!warning] Мы делаем две переменные на две таблицы, что б оптимизировать sql запросы, иначе - разместив всё в одном запросе, при вызове в *data* - `category.name` и `category.pk` у нас бы выполнялись дополнительные запросы к базе
### Рубрики women_tags.py
Не забываем, что рубрики у нас отображаются с пользовательского тега **templatetags/women_tags.py**
Пропишем в нем вызов пользовательсткого шаблона категорий с категориями с нашей таблицы Category, предварительно выбрав их из нее.
> [!abstract] `sitewomen/women/templatetags/women_tags.py`
```python
from django import template
import women.views as views
from women.models import Category

register = template.Library()

@register.inclusion_tag("women/list_categories.html")
def show_categories(cat_selected=0):
    cats = Category.objects.all()
    return {"cats": cats, "cat_selected": cat_selected}
```
Поменяем ссылку в шаблоне на абсолютный метод (пропишем его в модели нашей таблицы **Categories**)
> [!abstract] `sitewomen/women/templates/women/list_categories.html`
```django
{% for cat in cats %}
    {% if cat.id == cat_selected %}
        <li class='selected'>{{cat.name}}</li>
    {% else %}
        <li><a href="{{ cat.get_absolute_url }}">{{ cat.name }}</a></li>
    {% endif %}
{% endfor %}
```
### models.py
Пропишем абсолютный метод (`get_absolute_url`) для таблицы **Category**
> [!abstract] `sitewomen/women/models.py`
```python
from pyexpat import model
from django.db import models
from django.urls import reverse

class PublishedManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(is_published=Women.Status.PUBLISHED)

# Create your models here.
class Women(models.Model):
    
    class Status(models.IntegerChoices):
        DRAFT = 0, "Черновик"
        PUBLISHED = 1, "Опубликовано"

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
    is_published = models.BooleanField(
        choices=Status.choices, default=Status.PUBLISHED
    )  # по умолчанию - опубликовано

    objects = models.Manager()  # возвращаем менеджер по умолчанию
    published = PublishedManager()  # наш кастомный менеджер

    cat = models.ForeignKey("Category", on_delete=models.PROTECT, related_name="posts")

    def __str__(self):
        return self.title

    class Meta:
        ordering = ["-time_create"]  # указываем обратную сортировку по времени создания
        indexes = [models.Index(fields=["-time_create"])]

    def get_absolute_url(self):
        return reverse("post", kwargs={"post_slug": self.slug})


class Category(models.Model):
    name = models.CharField(max_length=100, db_index=True)
    slug = models.SlugField(max_length=254, unique=True, db_index=True)

    def __str__(self):
        return self.name

    def get_absolute_url(self):
        return reverse("category", kwargs={"cat_slug": self.slug})
```
> [!info]  Добавим в шаблон вывод  имени категории и даты создания статьи
```django
{% extends 'base.html' %}
{% block content %}
  <ul class="list-articles">
    {% for p in posts %}
        <li>
          <div class="article-panel">
            <p class="first">Категория: {{p.cat.name}}</p>
            <p class="last">Дата: {{p.time_update | date:"d-m-Y H:i:s"}}</p>
          </div>
          <h2>{{ p.title }}</h2>
          {% autoescape off %}
            {{ p.content|linebreaks|truncatewords:40 }}
          {% endautoescape %}
          <div class="clear"></div>
          <p class="link-read-post">
            <a href="{{ p.get_absolute_url }}">Читать пост</a>
          </p>
        </li>
    {% endfor %}
  </ul>
{% endblock %}
```

## 29 Добавление тегов на сайт
### Добавим новый маршрут для отображения страници тегов (`tags`)
>[!abstract] urls.py
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
    path("category/<slug:cat_slug>/", views.show_category, name="category"),
    path("tags/<slug:tag_slug>/", views.show_tag_postlist, name="tag"),
]
```
### Определим фун-ю представления **show_tag_postlist**, для отображения страници отсортированную по тегам 
>[!abstract] views.py
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

from .models import TagPost, Women, Category

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

def show_category(request, cat_slug):
    category = get_object_or_404(Category, slug=cat_slug)
    post = Women.published.filter(cat_id=category.pk)

    data = {
        "title": f"Рубрика: {category.name}",
        "menu": menu,
        "posts": post,
        "cat_selected": category.pk,
    }
    return render(request, "women/index.html", context=data)

def show_tag_postlist(request, tag_slug):
    tag = get_object_or_404(TagPost, slug=tag_slug)
    posts = tag.tags.filter(is_published=Women.Status.PUBLISHED)

    data = {
        "title": f"Тег: {tag.tag}",
        "menu": menu,
        "posts": posts,
        "cat_selected": None,
    }
    return render(request, "women/index.html", context=data)

def page_not_found(request, exception):
    return HttpResponseNotFound("<h1>Страница не найдена</h1>")
```
### Добавим абсолютный маршрут к классу тегов
>[!abstract] models.py
```python
from pyexpat import model
from django.db import models
from django.urls import reverse

class PublishedManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(is_published=Women.Status.PUBLISHED)

# Create your models here.
class Women(models.Model):

	class Status(models.IntegerChoices):
        DRAFT = 0, "Черновик"
        PUBLISHED = 1, "Опубликовано"

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
    is_published = models.BooleanField(
        choices=Status.choices, default=Status.PUBLISHED
    )  # по умолчанию - опубликовано
    cat = models.ForeignKey("Category", on_delete=models.PROTECT, related_name="posts")
    tags = models.ManyToManyField("TagPost", blank=True, related_name="tags")

    objects = models.Manager()  # возвращаем менеджер по умолчанию
    published = PublishedManager()  # наш кастомный менеджер

    def __str__(self):
        return self.title

    class Meta:
        ordering = ["-time_create"]  # указываем обратную сортировку по времени создания
        indexes = [models.Index(fields=["-time_create"])]

    def get_absolute_url(self):
        return reverse("post", kwargs={"post_slug": self.slug})

class Category(models.Model):
    name = models.CharField(max_length=100, db_index=True)
    slug = models.SlugField(max_length=254, unique=True, db_index=True)

    def __str__(self):
        return self.name

    def get_absolute_url(self):
        return reverse("category", kwargs={"cat_slug": self.slug})

class TagPost(models.Model):
    tag = models.CharField(max_length=100, db_index=True)
    slug = models.SlugField(max_length=255, unique=True, db_index=True)

    def __str__(self):
        return self.tag

    def get_absolute_url(self):
        return reverse("tag", kwargs={"tag_slug": self.slug})  # "tag" маршрут с urls.py (name='tag') возвращает tag_slug c значением slug в данном случае из таблици TagPost
```
### Пропишем функцию шаблона **show_all_tags** для сайдбара в котором будут отображаться все теги 
> [!abstract] `sitewomen/women/templatetags/women_tags.py`
```python
from django import template
import women.views as views
from women.models import Category, TagPost

register = template.Library()

@register.inclusion_tag("women/list_categories.html")
def show_categories(cat_selected=0):
    cats = Category.objects.all()
    return {"cats": cats, "cat_selected": cat_selected}

@register.inclusion_tag("women/list_tags.html")
def show_all_tags():
    return {"tags": TagPost.objects.all()}
```
#### шаблон для него
> [!abstract] `sitewomen/women/templates/women/list_tags.html`
```django
{% if tags %}
    <p>Теги:</p>
    <ul class="tags-list">
        {% for t in tags %}
        <li><a href="{{ t.get_absolute_url }}">{{t.tag}}</a></li>
        {% endfor %}
    </ul>
{% endif %}
```
#### вставим етот шаблон в base.html
> [!abstract] `sitewomen/templates/base.html`
```django
{% load static %}
{%  load women_tags %}
<!DOCTYPE html>
<html>
<head>
    <title>{{title}}</title>
    <link type="text/css" href="{% static 'women/css/styles.css' %}" rel="stylesheet" />
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <link rel="shortcut icon" href="{% static 'women/images/main.ico' %}" type="image/x-icon"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>
<table class="table-page" border=0 cellpadding="0" cellspacing="0">
<tr><td valign=top>
{% block mainmenu %}
        <div class="header">
            <ul id="mainmenu" class="mainmenu">
            <li class="logo"><a href="{% url 'home' %}"><div class="logo"></div></a></li>
{% for m in menu %}
    {% if not forloop.last %}<li>{% else %}<li class="last">{% endif %}
        <a href="{% url m.url_name %}">{{m.title}}</a></li>
{% endfor %}
            </ul>
            <div class="clear"></div>
        </div>
{% endblock mainmenu %}

<table class="table-content" border=0 cellpadding="0" cellspacing="0">
<tr>
<!-- Sidebar слева -->
    <td valign="top" class="left-chapters">
    <ul id="leftchapters">
        {% if cat_selected == 0 %}
            <li class="selected">Все категории</li>
        {% else %}
            <li><a href="{% url 'home' %}">Все категории</a></li>
        {% endif %}
        {% show_categories cat_selected %}
        <li class="share">
        <p>Наш канал</p>
        <a class="share-yt" href="https://www.youtube.com/channel/UClJzWfGWuGJL2t-3dYKcHTA/playlists" target="_blank" rel="nofollow"></a>
        </li>
		<!-- теги -->
        <li>{% show_all_tags %}</li> 
    </ul>
</td>
<!-- Конец Sidebar'а -->
<td valign="top" class="content">
    <!-- Хлебные крошки -->
    {% block breadcrumbs %}
    {% endblock %}
<!-- Блок контента -->
    <div class="content-text">
{% block content %}
{% endblock %}
    </div>
<!-- Конец блока контента -->
</td></tr></table>
</td></tr>
<!-- Footer -->
<tr><td valign=top>
    <div id="footer">
        <p>&copy; 2023 Частичное или полное копирование информации с данного сайта для распространения на других ресурсах, в том числе и бумажных, строго запрещено.</p>
    </div>
</td></tr></table>
<!-- Конец footer'а и страницы -->
</body>
</html>
```

### Добавим отображение тегов на страницe постов
>[!abstract] `sitewomen/women/templates/women/post.html`
```django
{% extends "base.html" %}

<!-- блок тегов -->
{% block breadcrumbs %}
{% with post.tags.all as tags %}
{% if tags %}
    <ul class="tags-list">
        <li>Теги: </li>
        {% for t in tags %}
            <li><a href="{{ t.get_absolute_url }}">{{ t.tag }}</a></li>
        {% endfor %}
</ul>
{% endif %}
{% endwith %}
{% endblock breadcrumbs %}

<!-- блок контента -->
{% block content %}
    <h1>{{post.title}}</h1>
    {% if post.photo %}
        <p><img src="{{post.photo.url}}" class="img-article-left"></p>
    {% endif %}
    {{post.content | linebreaks}}
{% endblock content %}
```
##
##