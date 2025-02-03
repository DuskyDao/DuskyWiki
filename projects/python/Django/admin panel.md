## Установка
[Оф дока](https://docs.djangoproject.com/en/4.2/ref/contrib/admin/)
### Установим нужную локаль
> [!abstract] sitewomen>manage.py
```python
LANGUAGE_CODE = 'uk_UA'
```
### Добавим суперпользователя на сайт
```cmd
python manage.py createsuperuser
```
## Регистрация моделей
### Подключим модель Women (таблицу)
> [!abstract] sitewomen>women>admin.py
```python
from django.contrib import admin
from .models import Women

# Register your models here.
admin.site.register(Women)
```
> [!attention] кнопка в открытой таблице **смотреть на сайте** активна только если в класс Women добавлен метод `get_absolute_url`

Например, что бы добавить отображение других полей нашей таблицы Women как на рисунке
![](files/Pasted%20image%2020250202170323.png)
в админ панели, нужно `WomenAdmin(admin.ModelAdmin)` и прописать в нему следующие переменные (не забываем зарегистрировать этот класс ниже):
> [!abstract] sitewomen>women>admin.py
```python
from django.contrib import admin
from .models import Women

class WomenAdmin(admin.ModelAdmin):
    list_display = ("id", "title", "time_create", "is_published",)  # добавляем отображение дополнительных колонок
    list_display_links = ("id", "title")  # кликабельные ссылки
    ordering = ["-time_create", "title"]  # сортировка

admin.site.register(Women, WomenAdmin)
```
Для удобства мы можем строку регистрации прописать как декоратор, что является более читабельным вдом
> [!abstract] sitewomen>women>admin.py
```python
from atexit import register
from django.contrib import admin
from .models import Women

@admin.register(Women)
class WomenAdmin(admin.ModelAdmin):
    list_display = ("id","title","time_create","is_published",)
    list_display_links = ("id", "title")
    ordering = ["-time_create", "title"]  # сортировка
```

## Визуал
### Поменяем текст кнопок в админ панели
* admin.site.site_header - шапку админ панели
* admin.site.index_title - заголовок главной страницы панели
![](files/Pasted%20image%2020250202173209.png)
> [!abstract] sitewomen>urls.py
```python
from django.contrib import admin
from django.urls import path, include
from women.views import page_not_found
from debug_toolbar.toolbar import debug_toolbar_urls

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("women.urls")),
] + debug_toolbar_urls()

handler404 = page_not_found

admin.site.site_header = "Панель администрирования"
admin.site.index_title = "Известные женщины мира"
```
### Поменяем имя нашего приложения
![](files/Pasted%20image%2020250202173253.png)
> [!abstract] sitewomen>women>apps.py
```python
from django.apps import AppConfig

class WomenConfig(AppConfig):
    verbose_name = "Женщины мира"  # имя для админ панели
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'women'
```
### Поменяем имя нашей таблицы Women
![](files/Pasted%20image%2020250202173317.png)
В классе **Women** в подклассе **Meta** пропишем наши названия (для одиночного и множественного числа)
> [!abstract] sitewomen>women>models.py
```python
class Wonem(models.Model):
	#...
	
	class Meta:
        verbose_name = "Известные женщины"  # в единственном числе
        verbose_name_plural = "Известные женщины"  # в множественном
```
### Поменяем имена заголовков колонок
![](files/Pasted%20image%2020250202173428.png)
Добавим в класс Women к колонкам `verbose_name=""`
> [!abstract] sitewomen>women>models.py
```python
class Women(models.Model):

    class Status(models.IntegerChoices):
        DRAFT = 0, "Черновик"
        PUBLISHED = 1, "Опубликовано"

    title = models.CharField(max_length=255, verbose_name="Заголовок")  # название статьи
    slug = models.SlugField(max_length=255, unique=True, db_index=True, verbose_name="slug")
    content = models.TextField(blank=True, verbose_name="Текст статьи")  # текстовое поле, blank=True - можно не заполнять при создании
    time_create = models.DateTimeField(auto_now_add=True, verbose_name="Время создания")  # время создания
    time_update = models.DateTimeField(auto_now=True, verbose_name="Время изменения")  # время обновления
    is_published = models.BooleanField(choices=Status.choices, default=Status.DRAFT, verbose_name="Статус")  # по умолчанию - опубликовано
    cat = models.ForeignKey("Category", on_delete=models.PROTECT, related_name="posts",
        verbose_name="Ктегории",)  # связь для категорий
    tags = models.ManyToManyField("TagPost", blank=True, related_name="tags", verbose_name="Теги"
    )  # связь для тегов
    husband = models.OneToOneField("Husband", on_delete=models.SET_NULL,null=True, blank=True,
        related_name="wuman", verbose_name="Муж",)  # связь для мужей
```
### Возможность редактирования поля с окна предосмотра
![](files/Pasted%20image%2020250202183742.png)
Добавим аргумент `list_editable = ["is_published"]`
> [!abstract] sitewomen>women>admin.py
```python
from django.contrib import admin
from .models import Category, TagPost, Women

@admin.register(Women)
class WomenAdmin(admin.ModelAdmin):
    list_display = (
        "id",
        "title",
        "time_create",
        "is_published",
    )
    list_display_links = ("id", "title")
    ordering = ["-time_create", "title"]
    list_editable = [
        "is_published",
    ]
```
> [!info] В нашем случає нужно закостылить поле `is_published` в классе models.py>Women , так как изначально оно возращает цифры (0 и 1), а оснастка джанго работает с булевыми значениями. Преобразуем их

> [!abstract] sitewomen>women>models.py
```python
class Women(models.Model):

    class Status(models.IntegerChoices):
        DRAFT = 0, "Черновик"
        PUBLISHED = 1, "Опубликовано"
#......
    is_published = models.BooleanField(
        choices=tuple(map(lambda x: (bool(x[0]), x[1]), Status.choices)),
        default=Status.DRAFT,
        verbose_name="Статус",
    )  # стало
    # is_published = models.BooleanField(choices=Status.choices, default=Status.DRAFT, verbose_name="Статус") # было
```
> [!warning] Так же нужно понимать, что редактируемыми полями могут быть только не ссылочные поля, без `list_display_links`
### Пагинация списка
Количество строк на старнице  list_per_page = 5
> [!abstract] sitewomen>women>admin.py
```python
@admin.register(Women)
class WomenAdmin(admin.ModelAdmin):
    list_display = (
        "id",
        "title",
        "time_create",
        "is_published",
        "cat",
    )
    list_display_links = ("id", "title")
    ordering = ["-time_create", "title"]
    list_editable = [
        "is_published",
    ]
    list_per_page = 5
```
## Пользовательские поля
### Поля таблиц
Можно добавлять кастомные поля, в нашем случае, в список статей класса Women, которых нет изначально в таблице
![](files/Pasted%20image%2020250202202411.png)
Добавим метод `brief_info` в клас Women который будет выводить количество символов в статье и добавим его вывод в `list_display`. Обернув метод в декоратор `@admin.display` - можно применять к полю некоторые методы:
* description="" - имя поля
* ordering="" - сортировка по полю (в нашем случае `content`)

> [!abstract] sitewomen>women>admin.py
```python
from django.contrib import admin
from .models import Category, TagPost, Women

@admin.register(Women)
class WomenAdmin(admin.ModelAdmin):
    list_display = ("id", "title", "time_create", "is_published", "cat", "brief_info",)
    list_display_links = ("id", "title")
    ordering = ["-time_create", "title"]
    list_editable = ("is_published",)
    list_per_page = 5

	@admin.display(description="Количество символов", ordering="content")
    def brief_info(self, women: Women):
        return f"Описание {len(women.content)} символов"
```
### Действия
Можно так же изменять это поле
![](files/Pasted%20image%2020250202211055.png)
Добавляется пункты так же с помощью методов к основному WomenAdmin.
Добавим массовое изменение статуса записей методами `set_published` и `set_draft`
* @admin.action - дает возможность работать с именем метода

> [!abstract] sitewomen>women>admin.py
```python
from django.contrib import admin, messages
from .models import Category, TagPost, Women

@admin.register(Women)
class WomenAdmin(admin.ModelAdmin):
    list_display = ("id", "title", "time_create", "is_published", "cat", "brief_info",)
    list_display_links = ("id", "title")
    ordering = ["-time_create", "title"]
    list_editable = ("is_published",)
    list_per_page = 5
    actions = ["set_published", "set_draft"]  # поле для кастомных действий

	@admin.display(description="Количество символов", ordering="content")
    def brief_info(self, women: Women):
        return f"Описание {len(women.content)} символов"

    @admin.action(description="Опубликовать выбранные")
    def set_published(self, request, queryset):
        count = queryset.update(is_published=Women.Status.PUBLISHED)
        self.message_user(request, f"Изменено {count} записей.")

    @admin.action(description="Снять с публикации выбранные")
    def set_draft(self, request, queryset):
        count = queryset.update(is_published=Women.Status.DRAFT)
        self.message_user(request, f"Снято с публикации {count} записей.", messages.WARNING)
```
> [!info]  messages.WARNING меняет визуальный вид сообщения об применении (messages нужно импортировать)
### Панель поиска
Включить поиск можно добавив аргумент `search_fields` в классе  WomenAdmin
> [!abstract] sitewomen>women>admin.py
```python
from django.contrib import admin, messages
from .models import Category, TagPost, Women

@admin.register(Women)
class WomenAdmin(admin.ModelAdmin):
	 search_fields = ["title__startswith", "cat__name"]
```
> [!info]  лукап `startswith` - поиск только по первым символам, `cat__name` - поиск по категориям
### Панель для фильтрации
За фильтрацию отвечает поле `list_filter`. В списке которого указываются поля по которым будет проходить фильтрация.
> [!abstract] sitewomen>women>admin.py
```python
from django.contrib import admin, messages
from .models import Category, TagPost, Women

@admin.register(Women)
class WomenAdmin(admin.ModelAdmin):
	list_filter = ["cat__name", "is_published"]
```
#### Кастомные фильтры
Джанго позволяет нам создавать свои фильтры. Для этого в admin.py пропишем новый класс **MarriedFilter**
```python
class MariedFilter(admin.SimpleListFilter):  # SimpleListFilter класс для кастомных фильтров
    title = "Статус женщин"  # имя поля в фильтре
    parameter_name = "status"  # переменная в поле ввода адреса браузера

    def lookups(self, request, model_admin):  # возращает значение запроса для переменной выше
        return [("married", "Замужем"), ("single", "Не замужем")]  # значения для переменной и имена для отображения в окне фильтра

    def queryset(self, request, queryset):
	    # return queryset  # для теста без значений фильтрования
        if self.value() == "married":  # возвращает значение параметра "status"
            return queryset.filter(husband__isnull=False)  #все записи поля husband которые не =0
        elif self.value() == "single":
            return queryset.filter(husband__isnull=True)   #все записи поля husband которые =0
```
### Формы
Действия в окне с формой данных таблици
> [!abstract] sitewomen>women>admin.py
```python
@admin.register(Women)
class WomenAdmin(admin.ModelAdmin):
    # fields = ["title", "content"]  # отображать только выбранные поля
    # exclude = ["title", "content"]  # поля которые исключаются
    # readonly_fields = ["title", "slug", "content"]  # поля которые нельзя редактировать
    # filter_horizontal = ["tags"]  # добавляет горизонтальный выбор для поля (работает для полей ManyToMany)
    # filter_vertical = ["tags"]  # добавляет вертикальный выбор для поля (работает для полей ManyToMany)
```
#### Автоматический **slug**
#django #slug
##### 1й способ
Добавим функцию save и перевод ру букв в англ функцию translit_to_eng
> [!abstract] sitewomen>women>models.py

```python
from django.db import models
from django.urls import reverse
from django.utils.text import slugify

# fmt: off
def translit_to_eng(s: str) -> str:
    d = {'а': 'a', 'б': 'b', 'в': 'v', 'г': 'g', 'д': 'd',
         'е': 'e', 'ё': 'yo', 'ж': 'zh', 'з': 'z', 'и': 'i', 'к': 'k',
         'л': 'l', 'м': 'm', 'н': 'n', 'о': 'o', 'п': 'p', 'р': 'r',
         'с': 's', 'т': 't', 'у': 'u', 'ф': 'f', 'х': 'h', 'ц': 'c', 'ч': 'ch',
         'ш': 'sh', 'щ': 'shch', 'ь': '', 'ы': 'y', 'ъ': '', 'э': 'r', 'ю': 'yu', 'я': 'ya'}

    return "".join(map(lambda x: d[x] if d.get(x, False) else x, s.lower()))
# fmt: on

class PublishedManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(is_published=Women.Status.PUBLISHED)

class Women(models.Model):
    class Status(models.IntegerChoices):
        DRAFT = 0, "Черновик"
        PUBLISHED = 1, "Опубликовано"
    title = models.CharField(
        max_length=255, verbose_name="Заголовок"
    )  # название статьи
    slug = models.SlugField(
        max_length=255, unique=True, db_index=True, verbose_name="slug"
    )
    content = models.TextField(
        blank=True, verbose_name="Текст статьи"
    )  # текстовое поле, blank=True - можно не заполнять при создании
    time_create = models.DateTimeField(
        auto_now_add=True, verbose_name="Время создания"
    )  # время создания
    time_update = models.DateTimeField(
        auto_now=True, verbose_name="Время изменения"
    )  # время обновления
    is_published = models.BooleanField(
        choices=tuple(map(lambda x: (bool(x[0]), x[1]), Status.choices)),
        default=Status.DRAFT,
        verbose_name="Статус",
    )

    cat = models.ForeignKey(
        "Category",
        on_delete=models.PROTECT,
        related_name="posts",
        verbose_name="Ктегории",
    )  # связь для категорий
    tags = models.ManyToManyField(
        "TagPost", blank=True, related_name="tags", verbose_name="Теги"
    )  # связь для тегов
    husband = models.OneToOneField(
        "Husband",
        on_delete=models.SET_NULL,
        null=True,
        blank=True,
        related_name="wuman",
        verbose_name="Муж",
    )  # связь для мужей
  
    objects = models.Manager()  # возвращаем менеджер по умолчанию
    published = PublishedManager()  # наш кастомный менеджер

    def __str__(self):
        return self.title

    class Meta:
        verbose_name = "Известные женщины"  # в единственном числе
        verbose_name_plural = "Известные женщины"  # в множественном

        ordering = ["-time_create"]  # указываем обратную сортировку по времени создания
        indexes = [models.Index(fields=["-time_create"])]

    def get_absolute_url(self):
        return reverse("post", kwargs={"post_slug": self.slug})

    def save(self, *args, **kwargs):
        self.slug = slugify(translit_to_eng(self.title))
        super().save(*args, **kwargs)
```
##### 2й способ
```python
@admin.register(Women)
class WomenAdmin(admin.ModelAdmin):
    prepopulated_fields = {"slug": ("title",)}
    # readonly_fields = ["slug"]
    
```
> [!warning] поле slug должно быть редактируемым. Баг при редактировании заголовка - слаг автоматом не меняется (нужно удалить заголовок статьи и сохранить, и по новой ввести новый заголовок)
### 
