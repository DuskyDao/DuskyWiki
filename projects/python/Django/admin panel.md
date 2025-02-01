### Установим нужную локаль
> [!abstract] sitewomen>manage.py
```python
LANGUAGE_CODE = 'uk_UA'
```
### Добавим суперпользователя на сайт
```cmd
python manage.py createsuperuser
```
### Подключим модель Women (таблицу)
> [!abstract] sitewomen>women>admin.py
```python
from django.contrib import admin
from .models import Women

# Register your models here.
admin.site.register(Women)
```
> [!attention] кнопка в открытой таблице **смотреть на сайте** активна только если в класс Women добавлен метод `get_absolute_url`
### Поменяем текст кнопок в админ панели
* admin.site.site_header - шапку админ панели
* admin.site.index_title - заголовок главной страницы панели

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
> [!abstract] sitewomen>women>apps.py
```python
from django.apps import AppConfig

class WomenConfig(AppConfig):
    verbose_name = "Женщины мира"  # имя для админ панели
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'women'
```
### Поменяем имя нашей таблицы Women
В классе **Women** в подклассе **Meta** пропишем наши названия (для одиночного и множественного числа)
> [!abstract] sitewomen>women>models.py
```python
class Wonem(models.Model):
	#...
	
	class Meta:
        verbose_name = "Известные женщины"  # в единственном числе
        verbose_name_plural = "Известные женщины"  # в множественном
```