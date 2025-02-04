## Формы связанные с моделью
>[!abstract] sitewomen/women/views.py 

Пропишем вьюху
```python
def addpage(request):
    return render(
        request, "women/addpage.html", {"menu": menu, "title": "Добавление статьи"}
    )
```
Страница с формой
* method="POST" метод для форм, который скрывает данные при передаче
* {% csrf_token %} токен который дает понять движку джанго что данные присланы с страницы сайта
* action="" если пустой то данные с формы обрабатывает та же функция что и отображает страницу с формой ( в нашем случае - addpage())
* name="field_text" имя для переменной в форме (видно в строке браузера)
```django
{% extends 'base.html' %}

{% block content %}
<h1>{{title}}</h1>
<form action="" method="POST">
    {% csrf_token %}
    <p>
        <label for="id_1">Текст:</label>
        <input type="text" id="id_1" name="field_text">
    </p>
    <p>
        <label for="id_2">Отметка:</label>
        <input type="checkbox" id="id_2" name="field_check">
    </p>
    <p>
        <label for="id_3">Число:</label>
        <input type="number" id="id_3" name="field_num">
    </p>
    <p>
        <label for="id_4">Пароль:</label>
        <input type="password" id="id_4" name="field_psw">
    </p>
    <p><button type="submit">Отправить</button></p>
</form>
{% endblock %}
```
> [!info] реквест от ввода можно ловить вотладке вскоде через `request.POST`
## Формы не связанные с моделью
##