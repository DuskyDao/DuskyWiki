#### Структура проєкта для фрифтов
```
project/
│
├── fonts/
│   ├── MyCustomFont-Regular.woff2
│   ├── MyCustomFont-Regular.woff
│
├── css/
│   ├── fonts.css
│
├── index.html

```
#### Качаем шрифты
[webfonts.pro](https://webfonts.pro)
В архиве обычно есть так же файл stylesheet
#### Выбираем нужные
Для примера возмем шрифт **Roboto** и три его разновидности
* **Roboto-Light** что соответствует жирности 300 (`font-weight: 300;`)
* **Roboto-Medium** что соответствует жирности 500 (`font-weight: 500;`)
* **Roboto-Black** что соответствует жирности 900 (`font-weight: 900;`)

Выбираем формат **.woff**, так как он занимает меньше всего места на диске и будет быстрее загружаться на нашем сайте.
Кидаем их в папку стилей **fonts**
#### Подключаем
##### Создадим в папке стилей файл *fonts.css* и пропишем пути и правила к шрифтам
```css
@font-face {
    font-family: 'Roboto';
    src: local('Roboto Medium'), local('Roboto-Medium'),
        url('../fonts/Roboto-Medium.woff') format('woff');
    font-weight: 500;
    font-style: normal;
}

@font-face {
    font-family: 'Roboto';
    src: local('Roboto Black'), local('Roboto-Black'),
        url('../fonts/Roboto-Medium.woff') format('woff');
    font-weight: 900;
    font-style: normal;
}

@font-face {
    font-family: 'Roboto';
    src: local('Roboto Light'), local('Roboto-Light'),
        url('../fonts/Roboto-Light.woff') format('woff');
    font-weight: 300;
    font-style: normal;
}
```
##### Подключение файла `fonts.css` в HTML
Включите файл со шрифтами в ваш HTML:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="css/fonts.css">
    <title>Шрифты</title>
</head>
<body>
    <h1 class="custom-heading">Пример текста с локальным шрифтом</h1>
    <p>Этот текст использует Google Fonts.</p>
</body>
</html>

```
##### Ещё пример fonts.css
```css
/* 1. Подключение шрифтов с помощью @import */
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

/* 2. Подключение локального шрифта через @font-face */
@font-face {
    font-family: 'MyCustomFont';
    src: url('fonts/MyCustomFont-Regular.woff2') format('woff2'),
         url('fonts/MyCustomFont-Regular.woff') format('woff');
    font-weight: 400;
    font-style: normal;
}

/* 3. Настройка шрифтов для тега body */
body {
    font-family: 'Roboto', Arial, sans-serif; /* Используем Google Fonts */
    font-size: 16px;
    line-height: 1.5;
    color: #333;
}

/* 4. Применение локального шрифта */
.custom-heading {
    font-family: 'MyCustomFont', serif;
    font-weight: 400;
    font-size: 24px;
}
```
#### Конвертер шрифтов
https://transfonter.org/