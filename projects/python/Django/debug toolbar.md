**Django Debug Toolbar**
* скорость загрузки приложений
* нагрузку на СУБД (частоту и сложность запросов)
* корректность возвращаемых пользователю данных
### Установка
В рабочей среде (в venv) устанавливаем как **pip** пакет
```cmd
pip install django-debug-toolbar
```
Дпльше плагин нужно подключить по инструкции по офф [cсылке](https://django-debug-toolbar.readthedocs.io/en/latest/installation.html)
### Убираем дублирующие запросы базы данных
Для правки можно использовать метод:
* `select_related(key)` - "жадная" загрузка связанных данных по внешнему ключу key, который имеет тип **ForeignKey**;
* `prefetch_related(key)`- "жадная" загрузка связанных данных по внешнему ключу key, который имеет тип **ManyToManyField**.

На странице дебага страници видим дублирующие запросі к базе
![](files/Pasted%20image%2020250202000901.png)

Как видно из скриншота, дублирование у нас вызывает вызов категорий на главной странице сайта (index.html)
Применим к выборке с таблици Women метод `select_related("cat")` для дополнительной выборки и из таблици Category по связанному полю "cat" в таблице Women
>[!abstract] sitewomen>women.views.py 
```python
def index(request):
    posts = Women.published.all().select_related("cat")
    data = {
        "title": "Главная страница",
        "menu": menu,
        "posts": posts,
        "cat_selected": 0,
    }
    return render(request, "women/index.html", context=data)
```
Видим что дублирование пропало
![](files/Pasted%20image%2020250202002407.png)
