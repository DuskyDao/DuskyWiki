## Формы не связанные с моделью
### Этап 1. Затычка
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
* {% csrf_token %} токен который дает понять движку джанго что данные присланы с страницы сайта, требуется для всех POST запросов
* action="" если пустой то данные с формы обрабатывает та же функция что и отображает страницу с формой ( в нашем случае - addpage())
* name="field_text" имя для переменной в форме (видно в строке браузера)
>[!abstract] sitewomen/women/templates/women/addpage.html 
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
> [!info] реквест от ввода можно ловить в отладке вскоде через `request.POST`
### Этап 2. Добавление статьи
В джанго формы добавляются автоматически по принципу классов таблиц. Создадим `sitewomen/women/forms.py` для прописывания классов для форм. Пропишем в нём форму AddPostForm - импортируем forms и таблици
>[!abstract] sitewomen/women/forms.py
```python
from django import forms
from .models import Category, Husband

class AddPostForm(forms.Form):
    title = forms.CharField(max_length=255)  # текстовое поле
    slug = forms.SlugField(max_length=255)  #  слаг
    content = forms.CharField(widget=forms.Textarea(), required=False)  # большое текстовое поле
    is_published = forms.BooleanField(required=False)  # чекер
    cat = forms.ModelChoiceField(
        queryset=Category.objects.all()
    )  # раскрівающееся меню с данными из таблиц
    husband = forms.ModelChoiceField(queryset=Husband.objects.all(), required=False)  # required=False - не обязательно к заполнению
```
> [!info]  [Оф дока](https://docs.djangoproject.com/en/4.2/ref/forms/fields/)

#### Усовершенствуєм функцию представления
Если в функцию передается запрос GET, то выводится просто пустая форма - нужно для открытия страницы в первый раз.  Если метод POST, то функция принимает значения введенные в форму, дальше значения проверяются на уровне движка на валидность с помощью метода `is_valid()` - если данные нормальные т опока прост овыведем их в консоль
```python
def addpage(request):
    if request.method == "POST":
        form = AddPostForm(request.POST)
        if form.is_valid():
            print(form.cleaned_data)
    else:
        form = AddPostForm()
  
    data = {"menu": menu, "title": "Добавление статьи", "form": form}
    return render(request, "women/addpage.html", data)
```
Добавим в функцию вывода страницы наш класс шаблона формы AddPostForm() и передадим его в шаблон страницы. Не забіваем импортировать класс формы.
>[!abstract] sitewomen/women/views.py
```python
def addpage(request):
    form = AddPostForm()
    data = {"menu": menu, "title": "Добавление статьи", "form": form}
    return render(request, "women/addpage.html", data)
```
Изменим шаблон для страницы с формой автоматом выводя все поля
>[!abstract] sitewomen/women/templates/women/addpage.html 
```django
{% extends 'base.html' %}
{% block content %}
<h1>{{title}}</h1>
<form action="" method="POST">
    {% csrf_token %}
    {{ form.as_p }} 
    <p><button type="submit">Отправить</button></p>
</form>
{% endblock %}
```
> [!info]  `{{ form.as_p }} ` один из методов отображения формы - в данном случае, поля выводятся с тегом абзаца `<p>` (можно as_ol, as_table)
### Этап 3
#### Улучшим forms.py
* **label** - заголовок
* **required=False** - можно не вводить
* **initial=True** - по умолчанию отмечено
* **empty_label="Не замужем"** - текст в поле выбора
>[!abstract] sitewomen/women/forms.py
```python
from django import forms
from .models import Category, Husband

# fmt: off
class AddPostForm(forms.Form):
    title = forms.CharField(max_length=255, label="Заголовок")  # текстовое поле
    slug = forms.SlugField(max_length=255, label="URL")  #  слаг
    content = forms.CharField(widget=forms.Textarea(), required=False, label="Контент")  # большое текстовое поле
    is_published = forms.BooleanField(required=False, label="Статус", initial=True)  # чекер
    cat = forms.ModelChoiceField(queryset=Category.objects.all(), label="Категории", empty_label="Категория не выбрана")  # раскрівающееся меню с данными из таблиц
    husband = forms.ModelChoiceField(queryset=Husband.objects.all(), required=False, label="Муж", empty_label="Не замужем")  #
```
[Оф дока](https://docs.djangoproject.com/en/4.2/ref/forms/fields/)
#### Переделаем addpage.html
Можно так выводить, по дибильному, как мы любим)
```django
{% extends 'base.html' %}
{% block content %}
<h1>{{title}}</h1>
<form action="" method="POST">
    {% csrf_token %}
    <div class="form-error">{{ form.non_field_errors }}</div>
    <p><label class="form-label" for="{{ form.title.id_for_label }}">{{ form.title.label }}</label>{{ form.title }}</p>
    <div class="form-error">{{ form.title.errors }}</div>
    <p><label class="form-label" for="{{ form.slug.id_for_label }}">{{ form.slug.label }}</label>{{ form.slug }}</p>
    <div class="form-error">{{ form.slug.errors }}</div>
    <p><label class="form-label" for="{{ form.content.id_for_label }}">{{ form.content.label }}</label>{{ form.content }}</p>
    <div class="form-error">{{ form.content.errors }}</div>
    <p><label class="form-label" for="{{ form.is_published.id_for_label }}">{{ form.is_published.label }}</label>{{ form.is_published }}</p>
    <div class="form-error">{{ form.is_published.errors }}</div>
    <p><label class="form-label" for="{{ form.cat.id_for_label }}">{{ form.cat.label }}</label>{{ form.cat }}</p>
    <div class="form-error">{{ form.cat.errors }}</div>
    <p><label class="form-label" for="{{ form.husband.id_for_label }}">{{ form.husband.label }}</label>{{ form.husband }}</p>
    <div class="form-error">{{ form.husband.errors }}</div>
    <p><button type="submit">Отправить</button></p>
</form>
{% endblock %}
```
>[!info] non_field_errors для отлова не связанных  ошибок с формой 

Либо по нормальному
```django
{% extends 'base.html' %}
{% block content %}
<h1>{{title}}</h1>
<form action="" method="POST">
    {% csrf_token %}
    <div class="form-error">{{ form.non_field_errors }}</div>
    {% for f in form %}
        <p><label class="form-label" for="{{ f.id_for_label }}">{{ f.label }}</label>{{ f }}</p>
        <div class="form-error">{{ f.errors }}</div>
    {% endfor %}
    <p><button type="submit">Отправить</button></p>
</form>
{% endblock %}
```
>[!info] Если нужно прописать для каждой колонки свой css стиль, то его нужно задать в forms.py в атрибутах обьявления колонки формы
```python
from django import forms
from .models import Category, Husband

# fmt: off
class AddPostForm(forms.Form):
    title = forms.CharField(max_length=255, label="Заголовок", widget=forms.TextInput(attrs={'class': 'form-input'}))  # текстовое поле, class: form-input для css
    slug = forms.SlugField(max_length=255, label="URL")  #  слаг
    content = forms.CharField(widget=forms.Textarea(attrs={'cols': 50, 'rows': 5}), required=False, label="Контент")  # большое текстовое поле 50столбцов и 5строк
    is_published = forms.BooleanField(required=False, label="Статус", initial=True)  # чекер
    cat = forms.ModelChoiceField(queryset=Category.objects.all(), label="Категории", empty_label="Категория не выбрана")  # раскрівающееся меню с данными из таблиц
    husband = forms.ModelChoiceField(queryset=Husband.objects.all(), required=False, label="Муж", empty_label="Не замужем")  #
```

Конечній вариант функции представления для формы с обработчиком ошибки и передачей данных в базу
>[!abstract] sitewomen/women/views.py
```python
def addpage(request):
    if request.method == "POST":
        form = AddPostForm(request.POST)
        if form.is_valid():
            try:
                Women.objects.create(**form.cleaned_data)
                return redirect("home")
            except:
                form.add_error(None, "Ошибка добавления поста")
    else:
        form = AddPostForm()

    data = {"menu": menu, "title": "Добавление статьи", "form": form}
    return render(request, "women/addpage.html", data)
```
> [!warning]  Важно, что бы имена атрибутов в классе **AddPostForm** совпадали с именами атрибутов в классе таблици **Women** - иначе експорта не выйдет
### Валидаторы
Помимо стандартных валидаторов типа `min_length=5` у аргументов, так же можно прописывать свои выводы сообщений об ошибках в виде `error_messages` в виде словаря указывая там валидаторы в виде ключей и описание ошибок для их.
Та же дял валидаторов можно использовать заготовленные джангой классы типа **MinLengthValidator** (имеет свой синтакс, так же эти классы можно задавать прямо в моделях таблиц)
[Оф дока](https://docs.djangoproject.com/en/4.2/ref/forms/fields/)
>[!abstract] sitewomen/women/forms.py
```python
from django import forms
from django.core.validators import MaxLengthValidator, MinLengthValidator
from .models import Category, Husband

class AddPostForm(forms.Form):
    title = forms.CharField(
        max_length=255,
        min_length=5,
        label="Заголовок",
        error_messages={
            "min_length": "Слишком короткий заголовок",
            "required": "Без заголовка никак",
        },
        widget=forms.TextInput(attrs={"class": "form-input"}),
    )  # текстовое поле, class: form-input для css
    slug = forms.SlugField(
        max_length=255,
        label="URL",
        validators=[
            MinLengthValidator(5, message="Минимальная длина 5 символов"),
            MaxLengthValidator(100, message="Максимальная длина 100 символов"),
        ],
    )  #  слаг
    content = forms.CharField(
        widget=forms.Textarea(attrs={"cols": 50, "rows": 5}),
        required=False,
        label="Контент",
    )  # большое текстовое поле 50столбцов и 5строк
    is_published = forms.BooleanField(
        required=False, label="Статус", initial=True
    )  # чекер
    cat = forms.ModelChoiceField(
        queryset=Category.objects.all(),
        label="Категории",
        empty_label="Категория не выбрана",
    )  # раскрівающееся меню с данными из таблиц
    husband = forms.ModelChoiceField(
        queryset=Husband.objects.all(),
        required=False,
        label="Муж",
        empty_label="Не замужем",
    )  #
```
#### Свой валидатор
Создадим свой валидатор **RussianValidator** который будет проверять символы на кирилицу и применим его к title. 
```python
from django import forms
from django.core.validators import MaxLengthValidator, MinLengthValidator
from django.utils.deconstruct import deconstructible
from .models import Category, Husband

@deconstructible
class RussianValidator:
    ALLOWED_CHARS = (
        "АБВГДЕЁЖЗИЙКЛМНОПРСТУФХЦЧШЩЬЫЪЭЮЯабвгдеёжзийклмнопрстуфхцчшщбыъэюя0123456789- "
    )
    code = "russian"

    def __init__(self, message=None):
        self.message = (
            message
            if message
            else "Должны присутствовать только русские символы, дефис и пробел."
        )

    def __call__(self, value, *args, **kwargs):
        if not (set(value) <= set(self.ALLOWED_CHARS)):
            raise forms.ValidationError(self.message, code=self.code)

class AddPostForm(forms.Form):
    title = forms.CharField(
        max_length=255,
        min_length=5,
        label="Заголовок",
        validators=[RussianValidator()],
        error_messages={
            "min_length": "Слишком короткий заголовок",
            "required": "Без заголовка никак",
        },
        widget=forms.TextInput(attrs={"class": "form-input"}),
    )  # текстовое поле, class: form-input для css
    slug = forms.SlugField(
        max_length=255,
        label="URL",
        validators=[
            MinLengthValidator(5, message="Минимальная длина 5 символов"),
            MaxLengthValidator(100, message="Максимальная длина 100 символов"),
        ],
    )  #  слаг
    content = forms.CharField(
        widget=forms.Textarea(attrs={"cols": 50, "rows": 5}),
        required=False,
        label="Контент",
    )  # большое текстовое поле 50столбцов и 5строк
    is_published = forms.BooleanField(
        required=False, label="Статус", initial=True
    )  # чекер
    cat = forms.ModelChoiceField(
        queryset=Category.objects.all(),
        label="Категории",
        empty_label="Категория не выбрана",
    )  # раскрівающееся меню с данными из таблиц
    husband = forms.ModelChoiceField(
        queryset=Husband.objects.all(),
        required=False,
        label="Муж",
        empty_label="Не замужем",
    )  #
```
> [!info]  Создание подобных валидаторов имеет смысл при неоднократном использовании в проэкте, иначе лучше использовать метод **clean** в классе формы
```python
from turtle import title
from django import forms
from django.core.validators import MaxLengthValidator, MinLengthValidator
from django.utils.deconstruct import deconstructible
from .models import Category, Husband

class AddPostForm(forms.Form):
    title = forms.CharField(
        max_length=255,
        min_length=5,
        label="Заголовок",
        error_messages={
            "min_length": "Слишком короткий заголовок",
            "required": "Без заголовка никак",
        },
        widget=forms.TextInput(attrs={"class": "form-input"}),
    )  # текстовое поле, class: form-input для css
    slug = forms.SlugField(
        max_length=255,
        label="URL",
        validators=[
            MinLengthValidator(5, message="Минимальная длина 5 символов"),
            MaxLengthValidator(100, message="Максимальная длина 100 символов"),
        ],
    )  #  слаг
    content = forms.CharField(
        widget=forms.Textarea(attrs={"cols": 50, "rows": 5}),
        required=False,
        label="Контент",
    )  # большое текстовое поле 50столбцов и 5строк
    is_published = forms.BooleanField(
        required=False, label="Статус", initial=True
    )  # чекер
    cat = forms.ModelChoiceField(
        queryset=Category.objects.all(),
        label="Категории",
        empty_label="Категория не выбрана",
    )  # раскрівающееся меню с данными из таблиц
    husband = forms.ModelChoiceField(
        queryset=Husband.objects.all(),
        required=False,
        label="Муж",
        empty_label="Не замужем",
    )  #

	def clean_title(self):
        title = self.cleaned_data["title"]
        ALLOWED_CHARS = "АБВГДЕЁЖЗИЙКЛМНОПРСТУФХЦЧШЩЬЫЪЭЮЯабвгдеёжзийклмнопрстуфхцчшщбыъэюя0123456789-"
        if not (set(title) <= set(ALLOWED_CHARS)):
            raise forms.ValidationError(
                "Должны присутствовать только русские символы, дефис и пробел."
            )
```
## Формы связанные с моделью
Выведем форму связанную с таблицей Women. Будут выведены все поля которые не заполняются автоматически
* `model = Women` подключаемая модель
* `fields = "__all__"` вывести все поля
```python
from django import forms
from .models import Women

class AddPostForm(forms.ModelForm):

	class Meta:
		model = Women
	    fields = "__all__"
```
> [!info]  В поле fields можно прописывать в словаре только нужные поля
### Нормальный пример
>[!abstract] sitewomen>women>forms.py 
```python
from django import forms
from django.forms import ValidationError, widgets
from .models import Category, Husband, Women

class AddPostForm(forms.ModelForm):
    # fmt: off
    cat = forms.ModelChoiceField(queryset=Category.objects.all(), label="Категории", empty_label="Категория не выбрана",)
    husband = forms.ModelChoiceField(queryset=Husband.objects.all(), required=False, label="Муж", empty_label="Не замужем",)

    class Meta:
        model = Women
        fields = ['title', 'slug', 'content', 'is_published', 'cat', 'husband', 'tags']
        widgets = {
            'title': forms.TextInput(attrs={'class': 'form-input'}),
            'content': forms.Textarea(attrs={'cols':50, 'rows': 5}),
        }  # свои класы css
        labels = {'slug': 'URL'}  # rename slug 
	
	# свой валидатор проверит на длину
    def clean_title(self):
        title = self.cleaned_data["title"]
        if len(title) > 50:
            raise ValidationError("Длина превышает 50 символов!")
        return title
```
Теперь у нас есть возможность использовать метод save() без дополнительного подключения к таблице Women
>[!abstract] sitewomen>women>views.py 
```python
def addpage(request):
    if request.method == "POST":
        form = AddPostForm(request.POST)
        if form.is_valid():
	        # try:
            #     Women.objects.create(**form.cleaned_data)
            #     return redirect("home")
            # except:
            #     form.add_error(None, "Ошибка добавления поста")
            form.save()
            return redirect("home")
    else:
        form = AddPostForm()

    data = {"menu": menu, "title": "Добавление статьи", "form": form}
    return render(request, "women/addpage.html", data)
```
## Загрузка файлов 
Загрузка файлов на сервер обрабатывается та же формами. Самый базовый пример
>[!abstract] sitewomen/women/templates/women/about.html
```django
{% extends 'base.html' %}

{% block content %}
<h1>{{title}}</h1>
<form action="" method="post" enctype="multipart/form-data">
    {% csrf_token %}
    <p><input type="file" name="file_upload"></p>
    <p><button type="submit">Отправить</button></p>
</form>
{% endblock %}
```
Где **`<form>`**:
- **`action=""`**: Указывает URL, на который будет отправлена форма. Если оставить пустым, форма отправится на текущий URL.
- **`method="post"`**: Указывает, что данные формы будут отправлены методом POST.
- **`enctype="multipart/form-data"`**: Этот атрибут обязателен для форм, которые загружают файлы. Без него файлы не будут переданы на сервер.

Функция представления
>[!abstract] sitewomen/women/templates/women/views.html
```python
def handle_uploaded_file(f):
    with open(f"sitewomen/uploads/{f.name}", "wb+") as destination:
        for chunk in f.chunks():
            destination.write(chunk)

def about(request):
    if request.method == "POST":
        handle_uploaded_file(request.FILES["file_upload"])
    return render(request, "women/about.html", {"title": "О сайте", "menu": menu})
```
###
###